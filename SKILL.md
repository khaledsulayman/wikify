---
name: wikify
description: Use when the user wants a structured overview of a topic, technology, research paper, or document. Triggers on /wikify, "wikify this", "give me the tldr", "brief me on", "what should I know about", or when the user encounters something new and wants the conceptual landscape mapped out before diving in.
---

# wikify

Generate a concept map — a concise learning scaffold with wikilinks — in the user's Obsidian vault.

## Overview

A concept map is NOT a comprehensive summary. It is a map of terrain the user hasn't explored yet. Key concepts appear as `[[wikilinks]]` with one-line descriptions. Many wikilinks will be dangling — that's the point. They mark territory to explore later.

The user will fill in the linked notes over time. The concept map is the starting point, not the destination.

## Detecting Mode

The argument determines which reference file to follow:

| Input | Mode | Action |
|-------|------|--------|
| Plain topic string (`service-mesh`, `kubernetes`) | Unbounded | Read `references/unbounded.md` |
| PDF file path, arxiv/doi URL | Bounded | Read `references/bounded.md` |
| General URL | Read first | If it's a single document (blog post, RFC, paper), read `references/bounded.md`. If it's a landing page or docs site, read `references/unbounded.md`. |

After detecting the mode, read the appropriate reference file and follow its format exactly.

## Output Location

Write to `~/notes/agents/khaleds-concept-maps/<topic>.md`

If a concept map already exists for this topic, tell the user and ask whether to update or skip.

## Shared Rules

These apply to both modes.

### Calibrating Abstraction

The most important thing this skill teaches. Wikilinks should point to concepts at the right level for learning — not too granular, not too broad.

**Too granular** (don't do this):
```
- [[Sinusoidal Positional Encoding]] — uses sine/cosine at varying frequencies
```
Implementation details. The user doesn't need a standalone note about this.

**Too broad** (don't do this):
```
- [[Deep Learning]] — a subfield of machine learning
```
So broad it's not actionable.

**Just right**:
```
- [[Positional Encoding]] — how transformers know token order without recurrence
```
Worth exploring in its own note.

**The test**: would the user benefit from a standalone note on this concept? If yes, good wikilink. If it's only meaningful in context of this specific paper/topic, too granular.

### Wikilink Format

Use **title case with spaces**, not kebab-case. Match how titles appear in Obsidian:

- YES: `[[CAP Theorem]]`, `[[Sidecar Proxy]]`, `[[Multi-Head Attention]]`
- NO: `[[cap-theorem]]`, `[[sidecar-proxy]]`, `[[multi-head-attention]]`

### Wikilink Descriptions

One line max. The description helps the user decide whether to explore, not teach the concept.

- YES: `[[Sidecar Proxy]] — intercepts service traffic without code changes`
- NO: `[[Sidecar Proxy]] — a proxy instance deployed alongside each service instance, intercepting all inbound and outbound network traffic...`

Not every bullet needs a wikilink. The purpose of the concept map is comprehension. Wikilinks suggest learning paths in service of that goal — use them where a standalone note would genuinely help, skip them where the bullet is self-contained context.

### Wikilinks in Prose

Wikilinks aren't just for bullet lists. When named concepts, algorithms, tools, or benchmarks appear in descriptions or prose sections, wikilink them if they're worth a standalone note. For example, if a Key Concepts bullet mentions Raft and Paxos as examples of consensus protocols, those should be `[[Raft]]` and `[[Paxos]]` — not just plain text.

### Concept Ordering

Rank concepts by importance to the topic — most essential first. A reader skimming the list should encounter the foundational ideas before the secondary ones.

### Wikilink Resolution

Check if a note already exists:
1. `~/notes/agents/atomic-notes/<name>.md`
2. `~/notes/agents/<name>.md`
3. `~/notes/<name>.md`

Existing notes link to real content. Missing notes are dangling — both are fine. Links to the user's personal notes are allowed (agent → personal).

### Frontmatter

All concept maps use:
```yaml
---
date: YYYY-MM-DD
tags: [topic-a, topic-b]
type: concept-map
---
```

### Concept Disambiguation

Key concepts should be stable — the same topic should produce roughly the same concept list regardless of when or how many times you run it. To achieve this, use a multi-agent consensus approach:

1. **Spawn 3 subagents in parallel**, each with this prompt (adapt the topic/URL):

   ```
   List the 10-15 most important concepts someone needs to understand
   about [TOPIC]. For each concept, give the canonical name in
   [[Title Case]] wikilink format and a one-line description.

   Rank by importance — foundational concepts first. Only include
   concepts worth a standalone note (not implementation details,
   not overly broad categories).

   Output as a simple list:
   - [[Concept Name]] — one-line description
   ```

   For bounded mode (papers/articles), adjust the prompt to focus on the key concepts from that specific source.

2. **Collect all three lists** and find the overlap. A concept makes the cut if it appears in at least 2 of 3 lists. Match by meaning, not exact name — `[[CAP Theorem]]` and `[[Brewer's CAP Theorem]]` are the same concept.

3. **Use the consensus list** as your Key Concepts section. For descriptions, pick the clearest one across the agents or synthesize. Preserve the importance ranking.

This filters out concepts that are defensible-but-debatable (the kind that vary between runs) and keeps only the ones that any knowledgeable person would include.

If you cannot spawn subagents (e.g., running inside a subagent yourself), skip this step entirely and generate the concept list directly. Do not try to simulate multiple independent passes in your own reasoning — that produces worse results than just writing the list straightforwardly.
