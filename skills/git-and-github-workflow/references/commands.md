# Commands for the git and GitHub workflow

Exact commands for each step of the standard in `SKILL.md`. Shell variables stand for values you
fill in: `$repo` (owner/name), `$remote` (usually `origin`), `$base` (the integration branch),
`$branch` (your head branch, without `refs/heads/`), `$pr` (PR number). `gh api` calls use GitHub's
REST API unless the call says `graphql`. Build every JSON or markdown body in a file (`--input`,
`--body-file`, `-F body=@file`), never as an inline-quoted string. The examples assume the PR's head
branch lives in the same repository as its base.

## Contents

1. Read the repository's settings
2. Start or continue a branch
3. Commit and scan
4. Shape history
5. Update from the base and push a rewrite
6. Open and maintain the PR
7. Answer a review
8. Stacked PRs
9. After the merge

## 1. Read the repository's settings

```bash
# Default branch and merge settings (merge-method fields only appear with write access; missing = unknown).
gh api "repos/$repo" --jq '{default_branch, allow_merge_commit, allow_squash_merge, allow_rebase_merge, delete_branch_on_merge, squash_merge_commit_title, squash_merge_commit_message}'

# Rulesets and classic protection, for the target branch and (before a rewrite or deletion) your head
# branch. Encode each name as one path segment (slashes too) and page. A 403/404 you cannot explain
# means unknown, not "no rule".
for b in "$base" "$branch"; do
  enc=$(python3 -c 'import sys,urllib.parse;print(urllib.parse.quote(sys.argv[1],safe=""))' "$b")
  echo "== $b"
  gh api --paginate "repos/$repo/rules/branches/$enc" --jq '.[] | {type, parameters}'
  gh api "repos/$repo/branches/$enc/protection"
done

# Who you are acting as.
gh api user --jq .login; git config user.name; git config user.email

# Project conventions on disk (instructions, template, commit checks, hooks, queue config).
ls AGENTS.md CLAUDE.md CONTRIBUTING.md .github/pull_request_template.md .github/PULL_REQUEST_TEMPLATE* 2>/dev/null
git ls-files | grep -iE 'commitlint|lefthook|pre-commit|husky|mergify|merge.?queue' | head
```

A hint, not proof, of the merge method: merge commits on the base
(`git log --merges --oneline -10 "$remote/$base"`) suggest commits land as they are; single commits
titled `... (#123)` with no merges suggest squash. Use it only to decide how to shape commits.

## 2. Start or continue a branch

```bash
git fetch --prune "$remote"
gh pr list --repo "$repo" --state open --search "<keywords>"          # same goal already open?

# New branch: the exact remote ref must not exist, and the name must not be a tag.
git ls-remote --exit-code --heads "$remote" "refs/heads/$branch" && echo "branch exists: stop"
git show-ref --verify --quiet "refs/tags/$branch" && echo "tag has this name: pick another"
git check-ref-format --branch "$branch" >/dev/null || echo "invalid name"
git worktree add -b "$branch" "../wt-$(echo "$branch" | tr / -)" "$remote/$base"

# Continue an existing PR branch: is it checked out anywhere else?
git worktree list --porcelain | grep -B2 "branch refs/heads/$branch"   # output = someone owns it: coordinate
git fetch "$remote" "refs/heads/$branch:refs/remotes/$remote/$branch"
if git show-ref --verify --quiet "refs/heads/$branch"; then
  git worktree add "../wt-$(echo "$branch" | tr / -)" "$branch"        # attach the existing local branch
  git -C "../wt-$(echo "$branch" | tr / -)" log --oneline --left-right "$branch...$remote/$branch"   # divergence? stop and reconcile
else
  git worktree add -b "$branch" "../wt-$(echo "$branch" | tr / -)" "$remote/$branch"
fi
# Read-only look at a PR without taking its branch:
git fetch "$remote" "pull/$pr/head" && git worktree add --detach ../wt-pr-$pr FETCH_HEAD
```

If a tool already gave you a worktree on a generated local branch, keep that local name. To continue
an existing PR there, move it to the PR's tip only when that loses nothing: the worktree is clean and
its `HEAD` is an ancestor of the verified PR tip.

```bash
[ -z "$(git status --porcelain)" ] && git merge-base --is-ancestor HEAD "$remote/$branch" \
  && git reset --hard "$remote/$branch" || echo "local work would be lost: stop"
```

Then push with `HEAD:refs/heads/$branch`.

## 3. Commit and scan

```bash
git status --short
git add -- path/one path/two          # reviewed whole paths; `git add -p` only in an interactive session
git diff --cached --stat && git diff --cached
git commit -F msg.txt                 # subject, blank line, body, footers

# Secret scan of every outgoing commit, printing only commit:path locations, never matched text.
PAT='gh[pousr]_[A-Za-z0-9]{20,}|github_pat_[A-Za-z0-9_]{20,}|sk-[A-Za-z0-9]{20,}|AKIA[0-9A-Z]{16}|BEGIN [A-Z ]*PRIVATE KEY|xox[baprs]-'
cd "$(git rev-parse --show-toplevel)"
REVS=$(git rev-list "$remote/$base..HEAD") || { echo "cannot list outgoing commits: do not push"; exit 1; }
if [ -z "$REVS" ]; then echo "no outgoing commits"
else git grep -lE "$PAT" $REVS -- . ; rc=$?
  [ $rc -eq 1 ] && echo "clean for these patterns"; [ $rc -ge 2 ] && echo "scan failed: do not push"; fi
# rc 0 = matches, listed as commit:path only. Prefer the project's own redacting scanner when it has one.
```

Split the newest commit when it grew two concerns (only an unpushed, non-merge commit):

```bash
git status --porcelain                # must be empty
OLD=$(git rev-parse HEAD)             # recovery point
git reset --soft HEAD~1 && git restore --staged .
git add -- <paths for concern 1> && git commit -F msg1.txt
git add -- <paths for concern 2> && git commit -F msg2.txt
git diff --quiet "$OLD" HEAD && echo "same tree as before the split"
```

## 4. Shape history

```bash
git commit --fixup=<sha-of-commit-being-fixed>        # while working

git status --porcelain                                # must be empty before a rewrite
OLD=$(git rev-parse HEAD)
GIT_SEQUENCE_EDITOR=: git rebase -i --autosquash --no-update-refs "$remote/$base"
git range-diff "$remote/$base" "$OLD" HEAD            # orient: which commits changed
git diff --quiet "$OLD" HEAD && echo "trees identical: history-only rewrite"

# Reword or drop by editing the todo list with a script, never an interactive editor:
GIT_SEQUENCE_EDITOR="sed -i -e 's/^pick 1a2b3c4/drop 1a2b3c4/'" git rebase -i --no-update-refs "$remote/$base"
```

`--no-update-refs` stops the rebase from moving other local branches that point into the series.
A rebase that stops on a conflict: resolve, `git add -- <file>`, then
`GIT_EDITOR=: git rebase --continue` (keeps the existing message). Unsure about a resolution:
`git rebase --abort`.

## 5. Update from the base and push a rewrite

```bash
git status --porcelain                                # must be empty
EXPECT=$(git rev-parse HEAD)          # the head you last pushed and verified
# Destination check: one push URL, pointing at the PR's head repository; the PR's head ref and SHA
# equal your branch and EXPECT. Compare, never copy the live SHA into EXPECT. Stop on any mismatch.
git remote get-url --push --all "$remote"
gh pr view "$pr" --repo "$repo" --json headRefName,headRepositoryOwner,headRepository,headRefOid
OLD_BASE=$(git merge-base HEAD "$remote/$base")
git fetch "$remote"
git rebase --no-update-refs "$remote/$base"           # your own branch only
git range-diff "$OLD_BASE..$EXPECT" "$remote/$base..HEAD"
<run the repository's checks again>

git push --force-with-lease="refs/heads/$branch:$EXPECT" "$remote" "HEAD:refs/heads/$branch"
[ "$(git ls-remote "$remote" "refs/heads/$branch" | cut -f1)" = "$(git rev-parse HEAD)" ] \
  && echo "remote head is the new SHA" || echo "remote head differs: investigate"
```

If the lease is refused, someone else's commit reached the branch: fetch it, read it, and integrate
or coordinate. Do not re-read the remote SHA into `EXPECT` and retry.

First push of a new branch (refuses if the ref already exists, so you cannot update someone else's):

```bash
git push --force-with-lease="refs/heads/$branch:" "$remote" "HEAD:refs/heads/$branch"
```

Later ordinary pushes: `git push "$remote" "HEAD:refs/heads/$branch"`.

Merging the base instead (shared branch, or the repository asks for it):

```bash
git fetch "$remote" && git merge --no-edit "$remote/$base" && git push "$remote" "HEAD:refs/heads/$branch"
```

Rewrite notice for a PR with review activity (`notice.md`), posted with
`gh pr comment "$pr" --repo "$repo" --body-file notice.md`:

```text
Rewrote the branch: <why, e.g. "rebased onto <base> to resolve a conflict in api/search.py; no other changes">.
Old head <old-sha> (base <old-base-sha>), new head <new-sha> (base <new-base-sha>).
Orientation: git range-diff <old-base-sha>..<old-sha> <new-base-sha>..<new-sha>
```

## 6. Open and maintain the PR

```bash
gh pr create --repo "$repo" --base "$base" --head "$branch" --title "<commit-style subject>" --body-file pr.md [--draft]
gh pr view "$pr" --repo "$repo" --json baseRefName,headRefName,headRefOid,isDraft,url
gh pr edit "$pr" --repo "$repo" --body-file pr.md     # keep the description current
gh pr ready "$pr" --repo "$repo"                      # draft -> ready
gh pr edit "$pr" --repo "$repo" --add-reviewer "<login>"

# CI with a deadline (exit 124 = still pending; report it as pending, not passed).
timeout 2700 gh pr checks "$pr" --repo "$repo" --watch
```

Example description (`pr.md`) when the repository has no template. Example only: SKILL.md section 7
owns the contract.

```markdown
## Why
<problem or request, linked>

## What changed
- <area>: <change>

## How it was verified
- `<command>`: <result>

## Risk and rollback
<what could break; how to back it out>

Refs #<issue>
```

## 7. Answer a review

```bash
# Every review thread with state, paged (GraphQL is the only API that exposes resolution).
gh api graphql --paginate -f owner="${repo%/*}" -f name="${repo#*/}" -F pr="$pr" -f query='
query($owner:String!,$name:String!,$pr:Int!,$endCursor:String){repository(owner:$owner,name:$name){pullRequest(number:$pr){
  reviewThreads(first:100,after:$endCursor){pageInfo{hasNextPage endCursor}
    nodes{id isResolved isOutdated path line
      comments(first:50){nodes{databaseId author{login} body}}}}}}}' \
  --jq '.data.repository.pullRequest.reviewThreads.nodes[]'

# Before replying, check for an existing reply of yours (all review comments, paged to the end).
gh api --paginate "repos/$repo/pulls/$pr/comments" \
  --jq '.[] | select(.in_reply_to_id == <first-comment-databaseId>) | {id, user: .user.login, body}'

# Reply in a thread: the REST id of the thread's first comment (replies to replies are rejected).
gh api --method POST "repos/$repo/pulls/$pr/comments/<first-comment-databaseId>/replies" -F body=@reply.md

# Resolve a thread: the GraphQL thread node id (only your own, or where project policy says so).
gh api graphql -f id=<thread-node-id> -f query='mutation($id:ID!){resolveReviewThread(input:{threadId:$id}){thread{isResolved}}}'

# Re-request review after the round, then confirm it registered.
gh pr edit "$pr" --repo "$repo" --add-reviewer "<login>"
gh pr view "$pr" --repo "$repo" --json reviewRequests,reviewDecision,latestReviews
```

A reviewer's suggested change, inside an inline review comment:

````markdown
```suggestion
if not query.strip():
    raise EmptyQueryError()
```
````

Applying several suggestions locally in one commit: make the edits, then
`git commit -F msg.txt --trailer "Co-authored-by: <Name> <email>"` for each suggester whose text you
took.

## 8. Stacked PRs

```bash
# Record the cut point first, build the child at exactly that commit, and work inside its worktree.
git fetch "$remote"
CUT=$(git rev-parse "$remote/$parent")                # the parent state the child is built on; keep it
git worktree add -b "$child" "../wt-$(echo "$child" | tr / -)" "$CUT"
cd "../wt-$(echo "$child" | tr / -)"
<commit>
git push --force-with-lease="refs/heads/$child:" "$remote" "HEAD:refs/heads/$child"
gh pr create --repo "$repo" --base "$parent" --head "$child" --title "..." --body-file pr.md

# After the parent lands (run in the child's worktree): would the child show foreign commits on the base?
gh pr view "$child_pr" --repo "$repo" --json baseRefName,headRefOid
git fetch "$remote"
EXPECT=$(git rev-parse HEAD)                          # the child head you last pushed; must equal headRefOid
git log --oneline "$remote/$base..$EXPECT"            # only child commits listed = no rewrite needed
# No foreign commits (the parent landed as a merge commit): only retarget.
gh pr edit "$child_pr" --repo "$repo" --base "$base"

# Foreign commits listed (the parent was squashed or rebased): move only the child's own commits.
git merge-base --is-ancestor "$CUT" "$EXPECT" && git log --oneline "$CUT..$EXPECT"   # must list only child work
git rebase --no-update-refs --onto "$remote/$base" "$CUT"
git range-diff "$CUT..$EXPECT" "$remote/$base..HEAD"
<run the checks>
git push --force-with-lease="refs/heads/$child:$EXPECT" "$remote" "HEAD:refs/heads/$child"
gh pr edit "$child_pr" --repo "$repo" --base "$base"

# After either path: base, exact head and child-only commits.
gh pr view "$child_pr" --repo "$repo" --json baseRefName,headRefOid,commits \
  --jq '{baseRefName, headRefOid, commits: [.commits[].messageHeadline]}'
git diff --stat "$remote/$base...HEAD"                # only the child's files
```

## 9. After the merge

```bash
gh pr view "$pr" --repo "$repo" --json state,mergedAt,mergeCommit,headRefOid
git fetch "$remote"
# Merge-commit landing: the reviewed head is on the base.
git merge-base --is-ancestor <reviewed-head-sha> "$remote/$base" && echo landed
# Squash or rebase landing: the recorded landed tip is on the base, and the landed range carries the
# reviewed change. N = 1 for squash; for rebase merging, N = the number of PR commits.
M=$(gh pr view "$pr" --repo "$repo" --json mergeCommit --jq .mergeCommit.oid)
git merge-base --is-ancestor "$M" "$remote/$base" || echo "landed tip not on base: do not claim landing"
H=<reviewed-head-sha>; N=1
A=$(git diff "$M~$N" "$M" | git patch-id --stable | cut -d' ' -f1)
B=$(git diff "$(git merge-base "$H" "$M~$N")" "$H" | git patch-id --stable | cut -d' ' -f1)
[ "$A" = "$B" ] && echo "landed change equals reviewed change" || git diff --stat "$M~$N" "$M"   # differs: read the full diff
# If the landed range cannot be established, do not claim landing and do not revert.

# Cleanup, only after landing (or an authorized discard), with nothing else based on the branch.
# Both queries must succeed and print nothing; a failed query stops the cleanup.
gh pr list --repo "$repo" --state open --base "$branch" --json number --jq '.[].number' || exit 1
gh pr list --repo "$repo" --state open --head "$branch" --json number --jq '.[].number' || exit 1
TIP=$(git ls-remote "$remote" "refs/heads/$branch" | cut -f1)   # empty = already deleted
[ "$TIP" = "<sha you cleared>" ] && git push --force-with-lease="refs/heads/$branch:$TIP" "$remote" ":refs/heads/$branch"
git ls-remote --exit-code --heads "$remote" "refs/heads/$branch"; rc=$?
# rc 2 = gone; rc 0 = still there; anything else = the check failed (do not continue)
git worktree remove "../wt-..."                       # your own clean worktree only
git branch -d "$branch"                               # refuses if unmerged: keep it and investigate

# Undo a landed change through a new PR.
git worktree add -b "revert/<slug>" ../wt-revert "$remote/$base" && cd ../wt-revert
git log -1 --format='%P' <merge-commit-sha>           # parent 1 must be the base's previous tip
git revert --no-edit -m 1 <merge-commit-sha>          # merge commit
git revert --no-edit <squash-commit-sha>              # squash commit
git revert --no-edit <oldest-sha>^..<newest-sha>      # a rebase-merged range
```
