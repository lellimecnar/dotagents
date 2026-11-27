# Commands for the git and GitHub workflow

Exact commands for each step of the standard in `SKILL.md`. Replace `<owner>/<repo>`, `<base>`
(the integration branch) and numbers. `gh` is the GitHub CLI; every `gh api` call also works as a
plain REST call with a token. Build any JSON or markdown body with a file (`--input file.json`,
`--body-file body.md`), never with inline-quoted strings.

## Contents

1. Read the repository's settings
2. Start a branch
3. Commit
4. Shape history
5. Update from the base and push a rewrite
6. Open and maintain the PR
7. Answer a review
8. Stacked PRs
9. After the merge

## 1. Read the repository's settings

```bash
# Default branch and allowed merge methods (merge-method fields need write access to show).
gh api repos/<owner>/<repo> --jq '{default_branch, allow_merge_commit, allow_squash_merge, allow_rebase_merge, delete_branch_on_merge}'

# Every rule that applies to the target branch (rulesets): reviews, last-push approval,
# thread resolution, allowed merge methods, required checks, linear history.
gh api repos/<owner>/<repo>/rules/branches/<base> --jq '.[] | {type, parameters}'

# Classic branch protection, if the repository still uses it (404 when none or no access).
gh api repos/<owner>/<repo>/branches/<base>/protection

# Project conventions on disk.
ls AGENTS.md CLAUDE.md CONTRIBUTING.md .github/pull_request_template.md .github/PULL_REQUEST_TEMPLATE* \
   commitlint.config.* .commitlintrc* .husky lefthook.yml .pre-commit-config.yaml .mergify.yml 2>/dev/null
```

If you cannot read the settings, read how recent PRs were merged instead: merge commits on the base
(`git log --merges --oneline -10 origin/<base>`) mean commits land as they are; one commit per PR
titled `... (#123)` with no merge commits usually means squash.

## 2. Start a branch

```bash
git fetch --prune origin
# Is there already a PR or branch for this goal?
gh pr list --repo <owner>/<repo> --state open --search "<keywords>"
git branch -r --list 'origin/*<keyword>*'

# New branch from the fresh integration branch, in its own worktree.
git worktree add -b fix/search-empty-query-crash ../wt-search-fix origin/<base>

# Continue an existing PR instead.
gh pr checkout <n>        # or: git fetch origin pull/<n>/head && git switch -c <its-branch> FETCH_HEAD

# Make sure the name is not already a tag.
git show-ref --tags --verify --quiet refs/tags/<name> && echo "taken by a tag"
```

## 3. Commit

```bash
git status --short
git add -p <path>...                 # stage deliberately, hunk by hunk where useful
git diff --cached                    # read what you are about to commit
git diff --cached | grep -nE 'gh[pousr]_[A-Za-z0-9]{20,}|sk-[A-Za-z0-9]{20,}|AKIA[0-9A-Z]{16}|BEGIN [A-Z ]*PRIVATE KEY|xox[baprs]-'   # must print nothing
git commit -F msg.txt                # message from a file: subject, blank line, body, footers
```

Split a commit that grew two concerns:

```bash
git reset --soft HEAD~1              # undo the commit, keep the changes staged
git restore --staged .               # unstage everything
git add -p <paths for concern 1> && git commit -F msg1.txt
git add -p <paths for concern 2> && git commit -F msg2.txt
```

## 4. Shape history

```bash
# Record a correction to an earlier commit.
git commit --fixup=<sha-of-commit-being-fixed>

# Fold all fixups into their targets before review (non-interactive: the todo list is accepted as generated).
OLD=$(git rev-parse HEAD)
GIT_SEQUENCE_EDITOR=: git rebase -i --autosquash origin/<base>

# Prove the rewrite changed only what you meant.
git range-diff origin/<base> $OLD HEAD

# Reword, reorder or drop commits: edit the todo list with a script rather than an editor, e.g.
GIT_SEQUENCE_EDITOR="sed -i -e 's/^pick 1a2b3c4/reword 1a2b3c4/'" git rebase -i origin/<base>
```

`git rebase -i` needs a sequence editor; set `GIT_SEQUENCE_EDITOR` (and `GIT_EDITOR` for rewords)
so it never waits for an interactive editor that is not there.

## 5. Update from the base and push a rewrite

```bash
OLD=$(git rev-parse HEAD); OLD_BASE=$(git merge-base HEAD origin/<base>)   # before fetching
git fetch origin
git rebase origin/<base>             # your own branch only
# conflicts: edit each file hunk by hunk, then
git add <file> && git rebase --continue
# (abort the whole attempt with: git rebase --abort)

git range-diff $OLD_BASE..$OLD origin/<base>..HEAD   # your commits before vs after; base changes excluded
<run the repository's checks again>

# Lease on the exact head you expect to replace (the PR's current head), works whatever the local name is:
EXPECT=$(gh pr view <n> --repo <owner>/<repo> --json headRefOid -q .headRefOid)   # or the SHA you last pushed
git push --force-with-lease=<remote-branch>:$EXPECT origin HEAD:<remote-branch>
# Shortcut only when the local branch has the same name as the remote one:
git push --force-with-lease --force-if-includes origin HEAD
```

Tested behaviour (git 2.47): the named-SHA lease refuses when another author's commit reached the
branch, even after a background fetch; a bare `--force-with-lease` after a background fetch
overwrites it; `--force-if-includes` refuses a legitimate rewrite pushed as `HEAD:<other-name>`.

Merging the base instead (branch shared with others, or the repository asks for it):

```bash
git fetch origin && git merge --no-edit origin/<base> && git push
```

Rewrite notice for a PR that already has review activity (`notice.md`):

```text
Rewrote the branch: <one line on why, e.g. "rebased onto <base> to resolve a conflict in api/search.py; no other changes">.
Old head <old-sha>, new head <new-sha>. Compare: git range-diff <old-base-sha>..<old-sha> <new-base-sha>..<new-sha>
```

```bash
gh pr comment <n> --repo <owner>/<repo> --body-file notice.md
```

## 6. Open and maintain the PR

```bash
git push -u origin HEAD                                   # or HEAD:<type>/<desc> to rename at push time
gh pr create --repo <owner>/<repo> --base <base> --head <branch> \
  --title "fix(search): reject empty queries before hitting the index" --body-file pr.md [--draft]
gh pr view <n> --repo <owner>/<repo> --json baseRefName,headRefName,headRefOid,isDraft,url

gh pr edit <n> --repo <owner>/<repo> --body-file pr.md    # keep the description current
gh pr ready <n> --repo <owner>/<repo>                     # draft -> ready for review
gh pr edit <n> --repo <owner>/<repo> --add-reviewer <login>
gh pr checks <n> --repo <owner>/<repo> --watch            # bounded wait on CI
```

Description skeleton (`pr.md`) when the repository has no template:

```markdown
## Why
<problem or request, 2-4 lines, with link; quote the requester where their words define done>

## What changed
- <area>: <change>

## How it was verified
- `<command>`: <result>
- <pages or behaviour looked at; screenshots>

## Risk and rollback
<what could break; how to back it out>

## Decisions to confirm
A. <decision> (default: <your default>)

## Blockers
<only genuine ones, with evidence; delete the section when none>

Refs #<issue>   <!-- "Closes #<issue>" only when the base is the default branch -->
```

## 7. Answer a review

```bash
# List review threads with ids, state and first comment (GraphQL is the only API that shows resolution).
gh api graphql -f query='
query($o:String!,$r:String!,$n:Int!){repository(owner:$o,name:$r){pullRequest(number:$n){
  reviewThreads(first:100){nodes{id isResolved isOutdated path line
    comments(first:1){nodes{databaseId author{login} body}}}}}}}' \
  -f o=<owner> -f r=<repo> -F n=<n>

# Reply inside a thread: comment_id is the thread's first comment (replies to replies are not supported).
gh api --method POST repos/<owner>/<repo>/pulls/<n>/comments/<comment_id>/replies -f body="Fixed in <sha>: <what changed>."

# Resolve a thread (only your own, or where the repository's convention gives it to you).
gh api graphql -f query='mutation($id:ID!){resolveReviewThread(input:{threadId:$id}){thread{isResolved}}}' -f id=<thread-node-id>

# Re-request review from the same reviewer after pushing the round.
gh api --method POST repos/<owner>/<repo>/pulls/<n>/requested_reviewers -f 'reviewers[]=<login>'
```

A reviewer's suggested change, written in a review comment so the author can apply it in one click:

````markdown
```suggestion
if not query.strip():
    raise EmptyQueryError()
```
````

Applying several suggestions locally in one commit (when the web "batch" is not available): make
the edits, then `git commit -m "fix(search): apply review suggestions" --trailer "Co-authored-by: <Name> <email>"`
for each suggester whose text you took.

## 8. Stacked PRs

```bash
# Child branch on top of the parent branch; the child PR targets the parent branch.
git worktree add -b feat/export-csv-ui ../wt-export-ui origin/feat/export-csv-api
gh pr create --base feat/export-csv-api --head feat/export-csv-ui --title "..." --body-file pr.md

# After the parent merges: move the child onto the integration branch and retarget its PR.
git fetch origin
EXPECT=$(git rev-parse origin/feat/export-csv-ui)
git rebase --onto origin/<base> <last-parent-commit-sha> feat/export-csv-ui
git push --force-with-lease=feat/export-csv-ui:$EXPECT origin feat/export-csv-ui
gh pr edit <child-n> --base <base>
```

`<last-parent-commit-sha>` is the parent branch's head as the child knew it (for a merge-commit
merge, the parent PR's head SHA). Check the child PR now lists only its own commits.

## 9. After the merge

```bash
gh pr view <n> --repo <owner>/<repo> --json state,mergedAt,mergeCommit,headRefOid
git fetch origin && git merge-base --is-ancestor <reviewed-head-sha> origin/<base> && echo landed

git worktree remove ../wt-search-fix
git branch -d fix/search-empty-query-crash                 # local; -d refuses if unmerged
git push origin --delete fix/search-empty-query-crash      # only your own branch, only if not auto-deleted

# Undo a merged PR through a new PR.
git switch -c revert/search-empty-query origin/<base>
git revert -m 1 <merge-commit-sha>                         # plain `git revert <sha>` for a squash commit
```
