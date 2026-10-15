AISI Model v2.0

**Artificial Intelligence System Interconnection Model**

A seven-layer reference architecture for AI systems that call other agents, Model Context Protocol (MCP) servers and tools. The AISI Model defines how an agent discovers a counterpart, authenticates, negotiates terms, picks an oversight level and leaves evidence, all before any work runs. Four internal layers (physical through data), three exposed layers (agent logic through interface), governance and a signed audit trail that span all seven, a six-phase trust sequence, and 45 baseline controls.

> This is a reference architecture, not a standard, a regulation or a compliance requirement. It carries no force of law and no certification authority. See the "Before You Start" section in each document.

## Documents

The model is published as a core document and six companions, all at version 2.0 (October 2026). Each is self-contained and cross-referenced to the others. The documents carry no what-changed sections; version history is in [CHANGELOG.md](CHANGELOG.md). Readable Markdown copies of all seven are in `markdown/`, the control registry is in `controls/` as JSON and CSV, and JSON Schemas for the AISI manifest, the behavioral contract, the hold response, the audit trail segment and the MCP server registry entry are in `schemas/`.

| Document | Description |
|----------|-------------|
| AISI Model v2.0 | The core reference architecture. Seven layers, the two boundaries, the six-phase trust sequence, protocol notes (MCP, A2A, retrieval-augmented generation, mutual TLS (mTLS) versus OAuth), and adoption guidance. Start here. |
| Quick Start Guide | The core model and the Baseline Controls condensed into one starting point, with the Agent Card, behavioral contract and oversight tables. |
| Baseline Controls | The 45 controls for production agent deployments, grouped by the team that builds them: agent-to-agent and agent-to-tool (A2A-1 to A2A-13), AI/ML (AI-1 to AI-12), IT infrastructure (IT-1 to IT-10) and security operations (SEC-1 to SEC-10), with priorities, owners and the conditions that move a control up. |
| A2A Protocol Specification | A profile over the A2A protocol and MCP: the native A2A Agent Card and the AISI manifest bound to it, MCP server metadata, behavioral contracts (classification and retention are required terms), authentication and authorization (mTLS and OAuth 2.1, including token binding, client registration and enterprise-managed authorization for MCP), graduated oversight and the hold response. |
| Security Reference | OWASP Top 10 for LLM Applications, OWASP Top 10 for Agentic Applications and MITRE ATLAS (v2026.09) mapped to the layers, threat vectors AT-1 to AT-8 against the trust sequence, the audit trail, agent supply chain controls, lifecycle controls (Annex A), and governance framework mapping (NIST AI RMF, EU AI Act, ISO/IEC 42001, CSA AI Controls Matrix). |
| Implementation Guide | How to build it. Runtime isolation (microVMs for agent-generated code), workload identity (SPIFFE/SPIRE), mTLS, OAuth 2.1 for MCP, model registry, Agent-BOM, OPA policy engine, circuit breakers, Agent Card signing, an MCP server registry, observability and the audit trail, secrets, data controls and security operations. Kubernetes-native with EKS, AKS and GKE callouts. |
| Operations and Lifecycle Reference | How to run it. Behavioral drift detection, shadow agent and shadow MCP server discovery (in the mesh and in the platforms you run), RAG index freshness, prompt versioning and deprecation, memory and retention hygiene, cost enforcement, change control for agent meshes, and decommission. |

The figures are in `diagrams/` as SVG and PNG.

## The Seven Layers

```
Layer 7  Interface                  APIs, MCP servers, A2A endpoints, Agent Cards, protocol handshake
Layer 6  Orchestration and Control  Trust verification, oversight selection, authorization, circuit breakers
Layer 5  Agent Logic                Reasoning, planning, memory, tool selection, MCP client calls
------------------------------------------------------------------------------- internal access boundary
Layer 4  Data                       Runtime context: vector stores, RAG indexes, embeddings, knowledge graphs, agent memory
Layer 3  Model                      Deployed models, model registry, versioning, provenance, routing
Layer 2  Infrastructure             Containers, service mesh, model serving, observability, sustainability metrics
Layer 1  Physical                   GPUs, TPUs, NPUs, data centers, sensors and actuators, jurisdiction
```

Layers 5 to 7 are the exposed layers: where agents, tools and MCP servers meet, and where authorization, oversight and containment get enforced. Layers 1 to 4 are the internal layers, reached only through interfaces the owner controls. Governance and the audit trail (signed, hash-chained segments, one trace ID per action) span every layer on both sides of a connection.

## How an Agent Establishes Trust Before It Acts

Every interaction, agent-to-agent or agent-to-tool, follows the same six phases. Neither MCP nor the Agent2Agent (A2A) protocol defines this sequence; the protocols give you transport and authentication, and the rest has to be designed in.

1. Discovery (layer 7). The caller locates the target via a registry or orchestrator and retrieves its Agent Card or MCP server metadata.
2. Identity Exchange (layer 7). Both sides authenticate with the strongest mechanism the endpoint supports: mTLS with workload identity, OAuth 2.1 tokens, or both as certificate-bound tokens. Identity is established; execution is still subject to authorization.
3. Trust Verification (layer 6). The receiver validates the identity chain back to the originating principal and checks revocation.
4. Contract Negotiation and Oversight Selection (layer 6). Both sides agree scope, data classification, retention, context TTL, budget, latency and sub-delegation. The receiver's policy engine, not the agent, selects the oversight level, subject to the caller's minimum.
5. Execute Request (layer 5). The receiver performs the work inside the agreed contract scope.
6. Response and Compliance Evidence (layer 5, checked at layer 6). The result returns with the receiver's compliance claims; the signed audit record proves it was not altered and who wrote it. Some obligations, such as retention and deletion, can only be verified later.

Session-based negotiation lets the handshake run once and reuse a sender-constrained session token for later requests while conditions stay stable.

## Key Concepts

Agent Cards are the signed, machine-readable descriptions discovery starts from. In this model an Agent Card is the A2A protocol's own card together with an AISI manifest bound to it by digest; the manifest carries the runtime identity binding, the tool allowlist, the issuance window and the default contract terms, and is renewed on a schedule. Tiered discovery uses A2A's extended-card mechanism (a public card for unauthenticated callers, the full card and manifest after identity verification).

Behavioral contracts are per-session or per-request agreements covering scope, data classification, retention, context TTL, oversight level, latency bounds, sub-delegation rules and budget limits. Scope, classification and budget can only narrow through a delegation chain.

Graduated oversight assigns one of four levels per request: autonomous (no human), monitored (a human watches), gated (a human approves before execution) or manual (a human does it). The receiver's orchestration layer selects it, subject to the caller's minimum, and agents never pick their own.

The audit trail spans all layers and both systems: signed, hash-chained segments with one trace ID per action, so the record can be reconstructed end to end and shows if anyone changed it.

## Controls at a Glance

The Baseline Controls define 45 controls in four families, each named for the team that builds it. Every control describes an outcome rather than a product, so a hundred-person company and a hundred-thousand-person enterprise can both meet it. Priorities:

- P1, before production
- P2, for production where the risk warrants it
- P3, planned
- P4, mature programs

Several controls have a published priority that sits below where most agent deployments need them, and the Baseline Controls say when each belongs before production regardless: sandboxing and egress (A2A-8), budgets (A2A-13), third-party agent risk (SEC-9), evaluation and red teaming (AI-9), geographic applicability (IT-9), and replay protection (A2A-9) for agents whose actions commit or cannot be undone.

## Regulatory Framework Mappings

The Security Reference maps layers and controls to NIST AI RMF (GOVERN, MAP, MEASURE, MANAGE), the EU AI Act (Articles 9, 10, 12, 14, 15, 19, 26(6); Annex XI), ISO/IEC 42001 (Clauses 5 to 10; Annex A/B) and the CSA AI Controls Matrix. These mappings are informational, not authoritative interpretations.

## Getting Started

Single-agent system: focus on layers 1 to 5 and the AI/ML and IT infrastructure controls. Discovery and negotiation can wait for a second party; the action controls (A2A-6, A2A-8, A2A-11, A2A-13) apply as soon as the agent can act on anything. Write down the business task, the owner, the authority, the data and the permitted actions, and prove a prohibited action is denied before it executes.

Multi-agent, single organization: add the A2A Protocol Specification, implement Agent Cards and behavioral contracts, and start with the P1 agent-to-agent and agent-to-tool controls (A2A-1, A2A-2, A2A-5, A2A-6, A2A-7). Pull A2A-8 and A2A-13 forward for any agent that runs code, reaches sensitive systems or can delegate without a limit.

Cross-organization agents and third-party MCP servers: everything above, plus identity federation, explicit data classification, retention policies, budget limits, sub-delegation constraints, and the Security Reference's agent supply chain and third-party risk controls.

## License and citation

Apache License 2.0; see [LICENSE](LICENSE) for the text and [NOTICE](NOTICE) for attribution and the third-party material the documents cite. Every document carries the same licensing statement. Forks and adaptations must not imply the author's endorsement. To cite the model, use [CITATION.cff](CITATION.cff). Contributions are welcome under the terms in [CONTRIBUTING.md](CONTRIBUTING.md).
