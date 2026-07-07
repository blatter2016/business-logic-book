## A. Quick Reference Cards

### A.1 Pattern Quick Reference

| # | Pattern | Cat. | When to Use | Key Metric | Related |
|---|---------|------|-------------|------------|---------|
| 1 | Guard Clauses | Val. | Precondition checks scattered | Cog. complexity > 10 | Specification, DbC |
| 2 | Specification | Val. | Rules need composability | > 3 interdependent rules | Guard Clauses, Dec. Table |
| 3 | Design by Contract | Val. | Invariants must survive refactoring | Churn > 15%/release | Guard Clauses, Fail-Fast |
| 4 | Fail-Fast/Collect-All | Val. | UX demands all errors at once | Failure rate > 5% | Guard Clauses, Specification |
| 5 | Content-Based Router | Rout. | Message type determines pipeline | Cyclomatic > 8 | Dyn. Router, Chain of Resp. |
| 6 | Strategy | Rout. | Multiple interchangeable algorithms | > 3 branches | CBR, Dyn. Router |
| 7 | Chain of Responsibility | Rout. | Multiple handlers, order matters | > 4 handlers | CBR, Process Manager |
| 8 | Dynamic Router | Rout. | Rules change faster than handlers | Churn > 20%/qtr | CBR, DMN |
| 9 | Saga | Orch. | Long-running tx across boundaries | > 3 distributed ops | Process Mgr, Durable Exec. |
| 10 | Process Manager | Orch. | Workflow needs state machine | > 5 states | Saga, Routing Slip |
| 11 | Durable Execution (Temporal) | Orch. | Must survive crashes | Retry rate > 10% | Saga, Outbox |
| 12 | Transactional Outbox | Orch. | Event publish atomic with DB | Inconsistency > 0.1% | Saga, Durable Exec. |
| 13 | Routing Slip | Orch. | Fixed sequence, varying steps | > 6 permutations | Process Mgr, Chain of Resp. |
| 14 | Money Pattern | Calc. | Financial precision required | Defect rate > 0.5% | Param. Calc., Lookup |
| 15 | Dependency DAG | Calc. | Complex calc interdependencies | > 8 steps | Param. Calc., Lookup |
| 16 | Parameterized Calculation | Calc. | Formula changes, structure stable | Churn > 25%/qtr | DAG, Lookup |
| 17 | Lookup Table | Calc. | Discrete business-maintained mappings | > 10 entries/qtr | Param. Calc., Dec. Table |
| 18 | Hard-Coded Rules | Dec. | Simple stable rules (< 5 conditions) | ≤ 3 rules, churn < 10%/yr | Dec. Table, DMN |
| 19 | Decision Table | Dec. | Moderate condition-action rules | 4–20 rules, 2–6 dims | Hard-Coded, DMN |
| 20 | DMN with DRD | Dec. | Complex decisions, business-IT collab | > 20 rules or > 3 deps | Dec. Table, DaaS |
| 21 | Policy-as-Code (OPA) | Dec. | Auth distributed across services | Changes > monthly | DMN, DaaS |
| 22 | Decision-as-a-Service | Dec. | Centralized, versioned logic | > 3 consumers | DMN, PaC |

**Orchestration:** Saga for simple rollback; Process Manager for discrete long-lived states; Durable Execution when the platform handles retries and persistence.

## A.2 Complexity Thresholds Quick Reference

Defaults apply broadly. Elevated requires coverage and mutation defaults met first.

| Metric | Default | Elevated | Category | Action Trigger |
|--------|---------|----------|----------|----------------|
| Cognitive Complexity | ≤ 15 | ≤ 25 | All | Review + refactor in 2 sprints |
| Cognitive Complexity | ≤ 10 | ≤ 18 | Val, Rout | Refactor; no override |
| Cyclomatic Complexity | ≤ 10 | ≤ 20 | Orch, Calc | Alert; block at 1.5× |
| Cyclomatic Complexity | ≤ 8 | ≤ 15 | Dec | Alert + arch review |
| Code Churn | Trend | Baseline + 2σ | All | Retro at 2σ above 12-wk mean |
| Mutation Score | ≥ 70% | ≥ 80% | Calc, Dec | Block below target |
| Mutation Score | ≥ 60% | ≥ 70% | Val, Rout | Warn; block at −10 pts |
| Line Coverage | ≥ 80% | ≥ 90% | Orch | Alert; lead sign-off exempt |

Defect density correlates more with the complexity–coverage interaction than complexity alone. Apply elevated only after two stable cycles under defaults.

## A.3 Testing Strategy Quick Reference

Pairings match dominant failure modes: boundaries (validation), sequencing (orchestration), rule interaction (decisions).

| Category | Primary | Secondary | Tools | Coverage | Mutation |
|----------|---------|-----------|-------|----------|----------|
| Validation | Property-based | Example-based | Hypothesis, QuickCheck, jqwik | ≥ 85% line | ≥ 60% |
| Routing | Combinatorial | Approval | PICT, ACTS, ApprovalTests | ≥ 80% line | ≥ 60% |
| Orchestration | Replay-based | BDD | Temporal Server, Cucumber | ≥ 90% line | ≥ 70% |
| Calculation | Approval | Property-based | ApprovalTests, Hypothesis | ≥ 90% line | ≥ 80% |
| Decision | DMN TCK | Mutation | Camunda DMN, PIT, MutPy | ≥ 85% DMN | ≥ 80% |

The 80% mutation targets for calculation and decision (vs. 60%) reflect defect amplification: an off-by-one or misordered rule generates thousands of bad outcomes. DMN TCK verifies engine conformance to the OMG spec; mutation testing validates your rule set.



---