# Ingest

Read one source and integrate it into the wiki.

Input: a path, URL, or pasted text passed as the argument after `ingest`.

## Steps

1. **Read the source.** Fetch URLs; read files; accept pasted text as-is.

2. **Read `schema.md`.** It tells you the wiki's purpose, the link/naming rules, the raw file/package shape, the ref-record shape, and any page conventions the wiki has already settled into. The schema is authoritative for *structural* rules; for page shape it is a record of what has worked so far, not a fixed template.

3. **Briefly orient the user before writing.** State what you take the source to be fundamentally about and which entities look worth a page (one short list, with a one-line reason each). The user may redirect — narrow the scope, drop entities, name ones you missed. Skip this step only if the user has explicitly asked for a silent ingest.

4. **Create the immutable raw file or package.** Save the source input before distilling it. Choose the raw path from what the preserved artifact *is* — source title, author, format, capture type, or collection context — not mechanically from the ref id. Raw filenames must carry meaning; never use generic names like `original.md`, `document.md`, or `source.md`.
   - Single-file source → save one meaningfully named file under `raw/`.
   - Multi-file source → create one meaningfully named directory under `raw/` and use meaningful filenames inside it.
   - Supporting files → put them with the source package when they belong only to that source; use meaningful filenames for images, attachments, screenshots, audio, video, and data files.
   - Let the raw path and ref id share a stem only when that is genuinely the clearest name for both the preserved artifact and the citeable record.

5. **Create the ref record** at `refs/<id>.md` per the reference-record shape in `schema.md`. The `id` must be unique across both `pages/` and `refs/` (single namespace), and should name the citeable source record. The `raw-path` must point at the raw file or package from step 4. The `## Summary` should orient a future reader to what the source is — not transcribe its outline. If the source has multiple named entities with relationships, lead `## Summary` with a fenced monospace diagram (the same style `/explain` produces) so the global view is visible at a glance, then add one short orienting line beneath. A reader who only ever sees the diagram should already know what the source is about.

6. **For each entity that earned a page in step 3:**
   - Search `pages/` for an existing page (grep title, slug, aliases).
   - **Existing page** → edit in place. Merge new facts under a fitting section. Cite the new ref as `([[ref-id]])`, with a locator suffix when the source is long (see `schema.md`'s cross-ref rules — `([[ref-id]] §3.2)`, `([[ref-id]] @12:34)`, `([[ref-id]] "quoted span")`). Add or update `[[wikilinks]]` if the relationship graph changed.
   - **New page** → create a file in `pages/`. Choose the shape that fits the entity:
     - If `schema.md`'s "Page conventions" already has a matching pattern, follow it.
     - Otherwise, pick a shape the source actually supports — prose where the source is narrative, bullets where it's a list, a table where it's a comparison, sub-headings where there are distinct facets. Don't pad sections that have nothing to say; don't write paragraphs when bullets would scan better.
   - When citing the source, follow the locator rule above so future verification doesn't require re-reading the whole source.
   - Ensure reachability: list the new page in `index.md` (preferred) or link it from at least one listed page.

7. **Propose schema growth only when a shape has earned it.** If the same page shape has now appeared twice (same kind of entity, same useful sections) and isn't yet in `schema.md`'s "Page conventions," tell the user in one or two lines and add it on confirmation. Don't speculate about future shapes; codify only what has already recurred.

8. **Update each cited ref's `## Cited by`** to include the pages that now cite it.

9. **Update `index.md`** with one-line entries for any new pages, under an appropriate section (create the section if none fits).

10. **Stop** when every in-scope entity from the source is reflected somewhere. If the vault is git-tracked, commit the touched files — `git log` is the wiki's chronological history.

## Don't

- Don't paste long passages from the source into entity pages — paraphrase and cite. The raw file or package already preserves the source.
- Don't create one page per source. `refs/` holds provenance; `raw/` holds originals; `pages/` holds entities. A source may touch many pages; a page may cite many refs.
- Don't mint page conventions before they've recurred. The conventions list is for patterns the wiki has *already* settled into.
- Don't pad a section because the schema or a convention names it. If a section has nothing to add for the entity at hand, drop it. Empty padding is worse than a missing section.
- Don't speculate. If the source is ambiguous, write the ambiguity into the page, not a guess.
- Don't run lint as part of ingest. Ingest may leave invariants temporarily violated — that's what lint is for.

## Output

Brief report: pages created, pages edited, ref id, raw path, anything skipped (with reason), and any schema convention proposed. List touched files so the user (or you, if auto-committing) can record the diff in `git log`.
