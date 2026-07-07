## 13. Future Directions — Portable, Declarative, Durable

The preceding chapters mapped the territory of business logic architecture. This final chapter identifies three technology vectors converging to reshape it, each addressing a fundamental question: *where* should logic run, *how* should we express it, and *how* do we coordinate it reliably? The convergence of portable execution, declarative rule expression, and durable coordination is already measurable in production at scale.

### 13.1 Three Converging Technologies

#### 13.1.1 Portable: WebAssembly — "Where Should Logic Run?"

Shopify Functions provides the definitive production benchmark. The platform executes WebAssembly (Wasm) modules inside a sandboxed host at the edge, enforcing hard determinism: 5 MB binary limit, 5 ms execution budget, fuel-based instruction accounting, and zero I/O[^1430^]. Production Functions execute in 0.8–2.5 ms, compared to legacy Ruby Scripts that degraded to 50–200 ms during flash sales[^1430^]. The architectural significance is *location independence*: a Wasm module runs identically at the CDN edge, on a server, or in a mobile client. Shopify sunset its Ruby-based Scripts entirely in June 2026, validating Wasm as the runtime for portable business logic[^552^].

#### 13.1.2 Declarative: DMN — "How Do We Express Rules?"

The Decision Model and Notation (DMN) standard provides vendor-independent, business-readable decision models. DMN decision tables, Decision Requirements Diagrams (DRDs), and the Friendly Enough Expression Language (FEEL) form an executable framework separating *what* the business decides from *how* the system implements[^135^]. DMN models serve as both documentation and executable code — mitigating the tacit knowledge trap from Chapter 12 — and are portable between compliant engines. For moderate-to-high complexity decisions, DMN replaces proprietary BRMS and the hand-coded conditionals that become unmaintainable nests[^266^].

#### 13.1.3 Durable: Temporal — "How Do We Coordinate?"

Temporal (forked from Uber's Cadence) treats workflow code as a durably executed program: the full running state persists automatically, enabling recovery or replay at any point[^817^]. When a worker fails, another resumes from the exact interruption point by replaying the event history[^824^]. This eliminates state-machine boilerplate, manual retry logic, and reconciliation code[^848^]. Temporal's time-skipping test environment allows month-long workflows to execute in seconds during testing[^879^]. The shift is from *configuring* workflows (BPMN) to *programming* them in general-purpose languages with durability guaranteed by the runtime.

### 13.2 The Unified Architecture Vision

These three technologies occupy orthogonal dimensions. Wasm addresses execution environment, DMN addresses rule expression, and Temporal addresses process coordination. Together they form an architecture for business logic that is location-independent, version-controlled, and automatically resilient.

| Dimension | Technology | Core Question | Key Capability | Production Evidence |
|:---|:---|:---|:---|:---|
| **Portable** | WebAssembly | Where should logic run? | Sandboxed execution across edge/server/mobile with deterministic budgets | Shopify: 0.8–2.5 ms checkout logic; 5 ms hard ceiling[^1430^] |
| **Declarative** | DMN | How do we express rules? | Business-readable, vendor-independent, executable decision models | Capital One: complexity × change-rate selection[^266^]; OMG open standard[^135^] |
| **Durable** | Temporal | How do we coordinate? | Automatic persistence, failure recovery, deterministic replay | Uber (Cadence), Shopify; time-skipping tests[^817^][^879^] |

The convergence operates at the integration layer. A Temporal workflow might invoke a DMN decision service for a risk-rating calculation, then execute the resulting rule set as a Wasm module at the edge. If the edge node fails, Temporal resumes without data loss. Analysts modify the DMN table without touching workflow code. The same Wasm module runs at the edge during peak traffic and on the server during batch processing.

The implications for prior chapters are substantial. Composability (Chapter 8) becomes safer when Wasm sandboxes enforce hard boundaries. The tacit knowledge trap (Chapter 12) is mitigated when DMN tables serve as self-documenting, executable specifications. Deployment safety (Chapter 12) is strengthened when Temporal's replay testing catches non-deterministic changes before production. Automatic resilience means recovery from failures that previously demanded manual intervention, reducing the operational burden behind risky deployments.

This architecture is emerging, not speculative. Shopify runs Wasm at the edge. Financial services deploy DMN as Decision-as-a-Service APIs[^1013^]. Uber, Netflix, and hundreds of organizations run Temporal in production. The convergence of all three remains aspirational for most, but the trajectory is clear: business logic is becoming as portable, declarative, and durable as the infrastructure it runs on.



---


# Appendix A: Quick Reference Cards

Reference for patterns (Chapters 3–7), complexity governance (Chapter 9), and testing (Chapter 10).