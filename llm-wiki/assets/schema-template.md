# Wiki schema

> Configuration for this wiki. Every ingest, query, and lint reads this file. It starts thin — naming, links, the raw source package shape, and the reference-record shape — and grows as you and the LLM discover what works for this wiki's domain. Editing the schema is normal and expected; it is the document that turns a generic LLM into a disciplined maintainer of *this particular* wiki.

## Purpose

<!-- One sentence: what is this wiki for? Who or what does it serve?
Examples:
- "Notes from machine-learning papers and podcasts."
- "Engineering decisions and architecture notes for the foo backend."
- "Research toward an evolving thesis on topic X." -->

## Naming

- Filenames: lowercase kebab-case, ASCII only.
- Disambiguation: append a discriminator when needed (`<name>-<context>.md`).
- A page may declare aliases in frontmatter so it can be linked under several names.
- Ref IDs name citeable source records. Raw paths name preserved artifacts. They may share a stem when that is the clearest name for both, but don't force them to match.

## Cross-ref rules

- `[[name]]` resolves against `pages/` first, then `refs/`. Bare URLs for external links.
- **Single namespace:** page slugs and ref IDs share one namespace; one slug, one file. Lint flags collisions.
- Link the *first* mention of an entity on a page; don't link every occurrence.
- Every page must be reachable from `index.md` — directly listed there, or via a `[[wikilink]]` chain from a listed page.
- **Citation locators.** A bare `([[ref-id]])` is enough when the source is short enough that a future reader can locate the claim by re-reading the whole source (a tweet, a one-page article, a single conversation). For longer sources — PDF chapters, multi-thousand-line transcripts, multi-hour videos, long technical docs — append a locator so the citation actually supports verification: `([[ref-id]] §3.2)`, `([[ref-id]] p. 47)`, `([[ref-id]] @12:34)`, `([[ref-id]] "short quote span")`. The format is informal; what matters is that a reader can jump to the passage without re-skimming the source.

## Raw source files and packages

Files in `raw/` are immutable source snapshots. The LLM reads them but never edits them after ingest. Raw paths must carry meaning from the artifact itself — title, author, format, capture type, or collection context — not just from the ref id. Never use generic names like `original.md`, `document.md`, or `source.md`.

```
raw/
  <meaningful-artifact-name>.<ext>     # single copied file, fetched snapshot, transcript, or pasted text
  <meaningful-source-package>/         # multi-file source package
    <meaningful-file-name>.<ext>
    assets/                            # optional images, screenshots, audio, video, attachments
```

If a source needs correction, create a new raw file/package and a new ref instead of mutating the old raw content.

## Reference records

Files in `refs/` are append-only provenance metadata so any source can be cited and re-ingested later. Each record:

```yaml
---
id: <stable-kebab-slug>           # unique across pages/ AND refs/
type: <paper | article | video | conversation | …>
url: <source URL, file:///abs/path, or "conversation">
raw-path: <path to immutable raw file or package>
fetched-at: <ISO date>
---
```

Body:

1. `## Summary` — what this source *is*, written so a future reader (or future you) can tell at a glance what they're looking at. Pick whatever shape fits the source: prose, bullets, sub-headed facets, a small table. **When the source has multiple named entities with relationships among them, lead with a small monospace diagram (the same style `/explain` produces) — fenced in a code block so it renders verbatim — followed by one short orienting line.** A diagram gives the global view in a glance that no paragraph can. The job of `## Summary` is to orient, not to transcribe; if it starts to read like a flattened table of contents of the source, cut it down.
2. `## Cited by` — bulleted `[[page-name]]` entries, kept in sync by ingest.

`id`, `url`, `raw-path`, `fetched-at`, and `## Summary` are written once at ingest and don't change after. `## Cited by` is the one growing part.

## Page conventions

<!-- This section starts empty and accretes as the wiki settles into its domain.

As ingests recur, certain kinds of entities will keep appearing (people, places,
concepts, products, decisions, incidents — whatever fits the purpose above) and
certain section patterns will turn out to be repeatedly useful. When a shape has
*already recurred* in the wiki, write it down here in one or two lines so future
ingests stay consistent.

Each convention entry should record only what is load-bearing for *this* wiki:
the kind of entity, the frontmatter fields that matter, the sections that have
proven useful. Two anti-patterns to avoid:

  - Pre-inventing conventions before they've appeared.
  - Listing required sections that don't actually carry information for the
    entity at hand — empty or padded sections are noise.

The format of each section is a judgment call at write time, not something the
schema dictates: prose where there's narrative, bullets where there's a list,
a table where there's a comparison. The goal is a page that scans, not one that
matches a template. -->

## History

This wiki uses **git** as its chronological log. Commit after each meaningful ingest, query refile, lint --fix, or schema change so `git log` is the timeline.
