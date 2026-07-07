## 10. Testing Strategies by Logic Category

Running the same test pyramid against every category of business logic is like using a multimeter for both circuit boards and foundation slabs — the tool suits one purpose precisely and the other not at all. Validation fails on invariant violations that example-based tests miss because the tester never imagined the input. Orchestration fails on ordering assumptions that mocked dependencies hide. Calculation fails on rounding edge cases at the fourteenth decimal. Each category carries distinct failure modes, and the testing strategy must match.

### 10.1 The Testing Strategy Matrix

The following matrix maps each logic category to its primary testing approach, tools, and coverage targets. A calculation module with complex JSON may supplement with approval testing; a validation module at a service boundary may add contract tests. The matrix specifies where to start, not where to stop.

| Logic Category | Primary Approach | Secondary Approach | Representative Tools | Coverage Target |
|:---|:---|:---|:---|:---|
| Validation | Property-based testing | Approval testing for error messages | Hypothesis, QuickCheck, jqwik | All cross-field invariants; 100% guard-clause paths |
| Routing | Combinatorial / pairwise testing | Approval testing for expected outputs | PICT, ACTS, ApprovalTests | All pairwise interactions per NIST research [^1278^] |
| Orchestration | Replay-based testing | BDD with Gherkin scenarios | Temporal time-skipping, Cucumber | All saga compensation paths; deterministic replay passes |
| Calculation | Approval testing | Property-based for mathematical invariants | ApprovalTests, Hypothesis | Regression baselines for all rate/price combinations |
| Decision | DMN TCK scenario testing | Mutation testing for suite quality | Camunda, Drools, PIT, Stryker | 70–80% mutation score; all table rules [^1224^] |

*Table 10.1: Testing Strategy Matrix — each logic category maps to a primary approach tuned to its specific failure modes.*

The matrix embodies a governing principle: the failure mode determines the strategy. Validation fails when invariants break under unexpected inputs — PBT generates those inputs. Routing fails on untested path combinations — combinatorial coverage reaches them. Orchestration fails on ordering — replay validates the sequence. Calculation fails on precision drift — approval captures the baseline. Decision logic fails on rule interactions — mutation testing forces the suite to exercise them.

#### 10.1.1 Validation: Property-Based Testing for Invariants

Validation logic is defined by invariants — statements that must hold for all inputs. "A discount never exceeds the original price" is an invariant; testing it with three example prices leaves the five-hundredth case unverified. Property-based testing (PBT), originating with QuickCheck and available through Hypothesis, jqwik, and fast-check, automates this aspiration [^1228^]. Define the property (e.g., $\forall x \in \text{Order}: \text{discount}(x) \leq \text{price}(x)$)), let the framework generate hundreds of inputs, and rely on automatic shrinking to reduce any failure to a minimal counterexample [^585^]. Cross-field validation — where complexity jumps from $O(1)$ to $O(2^n)$ per Chapter 3 — is where PBT delivers the greatest return.

#### 10.1.2 Routing: Combinatorial Testing for Path Coverage

Routing logic distributes $2^N$ possible paths across $N$ conditions, as established in Chapter 4. Beyond small $N$, pairwise testing provides the pragmatic middle ground. NIST research shows that the majority of interaction defects stem from two-way interactions — covering all pairs catches the bulk of failures with a fraction of the cases [^1278^]. For five boolean flags, exhaustive testing demands 32 cases; pairwise coverage requires 6–8. Approval testing complements this by capturing expected output per path — when a feature flag changes the downstream service call, the approval test flags the difference for human review [^1226^].

#### 10.1.3 Orchestration: Replay-Based Testing and BDD

Orchestration logic fails on timing, ordering, and compensation assumptions that unit tests with mocks cannot reproduce. Temporal's time-skipping environment addresses this: a workflow sleeping 30 days executes in milliseconds as the test framework advances the clock programmatically [^879^]. Saga compensation paths are tested by injecting failures at each step and verifying the rollback chain executes in reverse. Temporal's `ReplayWorkflowHistoryFromJSONFile` guarantees updated workflow code remains deterministic against recorded histories [^1231^] — insurance against the "implicit workflow knowledge" problem from Chapter 5. BDD with Gherkin closes the communication gap: Societe Generale runs 400 Cucumber scenarios in 20–30 minutes, using the Three Amigos pattern to align analysts, developers, and testers before code is written [^1256^].

#### 10.1.4 Calculation: Approval Testing for Expected Outputs

Calculation logic produces deterministic outputs from deterministic inputs — a $500 invoice with 7.25% tax always yields $36.25. This determinism makes approval testing the natural primary strategy: run the calculation, capture the output, and approve it as the baseline [^1226^]. Unlike `assertEquals`, which requires specifying every field upfront, approval testing follows "arrange, act, verify" — the Printer function scrubs flaky data (timestamps, GUIDs) and presents human-readable diffs [^1275^]. Supplement with property-based tests for mathematical invariants: "total tax equals the sum of line-item taxes" and "final amount $\geq$ subtotal minus maximum discount." The approval test catches regressions in expected outputs; the property test catches logical contradictions no one thought to baseline.

#### 10.1.5 Decision: DMN TCK and Mutation Testing

Decision logic in DMN tables demands two validation layers. The DMN Technology Compatibility Kit (TCK) provides a standardized scenario format specifying inputs and expected outputs, enabling portable test suites against any compliant engine. For a table with $n$ input columns, TCK scenarios exercise each rule row, boundary conditions, and hit policy behavior. Mutation testing then validates whether the suite actually exercises the logic or merely executes it. Tools like PIT or Stryker introduce small faults — changing $>$ to $\geq$, altering output values — and verify detection [^1224^]. The Jia and Harman study (IEEE, 2011) found ~23% of mutants are equivalent, capping achievable scores below 100% [^1224^]. For business applications, 70–80% is strong; 80%+ for payment-critical decisions. Scope mutation testing to the domain model package — a surviving mutant there represents genuine risk [^1229^].

### 10.2 Cross-Category Testing Concerns

#### 10.2.1 Testing Composed Rules: Risk-Based Sampling

Rules that pass in isolation fail when composed — the compositional floor of $2^N$ from Chapter 8. The response is risk-based sampling, not exhaustive coverage: test all pairwise interactions between rules from different bounded contexts, and test triple interactions only for rules involving financial amounts or safety conditions. Approval testing with combinatorial generation explores high-priority combinations and captures baselines for regression [^1273^]. The principle: "test assembled use cases, not isolated components" — stub only at adapter boundaries and exercise the full composition together [^1223^].

#### 10.2.2 Contract Testing: Verifying Cross-Service Boundaries

When logic spans services — a pricing engine calling a tax service, orchestration invoking a payment provider — contract testing verifies API shape and data formats without full integration tests. Pact implements consumer-driven contracts: the consumer defines expected interactions, and the provider verifies compatibility [^1242^]. Contract tests replace the subset of integration tests verifying only "does Service A still talk to Service B correctly" [^1334^]. They do not replace logic testing — Pact tests focus on API client behavior, not functional correctness [^1244^]. Use contract tests for shape compatibility; use Table 10.1 strategies for logic validation within each service.

#### 10.2.3 Mutation Testing: The Antidote to Vanity Coverage

Line coverage is a vanity metric. A test calling a discount function and asserting only that the result is not null executes 100% of lines while validating nothing. Mutation testing exposes this by introducing controlled faults — changing `>=` to `>`, inverting booleans — and checking detection [^1226^]. A 90% line coverage paired with a 50% mutation score means tests touch code without verifying behavior — the Knight Capital pattern in miniature. That $440 million loss stemmed from untested legacy paths a surviving mutant would have flagged [^1252^]. Target 70–80% mutation scores on the domain model package; below 60% indicates tests that execute without validating [^1224^].



---