---
name: explain
description: Explain a topic — codebase feature, article, URL, doc, or concept — by extracting its logic and rendering it as a monospace text diagram. Brief by default; pass `--deep` for a multi-section breakdown.
---

## Context

- Topic to explain: $ARGUMENTS
- Current directory: !`pwd`

# Explain Topic

Extract the load-bearing logic of any topic — code, text, or concept — and render it as a monospace text diagram. Output is brief by default; deep on request.

## Mode Selection

Inspect `$ARGUMENTS`:

- **Deep mode** — if arguments contain `--deep`, `in detail`, `deep dive`, `comprehensive`, `full breakdown`, or `verbose`
  → Load [references/deep-mode.md](references/deep-mode.md) and follow that output template.
- **Brief mode** (default) — otherwise
  → Use the brief output described below.

**Exploration depth is the same in both modes. Only the output differs.**

## Step 1: Understand the Request

The user wants to understand: `$ARGUMENTS`

The topic can be anything that has structure worth drawing:

- **code** — a feature, directory, file, or component
- **text** — a URL, local document, or pasted block
- **concept** — a mental model, pattern, or term

If the topic is unclear or too broad, ask the user to clarify before proceeding.

## Step 2: Gather Material

The extraction goal is the same regardless of source. Read enough to see the whole shape before extracting — never summarize piecemeal.

**Always extract:**

- **Contract or invariant** — what must hold; the rule the rest of the topic is built around.
- **Motivating premise — *why this over the alternatives*.** Whenever the topic recommends, argues, chooses, or designs, capture *what gets rejected* and *the trade-off that decides*. This is usually the central contribution; never drop it.
- **Entry points and triggers** — what kicks the system off.
- **Core actors and their responsibilities.**
- **State transitions and lifecycles.**
- **Inter-actor communication** — events, calls, messages, ordering.
- **Error / failure modes.**

**Tactical hints by source (use whatever tools your harness exposes):**

- Code / local path → search and read; read types first, follow imports, trace happy path then errors.
- URL → fetch with whatever web-retrieval tool is available.
- Document / pasted text → read end-to-end before extracting.

## Step 3: Output

### Brief mode (default)

The deliverable is one monospace text diagram capturing the topic's *core* — its first-principle structure. The diagram is the output, not a supporting element. Skip surface mechanics; capture the load-bearing logic. The diagram should be **extensible**: if the user asks for more, grow the existing diagram (more nodes, branches, annotations) rather than redraw.

The shape is up to you — match it to the topic. A few common shapes as guides (not an exhaustive list, mix freely):

- **Linear chain** — sequence of steps or transforms
  ```
  A -> B -> C
  ```
- **Comparison** — alternatives side by side
  ```
  plan a: A -> B -> C
  plan b: A -> C -> D
  ```
- **Branch / decision** — one input forks
  ```
  A -> [cond?] -> B
                \-> C
  ```
- **State machine** — transitions between states
  ```
  idle -> running -> done
            \-> failed
  ```
- **Bounded regions / architecture** — when the topic spans roles or processes, group with boxes and label the wires (e.g. protocol, transport, direction):
  ```
  ┌─ remote ─────┐         ┌─ local ────────────┐
  │  agent  ──WSS──▶ broker ──WSS──▶ extension  │
  └──────────────┘         │            │       │
                           │            ▼ CDP   │
                           │         browser    │
                           └────────────────────┘
  ```

Guidelines, not limits:

- Prefer the smallest graph that captures the first principle — but go richer when the topic genuinely needs it (multiple actors, labeled wires, annotations).
- Label edges with the *thing that flows* (protocol, data, event) when it matters.
- Optional 1-line caption — a title placed *above* the diagram, never below. If you're writing words after the diagram, the diagram needs another node, not a footer.
- **Whenever the topic recommends, argues, chooses, or designs, the diagram MUST show what gets rejected and the trade-off that decides.** "X wins because Y" is the whole point — drawing only X drops the *why*. A side-by-side comparison or a `premise → trade-off → therefore` branch usually fits.

### Deep mode (`--deep`)

Load [references/deep-mode.md](references/deep-mode.md) and follow that template.

## Diagrams

For diagram patterns (boxes, arrows, containers, decision points), see [references/diagram-guide.md](references/diagram-guide.md). Load it only when you're about to draw.

Render in monospace text; box-drawing characters are fine. Output diagrams directly in the conversation — do **not** save to a file unless explicitly asked.
