# Chaptered Mode — Books, Courses, Multi-Part Series

For content with discrete, ordered chapters where you can read each chapter's content. The goal is to map the conceptual landscape AND the chapter structure so the reader can navigate the content intentionally.

## Ingestion Pipeline

Chaptered mode reads the actual content — it does not guess from titles or general knowledge. Three phases:

### Phase 1: Discovery

Figure out what chapters exist and where they live.

**Web-based**: Fetch the landing page or table of contents. Find chapter URLs from navigation, sidebar, or ToC links. Build an ordered list of `(chapter_number, title, URL)`.

**PDF**: Scan for chapter headings by reading page ranges. Build an ordered list of `(chapter_number, title, page_range)`.

### Phase 2: Extraction

Spawn subagents in parallel — one per chapter. Each subagent fetches/reads its chapter and returns:

- Chapter title
- 1–3 key concepts as `[[Title Case Wikilinks]]` with one-line descriptions
- One-sentence summary of what the chapter covers

Subagents don't coordinate — they each read independently.

Use this prompt for each subagent (adapt the URL/page range and chapter number):

```
Read the content at [URL or PDF pages]. This is Chapter [N]: "[Title]" of a larger work.

Return exactly three things:

1. TITLE: The chapter title
2. CONCEPTS: 1–3 key concepts from this chapter. For each, give the canonical name in [[Title Case]] wikilink format and a one-line description. Only include concepts worth a standalone note — not implementation details, not overly broad categories.
3. SUMMARY: One sentence describing what this chapter covers.

Format:
TITLE: [chapter title]
CONCEPTS:
- [[Concept Name]] — one-line description
SUMMARY: [one sentence]
```

**Cap**: max 10 subagents in parallel. If the content has more chapters, batch the remainder in a second wave.

### Phase 3: Synthesis

Take all per-chapter extractions and:

1. **Group chapters** into 3–5 thematic phases. Name each group thematically (e.g., "Foundations", "Core Pipeline"), not "Part 1, Part 2." Group by coherence, not just sequential order.
2. **Write group summaries** — 3–5 sentences each, informed by the per-chapter summaries and concepts. Use `[[wikilinks]]` in prose where named concepts appear.
3. **Consolidate Key Concepts** — collect all per-chapter concepts, deduplicate by meaning (not exact name), rank by importance, and prune to 10–15.
4. **Write the orientation paragraph** — what this is, who it's for, what perspective the author brings.

### Consensus Replacement

The 3-subagent consensus mechanism from SKILL.md does NOT apply in chaptered mode. Each chapter's concepts come from actually reading the chapter, so disambiguation is grounded in content rather than parallel guessing. Skip the Concept Disambiguation step in SKILL.md entirely.

## Format

```markdown
---
date: YYYY-MM-DD
tags: [topic-a, topic-b]
type: concept-map
source: <URL or PDF path>
---

# Title (Author, Year)

Orientation paragraph — what this is, who it's for, what perspective
the author brings. 2-4 sentences.

## Key Concepts

- [[Concept A]] — one line
- [[Concept B]] — one line
- [[Concept C]] — one line

## Reading Path

### Group Name (Ch. X–Y)

Summary paragraph — what this section covers, why it's structured
this way, what the reader will understand after finishing it. 3-5
sentences with [[wikilinks]] where named concepts appear in prose.

- Ch. X: Title — description with [[Key Concept]], other details
- Ch. Y: Title — description with [[Another Concept]], context

### Next Group (Ch. Z–W)

Summary paragraph...

- Ch. Z: Title — ...
```

## Guidelines

- **10–15 key concepts**, ranked by importance. These are consolidated from the per-chapter extractions — deduplicated by meaning and pruned.
- **3–5 chapter groups**, named thematically. The AI determines groupings based on content coherence.
- **Group summaries are the narrative.** 3–5 sentences explaining what this section of the content covers and why it's structured this way. Use `[[wikilinks]]` where named concepts appear.
- **Per-chapter entries are signposts.** One line each with 1–3 wikilinked concepts inline. The group summary provides context; individual chapters point to specifics.
- **No "Adjacent Topics" section.** The source itself defines the scope.
- **Always include the source.** Add `source:` to the frontmatter with the URL or PDF path.
- **Title includes metadata.** Author (last names) and year in the heading when available.

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| Per-chapter entries become multi-line summaries | One line per chapter. The group summary carries the narrative. |
| Groups named "Part 1", "Part 2" | Name groups thematically: "Foundations", "Core Pipeline", etc. |
| Key Concepts just mirrors the table of contents | Concepts are ideas, not chapter titles. "Reward Hacking" not "Reward Modeling Chapter". |
| Concepts from general knowledge, not the content | Every concept should trace back to a chapter extraction. Read the content. |
| Too many groups (6+) for a 10-chapter work | 3–5 groups. Merge related chapters. |
| Skipping the ingestion pipeline | Do not generate from general knowledge. Follow all three phases. |

## Example — `/wikify https://rlhfbook.com/`

```markdown
---
date: 2026-05-17
tags: [reinforcement-learning, llm-training, alignment]
type: concept-map
source: https://rlhfbook.com/
---

# Reinforcement Learning from Human Feedback (Lambert, 2025)

A comprehensive treatment of post-training for language models, written
by the post-training lead at the Allen Institute for AI. Covers the
full pipeline from preference collection through policy optimization,
with chapters on emerging topics like synthetic data and reasoning.
Blends perspectives from economics, philosophy, and optimal control
with the core ML formalism.

## Key Concepts

- [[Reward Modeling]] — learning a scoring function from human preferences to guide optimization
- [[Policy Gradient Methods]] — RL algorithms (PPO, REINFORCE) that optimize directly against a reward signal
- [[Direct Alignment]] — algorithms like DPO that skip the reward model and optimize from pairwise preferences
- [[Preference Data]] — human judgments comparing model outputs, the raw signal behind RLHF
- [[Bradley-Terry Model]] — probabilistic framework for converting pairwise comparisons into scores
- [[KL Regularization]] — constraining policy updates to stay close to a reference model
- [[Rejection Sampling]] — filtering candidate completions by reward score for instruction tuning
- [[Over-Optimization]] — when optimizing a proxy reward degrades true quality
- [[Constitutional AI]] — using AI feedback guided by principles instead of human labels
- [[Instruction Tuning]] — SFT that adapts base models to the question-answer format before RL
- [[Reasoning via Reinforcement Learning]] — using RL to develop inference-time reasoning capabilities

## Reading Path

### Foundations (Ch. 1–5)

Sets up the conceptual and mathematical toolkit. Starts with the
motivation for RLHF and a survey of seminal work (InstructGPT, LLAMA,
ChatGPT), then establishes the formal definitions from RL and language
modeling that the rest of the book builds on. The final two chapters in
this group develop the theory of preferences — why human judgment is
needed and how [[Bradley-Terry Model]] formalizes pairwise comparisons.

- Ch. 1: Introduction — motivation, scope, and what RLHF accomplishes
- Ch. 2: Seminal Works — [[InstructGPT]], [[ChatGPT]], key milestones
- Ch. 3: Definitions — RL and language modeling formalism
- Ch. 4: Training Overview — the RLHF objective and training pipeline
- Ch. 5: Preferences — why [[Preference Data]] matters, [[Bradley-Terry Model]]

### Core Pipeline (Ch. 6–12)

Walks through each optimization stage in pipeline order: collect
preference data, train a reward model, constrain optimization with
[[KL Regularization]], then apply the actual training algorithms. Treats
[[Rejection Sampling]], [[Policy Gradient Methods]], and [[Direct Alignment]]
as complementary approaches rather than competitors — each chapter
covers when and why you'd choose one over another.

- Ch. 6: Preference Data — annotation protocols, data quality
- Ch. 7: Reward Modeling — [[Reward Modeling]], [[Over-Optimization]] risks
- Ch. 8: Regularization — [[KL Regularization]] as a guardrail
- Ch. 9: Instruction Tuning — [[Instruction Tuning]] as the warm-start before RL
- Ch. 10: Rejection Sampling — [[Rejection Sampling]], best-of-N filtering
- Ch. 11: Policy Gradients — [[Policy Gradient Methods]], PPO, REINFORCE
- Ch. 12: Direct Alignment — [[Direct Alignment]], DPO and variants

### Advanced Topics (Ch. 13–17)

Extends the core pipeline into active research areas. [[Constitutional AI]]
replaces human labelers with AI feedback. The reasoning chapter connects
RLHF to inference-time scaling. Synthetic data and evaluation chapters
address the practical realities of modern training, and the final chapter
on [[Over-Optimization]] treats it as the central failure mode of the
entire paradigm.

- Ch. 13: Constitutional AI — [[Constitutional AI]], AI feedback loops
- Ch. 14: Reasoning — [[Reasoning via Reinforcement Learning]], inference-time scaling
- Ch. 15: Synthetic Data — distillation, shifting away from human data
- Ch. 16: Evaluation — benchmarks, prompting strategies, evaluation methodology
- Ch. 17: Over-Optimization — [[Over-Optimization]], why proxy rewards degrade quality
```
