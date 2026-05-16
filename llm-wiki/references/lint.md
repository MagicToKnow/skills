# Lint

Audit the wiki against its invariants. Report — don't auto-fix unless the user passed `--fix`.

## Checks

Page slugs and ref IDs share one global namespace — they must not collide. When resolving any `[[name]]`, look in `pages/` first; if no match, look in `refs/`; if neither, the link is dangling. The checks below apply that resolver.

1. **Unreachable pages** — compute the set of pages reachable from `index.md`:
   - Seed: every page directly listed in `index.md`.
   - Expand: for each seeded page, follow every `[[wikilink]]` in its body that resolves to a page in `pages/`. Add those pages to the set. Repeat until the set stops growing (BFS/DFS over the page graph; cycle-safe).
   - Flag every page in `pages/` that is *not* in the final set. This catches lone orphans *and* disconnected cycles whose members all have inbound links but no path to `index.md`.
2. **Namespace collision** — a slug exists as both `pages/<name>.md` and `refs/<name>.md`. Rename one (lint can't pick which).
3. **Dangling refs** — `[[name]]` that resolves to neither a page in `pages/` nor a ref in `refs/`.
4. **Missing raw paths** — a `refs/<id>.md` record has no `raw-path`, or its `raw-path` doesn't exist.
5. **Unused refs** — files in `refs/` not cited by any page (no `[[ref-id]]` appears in any page body, and the ref's `## Cited by` is empty).
6. **Cited-by drift** — a page cites `[[ref-id]]` but that ref's `## Cited by` doesn't list the page, or vice versa.
7. **Duplicates** — two pages plausibly about the same entity (similar titles, overlapping content, same canonical name in frontmatter).
8. **Contradictions** — two pages stating different facts about the same entity. Be precise: quote the conflicting lines with file paths. Don't flag plausible disagreements as contradictions; only call out things that genuinely can't both be true.

Page shape is *not* a lint check. `schema.md`'s "Page conventions" is a record of what has worked, not a template to enforce. If you spot the same shape appearing repeatedly across pages and it isn't yet in the conventions list, mention it in the report as a candidate convention — but don't flag pages that diverge from existing conventions; divergence may be the entity needing a different shape.

## Output

A markdown report grouped by check, each item with `path:line`. End with a numbered TODO list ordered by severity (contradictions > namespace collisions > dangling refs > missing raw paths > cited-by drift > unreachable pages > duplicates > unused refs).

## --fix mode

Apply only mechanical fixes — anything requiring judgment stays in the report. After fixing, commit if the vault is git-tracked so `git log` captures the maintenance pass.

Safe to auto-apply:
- Dangling ref where the target was renamed (resolve via fuzzy match against `pages/` and `refs/` + confirm with the user once per session).
- Unreachable page → add an entry to `index.md` under the right section (preferred), or — only if a clearly-related listed page exists — add a `[[wikilink]]` from there.
- Cited-by drift → reconcile `## Cited by` against actual `[[ref-id]]` citations on pages.
- Unused ref → move to `refs/_archive/` (don't delete).

Never auto-apply:
- Namespace collisions (which side to rename is a judgment call).
- Duplicate merges (needs human judgment).
- Contradiction resolution (needs ground truth).
- Candidate-convention adoption (the user decides whether a recurring shape is worth codifying).
