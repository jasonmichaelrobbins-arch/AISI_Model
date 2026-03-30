# AISI Model v1.0

**Artificial Intelligence System Interconnection Model**

A seven-layer reference architecture for AI systems that communicate with each other. The AISI Model defines how agents discover, authenticate, negotiate terms, and maintain oversight across trust boundaries. Four private layers (physical through data), three shared layers (logic through interface), and a protocol for safe agent-to-agent coordination.

> This is a reference architecture, not a standard, a regulation, or a compliance requirement. It does not carry the force of law or certification authority. See the "Before You Start" section in each document.

## Documents

The AISI Model is published as six companion documents. Each is self-contained but cross-referenced to the others.

| Document | Description |
|----------|-------------|
| **AISI Model v1.0** | The core reference architecture. Seven layers, the A2A negotiation sequence, governance framework, and adoption guidance. Start here. |
| **A2A Protocol Specification** | Agent Cards, Behavioral Contracts (including budget, retention, data classification, and context TTL terms), Authentication and Authorization, Graduated Oversight, session-based negotiation, and tiered discovery. |
| **Baseline Controls** | The minimum required controls for production A2A deployments (A2A-1 through A2A-13, plus AI/ML, IT Infrastructure, and Security controls), organized by team and priority level. |
| **Security Reference** | OWASP Top 10 for LLM Applications and MITRE ATLAS mappings to AISI layers, A2A-specific threat vectors, agent supply chain controls, and governance framework alignment (NIST AI RMF, EU AI Act, ISO/IEC 42001, CSA AI Controls Matrix). |
| **Implementation Guide** | How to build it. Runtime isolation, workload identity (SPIFFE/SPIRE), mTLS, model registry, OPA policy engine, circuit breakers, Agent Card signing, observability, data controls, and security operations. Kubernetes-native with EKS/AKS/GKE callouts. |
| **Operations & Lifecycle Reference** | How to run it. Behavioral drift detection, shadow AI discovery, RAG index freshness, prompt versioning, memory and context hygiene (including budget enforcement), change control for agent meshes, and agent decommission. |

## The Seven Layers

```
Layer 7  Interface             Agent Cards, APIs, protocol handshake
Layer 6  Orchestration/Control Oversight, authorization, circuit breakers
Layer 5  Agent Logic           Reasoning, planning, memory, tool use
─────────────────────────────────────────────────────────────────────
Layer 4  Data                  RAG indexes, embeddings, knowledge graphs
Layer 3  Model                 Weights, inference runtime, fine-tuning
Layer 2  Infrastructure        Containers, orchestration, networking
Layer 1  Physical              Compute, storage, cooling, power
```

Layers 5-7 are shared across the A2A boundary. Layers 1-4 are private to each system.

## A2A Negotiation Sequence

Every agent-to-agent interaction follows a six-phase handshake:

1. **Discovery** -- Caller locates the target via registry or orchestrator and retrieves its Agent Card (Layer 7)
2. **Identity Exchange** -- Both sides present credentials (SPIFFE IDs via mTLS) and establish a trace ID (Layer 7)
3. **Trust Verification** -- Receiver validates the identity chain back to the originating principal (Layer 6)
4. **Contract Negotiation + Oversight Selection** -- Both sides agree on scope, classification, retention, budget, latency; receiver selects oversight level (Layer 6)
5. **Execute Request** -- Agent performs work within the agreed contract scope (Layer 5)
6. **Response + Compliance Attestation** -- Result returned with contract compliance verification and logged trace segment (Layer 5)

Session-based negotiation allows the handshake to run once and reuse a sender-constrained JWT session token for subsequent requests under stable conditions.

## Key Concepts

**Agent Cards** are signed, machine-readable documents that describe an agent's capabilities, identity binding, and default contract terms. They support tiered discovery (public card for unauthenticated callers, full card after identity verification).

**Behavioral Contracts** are per-session or per-request agreements covering scope, data classification, retention policy, context TTL, oversight level, latency bounds, sub-delegation rules, and budget limits.

**Graduated Oversight** assigns one of four levels per request: autonomous (no human), monitored (human watches), gated (human approves before execution), or manual (human does it). The level is selected by the receiver's orchestration layer, not the caller.

**The Trace Plane** spans all layers horizontally, providing cryptographic audit trails across agent chains via OpenTelemetry with trace integrity verification.

## Controls at a Glance

The Baseline Controls document defines 13 A2A Protocol Controls, 12 AI/ML Controls, 10 IT Infrastructure Controls, and 10 Security Controls. Each is assigned a priority:

- **P1** -- Must have before production
- **P2** -- Should have before production
- **P3** -- Should have within 90 days of production
- **P4** -- Nice to have for mature programs

## Regulatory Framework Mappings

The Security Reference maps AISI layers and controls to:

- NIST AI RMF (GOVERN, MAP, MEASURE, MANAGE functions)
- EU AI Act (Articles 9, 10, 12, 14, 15, 19, 26(6); Annex XI)
- ISO/IEC 42001 (Clauses 5-10; Annex A/B)
- CSA AI Controls Matrix

These mappings are informational, not authoritative interpretations.

## Getting Started

**Single-agent system:** Focus on Layers 1-5 and the AI/ML and IT Infrastructure controls in the Baseline Controls.

**Multi-agent, single organization:** Add the A2A Protocol Specification, implement Agent Cards and behavioral contracts, and start with the P1 A2A controls (A2A-1, A2A-2, A2A-6, A2A-7).

**Cross-organization agents:** Everything above, plus identity federation, explicit data classification, retention policies, budget limits, sub-delegation constraints, and the Security Reference's agent supply chain and third-party risk controls.

## License

[Add your license here]
