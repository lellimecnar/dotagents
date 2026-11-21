# Importing recipes from documents into a recipe manager

Use when recipes from a PDF, a printed plan or a web page must go into the household's recipe manager (for example
a self-hosted recipe app). The goal is recipes that cook correctly from the manager without anyone
re-checking the source. A wrong unit or a missing ingredient is found on cook day, when it is too late to fix.
For a single recipe someone typed in by hand, steps 1 and 4 shrink to a glance; the parser check still applies.

## 1. Read the real source
- For a PDF, render the pages as images and read recipe names and ingredient lists from them. A text dump loses
  column order, so titles and ingredients get paired wrongly.
- Tell recipe pages apart from the other sheets a plan may bundle (for example shopping, prep, cook-day, thaw or label sheets). Label or sticker pages
  repeat ingredients out of alignment: build from the recipe cards only, pairing each title with its own card
  (not with whatever sits next to it in the text).
- Preserve the yield and any distinctions the source makes, such as ingredients used at different
  stages. Keep ingredients added at serving out of any make-ahead or freezing instructions.
- Verify what embedded links actually point to. Use an authorized export, recipe page or supplied
  document, depending on what the source supports; ask only if the intended source remains unclear.

## 2. Preview the parse before importing
- Run the ingredient lines of one recipe through the manager's parser in a mode that saves nothing (a dry run or
  preview), and show the requester a table: quantity, unit, food, note, and anything that would be newly created.
  Flag every wrong line. If no preview exists, test in a disposable destination if available; otherwise use a live pilot only with authorization to write and clean up its side effects. Verify the pilot before proceeding with the batch.
- Check every batch for the usual failures:
  - tablespoon parsed as teaspoon (a threefold error) or other unit conversions gone wrong;
  - compound phrases turned into a bogus unit ("minced garlic cloves" becoming the unit "minced");
  - weight ounces vs fluid ounces;
  - descriptive words ("whole", "fresh", "large") absorbed into the food name, which creates duplicate foods.
- Spot-check suspicious quantities against the page image. Keep printed oddities as flags in the recipe note, not
  silent corrections: the source may be right, and a silent fix hides the question.

## 3. Tag for grouping
- Give every recipe the shared source and category tags (source name, meal type, cooking method) plus one batch
  tag unique to this plan or volume, so a later batch can be imported and grouped without touching this one.
- Reuse the tag convention from earlier imports of the same source instead of inventing new tags; check the
  earlier spec or the existing tags first.
- Where the manager can group recipes by tag (a collection or cookbook filtered on the batch tag), set that up or
  say who has to. Put the source link in each recipe description so the original stays one click away.

## 4. Cross-check counts
- Reconcile the unique recipes in scope with the import and, if available, a source list that
  enumerates recipes. Explain duplicates, excluded items and any unmatched recipe before reporting done.
- A recipe that appears in two batches is imported once, with both batch tags. Same-named but different recipes
  (two cooking methods for one dish) stay separate.

## 5. Confirm before reporting done
- If a helper does the writes, share only the import details they are authorized to receive through
  an approved channel they can access. Coordinate overlapping imports or use the manager's
  deduplication support so retries and concurrent writers do not create duplicates.
- Preparing a spec is not an import. Report nothing as imported until the writer confirms, with counts you can
  compare against step 4; if you did the writes yourself, re-read the imported recipes rather than trusting the
  tool's success message.

## 6. Say up front what the manager cannot do
Name the limits before the requester plans around them: for example no merging of several recipes into one
cook-day sequence (many managers only sum ingredients on a shared shopping list), or tool-level limits on creating
collections, attaching files or setting some fields. Offer the workaround (a cook-day order in the plan itself,
the source link in the description) in the same message.
