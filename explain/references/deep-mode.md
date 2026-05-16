# Deep Mode Output Template

Use this template when the user passes `--deep` (or "in detail", "deep dive", "comprehensive", "full breakdown", "verbose") to the `explain` skill.

Deep mode follows the **same first-principle + text-diagram philosophy** as brief mode — just with more diagrams covering more facets. Each diagram should still capture the *core logic* of its slice (contract, invariant, flow), not surface mechanics. Prose is supporting material; the diagrams do the heavy lifting.

Adapt the content based on what the topic requires:

- **Architecture-focused** — if the topic is about system design, components, or structure
- **Data flow-focused** — if the topic is about how data moves through the system
- **Concept-focused** — if the topic is about understanding a specific concept or pattern
- **Comprehensive** — if the topic is broad, cover all relevant aspects

## Recommended Sections

Pick the sections that fit the topic. Not every explanation needs all of them. Each section should lead with its own diagram when the structure can be drawn — use the same shapes as brief mode (linear, comparison, branch, state, bounded regions), mixed freely.

### Overview
The first-principle framing: the *contract* (what must hold), the *invariant* that forces the design, and one high-level graph showing the big picture and what sits next to it.

### Components / Flows
A focused graph per component or flow, capturing the core logic of that slice. Cover responsibilities, inputs, outputs. Label edges with the thing that flows (protocol, data, event).

### Data Structures
Key types, interfaces, and schemas — show the shape, not just names. A type-relationship graph (boxes + arrows for references/inheritance) often beats prose.

### Events / Messages
Communication patterns rendered as a sequence or message-flow graph: what fires, what drains, in what order.

### State Transitions
A state-machine graph. Almost always clearer than prose for lifecycle topics.

### Key References
A reference table of the most important pointers — files, sections, URLs, or anchors. Format:

| Reference | Purpose |
|-----------|---------|
| `path/to/file.ts` | What lives here |
| `https://…#section` | The claim or argument that lives here |

## Quality Guidelines

- Each graph captures the first principle of its slice — no graph that just lists names without showing structure.
- Multiple graphs > one mega-diagram. Split by concern (architecture, flow, state, types) rather than cramming everything into one.
- Label edges with the *thing that flows* (protocol, data, event) when it matters.
- Reference specific pointers when helpful — `path:line` for code, section anchors or URL fragments for text.
- Highlight key design decisions, trade-offs, and patterns — ideally as a comparison graph (`option a: ... / option b: ...`).
- Adapt depth to topic complexity — a small utility doesn't need 8 sections.

## Output Mechanics

- Output directly in the conversation
- Do **not** save to a file unless explicitly asked
- For diagram patterns, see [diagram-guide.md](diagram-guide.md)
