# Query

Answer a question from the wiki, then file new findings back.

Input: a question passed as the argument after `query`.

## Steps

1. **Read `index.md` first.** It's the content-oriented catalog with a one-line summary per page. Pick the 3–5 entries that look most relevant *before* opening any page.

2. **Read** those pages. Follow `[[wikilinks]]` one hop to gather adjacent context. Stop at two hops unless the question demands deeper traversal — context bloat is the enemy. Grep `pages/` directly only if `index.md` doesn't yield obvious hits.

3. **Answer the user** from what you read. Cite the pages you used (and the `refs/` records behind them when the user is verifying a fact). If the wiki is silent on the question, say so plainly — do not fall back to training knowledge.

4. **Refile** what's worth keeping back into the wiki. Three valid paths:

   - **Existing source revealed more than the wiki captured.** Re-run [ingest](ingest.md) using the source content from `refs/<id>.md`'s `raw-path`. Refetch from `url:` only if the raw file/package is missing or unreadable, and flag the missing raw path for the user.

   - **User confirmed a new fact in the conversation.** Save the user's statement as a meaningfully named raw markdown snapshot, create a `refs/<id>.md` record with `type: conversation`, and ingest from that raw snapshot.

   - **The answer itself is a useful synthesis** — a comparison, analysis, or discovered relationship that no single existing page captures. Create a new page in `pages/` capturing the answer in whatever shape fits (table for a comparison, prose for an analysis, bulleted threads for a discovered chain). Mark it as derivative so provenance stays honest: include the originating question and a list of the pages you built from. Include a `## Refs` section listing every `[[ref-id]]` the synthesis transitively cites — this keeps cited-by symmetry real (the synthesis links to each ref AND each ref's `## Cited by` lists the synthesis), so lint sees no drift. Add the synthesis to `index.md` so it's reachable, and append `[[synthesis-page]]` to the `## Cited by` of each cited ref. (If your wiki's `schema.md` already has a settled convention for synthesis pages, follow it; otherwise pick a shape that fits and propose codifying it only if a second synthesis later uses the same shape.)

   Skip refile entirely if the answer was a pure lookup that surfaced nothing new. **Never** mint a `refs/<id>.md` record from the model's own answer — synthesis goes in `pages/`, never in `refs/`.

## Don't

- Don't answer from training knowledge. If the wiki doesn't know, the wiki doesn't know.
- Don't mint a `refs/<id>.md` record from the model's answer. Synthesis is a *page*, never a source ref.
- Don't make ad-hoc edits to existing pages while answering. Page changes happen only through the disciplined refile paths above (which may invoke ingest and edit pages — that's fine; the constraint is *ad-hoc* edits during answer generation, not refile itself).
- Don't paste the entire pages you read back into the answer. Synthesize.

## Output

The answer to the user, with `[[page-name]]` citations inline. If refile happened, append a 1-line note: `Refiled to: page-a, page-b (new)`. If the vault is git-tracked, commit the refile so the timeline reflects it.
