## C. Glossary

Definitions are calibrated to usage in this guide. Arrows (→) mark closely related terms.

**Aggregate** — A cluster of domain objects treated as a single consistency unit for data changes. → *Bounded Context*

**Anemic Domain Model** — Anti-pattern: entities hold only data, behavior scattered across service layers. → *Domain Service*, *Tacit Knowledge*

**Anti-Corruption Layer** — Translation boundary insulating a domain model from external system semantics. → *Bounded Context*

**Assume-Guarantee** — Compositional verification: each component guarantees output assuming the caller meets preconditions[^333^]. → *Design by Contract*

**Bounded Context** — Semantic boundary where domain terms have consistent meaning; limits which rules may interact. → *Anti-Corruption Layer*, *Aggregate*

**Canary Deployment** — Release strategy routing small traffic percentage to a new version before full rollout. → *Feature Flag*, *Circuit Breaker*

**Chain of Responsibility** — Routing pattern: request passes through handler sequence until one processes it. → *Content-Based Router*

**Choreography** — Distributed coordination without a central controller; services react to *Domain Events* independently[^824^]. → *Orchestration*, *Saga*

**Circuit Breaker** — Fault-tolerance pattern halting requests to a failing service for a cooldown period. → *Fail-Fast*

**Cognitive Complexity** — Metric for code understandability; scores above 15 warrant mandatory review[^1126^]. → *Essential Complexity*

**Content-Based Router** — Message-routing pattern directing messages based on content, not external configuration[^791^]. → *Dynamic Router*

**Contract Testing** — Verifying that consumer expectations match provider guarantees at API boundaries. → *Design by Contract*

**Decision Table** — Tabular decision logic with condition rows, action columns, and hit policies[^967^]. → *DMN*

**Design by Contract (DbC)** — Preconditions, postconditions, and invariants attached to interfaces. → *Assume-Guarantee*, *Guard Clause*

**DMN** — OMG standard for decisions executable by analysts and systems; comprises tables, DRDs, FEEL[^135^]. → *Decision Table*, *Policy-as-Code*

**Domain Event** — Immutable record of something that happened ("OrderPlaced"), published for downstream reaction. → *Choreography*, *Transactional Outbox*

**Domain Service** — Stateless logic not belonging to any *Aggregate*, coordinating across entities. → *Anemic Domain Model*

**Durable Execution** — Orchestration persisting workflow state automatically, surviving process crashes[^903^]. → *Saga*, *Process Manager*

**Dynamic Router** — Routing pattern consulting external configuration at runtime for message destinations[^791^]. → *Content-Based Router*, *Feature Flag*

**Emergent Complexity** — Complexity from interaction of correct components; $N$ rules yield $2^N$ states[^13^]. → *Essential Complexity*, *Cognitive Complexity*

**Essential Complexity** — Inherent domain complexity that cannot be eliminated without changing the business[^1131^]. → *Emergent Complexity*

**Fail-Fast** — Validation halting at first error; trades complete reporting for minimal latency. → *Guard Clause*, *Circuit Breaker*

**Feature Flag** — Runtime toggle decoupling deployment from release; requires lifecycle governance[^795^]. → *Canary Deployment*, *Zombie Flag*

**Floating-Point Trap** — Using IEEE 754 types for money, causing compounding representation errors[^996^]. → *Calculation Logic*

**God Validator** — Anti-pattern: single validation function growing until it exceeds human reasoning threshold. → *Specification Pattern*, *Guard Clause*

**Guard Clause** — Early-return check exiting on precondition failure, flattening nested conditionals. → *Fail-Fast*, *Specification Pattern*

**Orchestration** — Centralized workflow coordination; a controller directs downstream services via commands[^903^]. → *Choreography*, *Saga*, *Process Manager*

**Outbox Pattern** — See *Transactional Outbox*.

**Policy-as-Code** — Encoding governance rules in version-controlled, machine-evaluable files (e.g., OPA Rego). → *DMN*

**Process Manager** — State-machine-driven orchestrator with explicit states, transitions, and recovery. → *Orchestration*, *Saga*, *Routing Slip*

**Routing Slip** — Message carries its own itinerary, giving orchestration visibility with decentralization[^807^]. → *Process Manager*

**Saga** — Long-running distributed transaction via local steps with compensating actions on failure[^824^]. → *Choreography*, *Orchestration*, *Durable Execution*, *Transactional Outbox*

**Specification Pattern** — Composable rule object enabling logical combination (AND, OR, NOT) of criteria. → *Guard Clause*, *God Validator*, *Decision Table*

**Strategy Pattern** — Runtime selection among interchangeable algorithms for the same operation. → *Content-Based Router*, *Dynamic Router*

**Tacit Knowledge** — Domain expertise held only in practitioners' heads; liability when they depart[^1385^]. → *Anemic Domain Model*

**Temporal Coupling** — Dependency requiring components to be available simultaneously. → *Circuit Breaker*, *Durable Execution*

**Transactional Outbox** — Atomically persists events in a database table, with a relay publishing to a bus[^856^]. → *Saga*, *Domain Event*

**Validation** — Constraint enforcement: field checks, cross-field conditions, cross-entity consistency. → *Guard Clause*, *Specification Pattern*

**WebAssembly (Wasm)** — Portable binary format for sandboxed, deterministic business logic execution[^1430^].

**Zombie Flag** — Feature flag remaining after rollout, accumulating dead branches with no purpose[^800^]. → *Feature Flag*



---