# Bounded Mode — Papers, RFCs, Articles, Documents

For artifacts with a fixed scope — a research paper, an RFC, a blog post, a slide deck, a proposal. The goal is to help the user comprehend the source by mapping its conceptual landscape. If the user's notes cover most of the wikilinks, understanding is a matter of connecting the dots. Dangling wikilinks signal concepts worth exploring to deepen comprehension.

## Format

```markdown
# Title (Authors, Year)

Overview — flexible length. Orient the reader on what this is, what it
contributes, and why it matters. This is also the place for
source-specific content that doesn't fit the sections below (benchmark
results, dataset details, implementation specifics, adoption status).
Can include lists when the source has a distinctive taxonomy or
framework worth surfacing (e.g., "nine characteristics").

## Context

- What the field/landscape looked like before this
- The gap, problem, or motivation this addresses
- [[Prior Concept]] that the reader should understand

## [Format-Dependent Section]

- What the source proposes, contributes, or argues
- Key findings, innovations, or decisions

## Key Concepts

- [[Concept A]] — one line only
- [[Concept B]] — one line only

## How They Connect

Brief prose or an ASCII diagram showing how the ideas build on each
other. Use [[wikilinks]] inline. Diagrams work well for architectures
and data flows.
```

## The Format-Dependent Section

The section after Context adapts its heading to the source type:

| Source Type | Section Heading | Focus |
|-------------|----------------|-------|
| Research paper | **Contributions & Innovations** | What the paper figured out and why it matters |
| RFC / RFE | **Proposal** | What's being proposed and the design decisions |
| Blog post / article | **Key Arguments** or **Key Findings** | The author's main points or discoveries |
| Presentation / slide deck | **Key Takeaways** | The core messages |
| Design doc / proposal | **Proposal** | What's being proposed and the tradeoffs |

Use your judgment — the heading should match what the source is doing. The examples above are starting points, not an exhaustive list.

## Guidelines

- **Title includes metadata.** Always include authors (last names) and year in the heading. For well-known works, include the colloquial name too (e.g., "Attention Is All You Need").
- **Overview is flexible.** It can be paragraphs, lists, or both. This is the place for source-specific content that doesn't cleanly fit the sections below. Think of it as a brief that covers everything someone skimming would want to know. Examples of content that belongs here: a source's distinctive taxonomy or framework surfaced as a list, benchmark results with wikilinked benchmark names (e.g., "outperformed prior work on [[HellaSwag]] and [[MMLU]]"), dataset details, adoption status, implementation notes.
- **Always include the source.** Add `source:` to the frontmatter with the URL or citation. The reader should be able to find the original artifact.
- **"Context"** is broad. It might be the state of the field before a paper, the problem a proposal addresses, the gap a blog post identifies, or the situation that prompted an RFC. Use wikilinks for prior concepts the reader should understand.
- **The format-dependent section** captures what the source actually brings. Lead with the insight or proposal, not the process. The user wants "what did they figure out / propose / argue" before "how did they do it."
- **5-10 key concepts**, ranked by importance. These are the ideas worth a standalone note. They may overlap with concepts mentioned in other sections — here they get their one-line descriptions.
- **Not every bullet needs a wikilink.** Wikilinks suggest learning paths. Use them where a standalone note would help the reader go deeper. Skip them where the bullet is self-contained context.
- **Some source structure is fine.** If a paper's methodology or an RFC's alternatives section adds comprehension value, cover it — in the overview or as context. Don't reproduce the full source structure, but don't avoid it dogmatically either.

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| Reproducing the full source structure | Use the skeleton; fold extra details into the overview |
| Every technical term becomes a wikilink | Only concepts worth a standalone note |
| Missing "Context" | Always include — shows why this source matters |
| Overview limited to 2-3 sentences | Expand it to cover what doesn't fit the explicit sections |
| Rigid "Contributions & Innovations" heading on non-papers | Adapt the heading to the source type |
| Kebab-case wikilinks | Use title case with spaces: `[[Scaling Laws]]` not `[[scaling-laws]]` |

## Example — `/tldr https://arxiv.org/abs/1706.03762`

```markdown
---
date: 2026-05-11
tags: [deep-learning, nlp, architecture]
type: concept-map
source: https://arxiv.org/abs/1706.03762
---

# Attention Is All You Need (Vaswani et al., 2017)

Proposes the Transformer — replaces recurrence and convolution entirely
with attention mechanisms, achieving state-of-the-art translation quality
with far less training time. Virtually every modern LLM descends from this.

On WMT 2014 English-to-German, the big Transformer outperformed all
prior models including ensembles by over 2 BLEU. Training took 3.5 days
on 8 GPUs — a fraction of competing approaches.

## Context

- [[Recurrent Neural Networks]] were the dominant sequence modeling
  approach, but training was inherently sequential
- [[Sequence-to-Sequence Models]] with attention had improved translation
  quality, but still relied on RNN encoders and decoders
- Parallelization during training was severely limited, making scaling
  to longer sequences expensive

## Contributions & Innovations

- Eliminated recurrence entirely, enabling full parallelization during
  training
- Introduced [[Self-Attention]] as the sole mechanism for capturing
  dependencies at any distance in a sequence
- Demonstrated that [[Multi-Head Attention]] — running attention in
  parallel across different representation subspaces — captures richer
  relationships than single attention
- Achieved state-of-the-art on translation with significantly less
  training time

## Key Concepts

- [[Self-Attention]] — each token attends to all others in the sequence
- [[Multi-Head Attention]] — parallel attention over different
  representation subspaces
- [[Positional Encoding]] — how transformers know token order without
  recurrence
- [[Encoder-Decoder Architecture]] — source processing vs. target
  generation stacks
- [[Attention Scaling]] — preventing dot products from saturating softmax

## How They Connect

The [[Encoder-Decoder Architecture]] processes input through the encoder
(using [[Self-Attention]] to build contextual representations) and
generates output through the decoder (attending to both its own prior
tokens and the encoder's output via [[Multi-Head Attention]]).
[[Positional Encoding]] injects order information since there's no
recurrence. [[Attention Scaling]] keeps training stable as dimensions
grow.
```
