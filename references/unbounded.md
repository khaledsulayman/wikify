# Unbounded Mode — Topics, Technologies, Domains

For broad topics where there's no fixed boundary. The goal is to map the landscape so the user knows what exists before diving into any one area.

## Format

```markdown
# Topic Name

One paragraph orienting the reader. What is this, why does it matter,
and where does it sit relative to things the user already knows.

## Key Concepts

- [[Concept A]] — one line only
- [[Concept B]] — one line only
- [[Concept C]] — one line only

## How They Connect

Brief prose or an ASCII diagram showing how the key concepts relate.
Use [[wikilinks]] inline. A diagram works well when the relationships
are spatial or flow-based (e.g., data pipelines, request paths).

## Adjacent Topics

- [[Related Domain A]] — how it relates to this topic
- [[Related Domain B]] — why someone learning this might also need this

## References

- Sources used to build this concept map
```

## Guidelines

- **8-15 key concepts**, ranked by importance to the topic. Resist being comprehensive. If the topic is very broad, pick the 10 most important and mention the map is intentionally scoped.
- **Concepts over products, but products are fine when relevant.** Prefer `[[Sidecar Proxy]]` over `[[Envoy]]` when the concept is what matters. But a `/wikify podman` should absolutely mention `[[Docker]]` — don't avoid product names when they're central to understanding the topic.
- **Adjacent Topics** maps the neighborhood — related domains the user might want to explore next. Keep it to 2-4 links. These are broader than Key Concepts and help the user see where this topic sits in the larger landscape.
- **How They Connect** can be prose, an ASCII diagram, or both. Diagrams are especially useful for topics with clear component relationships or data flows.
- **References** — only cite sources you actually consulted while building this concept map (web pages fetched, articles read via search). If you worked entirely from memory, omit the References section. Never fabricate or guess at URLs to fill the section out.

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| 25+ concepts (wall of text) | Cut to 10-15. Scope is a feature. |
| Missing "Adjacent Topics" | Always include — helps the user see the bigger picture |
| Concepts not ranked by importance | Most essential concepts first, secondary ones later |
| Kebab-case wikilinks | Use title case with spaces: `[[CAP Theorem]]` not `[[cap-theorem]]` |

## Example — `/wikify service-mesh`

```markdown
---
date: 2026-05-11
tags: [networking, infrastructure]
type: concept-map
---

# Service Mesh

A dedicated infrastructure layer for service-to-service communication
in microservice architectures. Handles concerns that individual services
shouldn't own: routing, observability, security between services.

## Key Concepts

- [[Sidecar Proxy]] — intercepts service traffic without code changes
- [[Control Plane vs Data Plane]] — the brain (config, policy) vs. the muscle (proxying)
- [[Mutual TLS]] — automatic service identity and encryption
- [[Observability Without Instrumentation]] — tracing and metrics from the proxy layer
- [[Traffic Management]] — canary deployments, circuit breaking, retries
- [[Service Discovery]] — how services find each other dynamically

## How They Connect

A [[Sidecar Proxy]] is deployed alongside each service, forming the
[[Control Plane vs Data Plane|data plane]]. The control plane configures
all proxies to enforce [[Mutual TLS]], route traffic via
[[Traffic Management]] policies, and emit [[Observability Without Instrumentation|telemetry]]
without application code changes. [[Service Discovery]] feeds the control
plane so proxies know where to route.

## Adjacent Topics

- [[API Gateway]] — handles north-south traffic (external → cluster) where service mesh handles east-west (service → service)
- [[Container Orchestration]] — service meshes typically run on top of Kubernetes or similar platforms
- [[Zero Trust Networking]] — service mesh implements zero-trust principles at the network layer

## References

- https://istio.io/latest/docs/concepts/what-is-istio/
- https://linkerd.io/2/overview/
- https://www.cncf.io/blog/2017/04/26/service-mesh-critical-component-cloud-native-stack/
```
