# Diagram Guide

Patterns for clean, readable diagrams that render properly in monospace text. Box-drawing characters (`┌─┐│└┘─▶◀▲▼`) are encouraged; plain ASCII fallbacks (`+--+ | ->`) work too.

## Boxes for Components

```
┌─────────────────┐
│   Component     │
└─────────────────┘
```

## Arrows for Flow

```
──▶  Direction (right)
◀──  Direction (left)
 │
 ▼   Direction (down)
 ▲
 │   Direction (up)
```

## Container Boxes

Wrap a group of components inside a labeled container:

```
┌─────────────────────────────────────────┐
│              SYSTEM NAME                │
├─────────────────────────────────────────┤
│   [Components inside]                   │
└─────────────────────────────────────────┘
```

## Flow Connections

Linear flow with a branch:

```
Component A ──▶ Component B ──▶ Component C
                    │
                    ▼
              Component D
```

## Decision Points

```
     ┌─────────┐
     │ Check?  │
     └────┬────┘
    ┌─────┴─────┐
    ▼           ▼
  [YES]       [NO]
```

## Sequence Diagrams

For ordered interactions between actors:

```
User          API           Worker         DB
 │             │              │             │
 │── POST ───▶ │              │             │
 │             │── enqueue ─▶ │             │
 │◀── 202 ──── │              │             │
 │             │              │── INSERT ─▶ │
 │             │              │◀── ok ───── │
```

## State Diagrams

For lifecycles:

```
[pending] ──▶ [running] ──▶ [done]
                 │
                 └──▶ [failed] ──▶ [retrying]
```

## Tips

- Pick **box widths** that fit the longest label inside, then keep all boxes at the same width per row
- **Align arrows** so they don't visually drift — use spaces, not tabs
- **Keep diagrams narrow** — under 80 columns ensures they don't wrap in narrow terminals
- **One concept per diagram** — if you're cramming, split into two diagrams
