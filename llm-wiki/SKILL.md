---
name: llm-wiki
description: Maintain a persistent, LLM-curated markdown wiki as an alternative to RAG — knowledge is distilled into pages once and compounds over time instead of being re-derived per query.
---

## Context

- Command: $ARGUMENTS
- Current directory: !`pwd`

# LLM Wiki

A persistent markdown knowledge base maintained by an LLM. Four ops: `init`, `ingest`, `query`, `lint`. The wiki compounds — each ingest and query leaves it strictly better.

> **Pattern source (attribution):** https://gist.githubusercontent.com/karpathy/442a6bf555914893e9891c11519de94f/raw/ac46de1ad27f92b28ac95459c782c07f6b8c964a/llm-wiki.md — pinned to a specific revision. This skill is self-contained; extend `references/` when new edge cases come up rather than punting to the gist (which is intentionally abstract and isn't an operations manual).

## Mental model

> Obsidian is the IDE; the LLM is the programmer; the wiki is the codebase.

## First principle

Bookkeeping — cross-refs, dedup, consistency — is the bottleneck of a knowledge base, not reading. Let the LLM maintain the wiki so knowledge compounds, instead of re-deriving from raw chunks every query like RAG.

## Global view

```
                       ┌──── schema.md ─────┐
                       │  naming, links,    │
                       │  raw/ref-record    │
                       │  shapes, page      │
                       │  conventions       │
                       │  (grows with use)  │
                       └─────────┬──────────┘
                                 │ guides LLM behavior
                                 ▼
┌─ raw sources ─┐  INGEST   ┌──── wiki ─────┐   QUERY    ┌─ user ─┐
│  (immutable)  │ ────────▶ │  page_a.md    │ ◀────────  │        │
│  pdfs, urls,  │  read +   │  page_b.md    │            │        │
│  transcripts  │  edit     │  cross-refs   │ ─ answer ▶ │        │
└───────────────┘  pages    └──────┬────────┘            └────┬───┘
                                   │                          │
                                 LINT                  valuable findings
                          (contradictions,             re-filed into wiki
                           orphans, missing      ◀───────────┘
                           xrefs)                 (compounds over time)
```

## Layout

```
wiki/
  schema.md       configuration: naming, links, raw/ref-record shapes,
                  page conventions (the conventions list grows with use)
  index.md        content-oriented catalog — each entry is [[page]] + one-line summary;
                  this is the navigation entry point, read first on query
  pages/*.md      entity & synthesis pages, connected via [[wikilinks]]
  refs/*.md       append-only provenance records (id, type, url, raw-path,
                  summary, ## Cited by); citeable as [[ref-id]]
  raw/*           immutable source snapshots/packages; paths name the
                  preserved artifact, not necessarily the ref id
```

## Language

All generated content — entity pages, ref summaries, index entries, synthesis pages, lint reports, and any user-facing output — must use **Chinese (Simplified)** as the primary language, except:

- **File names** (page slugs, ref IDs, raw filenames) must remain in **English**, lowercase kebab-case.
- **Code blocks**, command output, and technical identifiers (variables, library names, CLI flags) stay in their original form.
- The `schema.md` configuration itself stays in English.

## Location

Default: `<obsidian-vault>/Wiki/` — resolve the vault with `obsidian eval code="app.vault.adapter.basePath"` (strip `=> ` prefix). Override with `--path <dir>`. Fall back to `./wiki/` only if no vault is available.

## Ops

Infer the op from `$ARGUMENTS` and act. Auto-init silently if the wiki doesn't exist yet. Ask only on a destructive fork.

- `init` → [references/init.md](references/init.md) — bootstrap schema/index/dirs
- `ingest <source>` → [references/ingest.md](references/ingest.md) — distil a source into pages
- `query <question>` → [references/query.md](references/query.md) — answer from the wiki; refile new findings
- `lint` → [references/lint.md](references/lint.md) — audit invariants

## Invariants

1. Every page reachable from `index.md` — directly listed there or via a `[[wikilink]]` chain from a listed page.
2. Every `[[name]]` resolves to a real page in `pages/` or reference in `refs/`. Page slugs and ref IDs share one namespace.
3. Every file in `refs/` cited by >=1 page.
4. Every ref's `raw-path` points at an existing immutable raw file or package.
5. No two pages contradict on the same entity.

Schema is co-evolved, not pre-frozen: page conventions in `schema.md` are a record of shapes the wiki has _already_ settled into through use, not a template that pages must conform to up front. Ingest proposes a convention once a shape has recurred; until then, each page picks the shape that fits its entity.

Distil don't dump; pick the shape that fits (prose, bullets, table, sub-headings) rather than padding a fixed template; one entity, one page; answers come from the wiki, not training knowledge. Ingest and query may temporarily violate invariants — that's what lint is for.

## History

The wiki uses **git** as its chronological log. Commit after each meaningful ingest, query refile, lint --fix, or schema change so `git log` is the timeline of how the wiki evolved. If the vault isn't under git, mention it once; otherwise stay quiet and just commit.
