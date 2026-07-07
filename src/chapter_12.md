## 12. Evolution, Anti-Patterns & Organizational Alignment

Business logic is institutional memory in executable form. Unlike infrastructure code documenting *how* a system works, business logic encodes *why* decisions were made — and the people who made them often leave before the code does. Conway's Law runs through every section: when the organization changes without the logic following, misalignment dominates.

### 12.1 The Tacit Knowledge Trap

#### 12.1.1 Why Rewrites Fail

An estimated 70% of large-scale rewrites fail because hidden complexity is underestimated[^1342^]. Edge cases in rounding rules, exception handling for corner cases, and guard clauses from regulatory scenarios years past — these rules "have been operating covertly for years"[^1342^]. The Knight Capital disaster exemplifies this: a deprecated order type called Power Peg lay dormant for seven years until a reused flag bit activated it on one missed server, losing $440 million in 45 minutes[^579^].

#### 12.1.2 Making Tacit Knowledge Explicit

Three practices counter the trap. **BDD discovery workshops** produce executable specifications — living documentation "as reliable as the code, but much easier to read and understand"[^1385^]. Teams with automated examples double the "great software" rating (26% vs. 13%)[^1386^]. **Rule harvesting** extracts logic from legacy systems through automated analysis and SME validation[^1006^]. **Architectural Decision Records (ADRs)** capture the *why* of choices, stating the domain assumption that motivated each rule.

### 12.2 Anti-Patterns Catalogue

This section consolidates ten anti-patterns from preceding chapters into a single reference, including three — Anemic Domain Model, Feature Flag Abuse, and Tacit Knowledge Trap — that emerge at the organizational layer.

**Table 12.1: Ten Anti-Patterns in Business Logic**

| Anti-Pattern | Symptoms | Consequences | Remedy | Source Ch. |
|:---|:---|:---|:---|:---:|
| **God Validator** | Single function >500 lines; cognitive complexity exceeds reasoning threshold | Incidents from interaction effects invisible in review | Decompose at 7 rules using Specification pattern[^754^] | 3 |
| **Zombie Flags** | Flags remain post-rollout; branches maintain dead paths | $2^N$ configuration explosion[^795^]; 15–25 ms latency overhead[^793^]; dormant code activates on boundary failures | Owner, end date, removal ticket at creation[^800^] | 4 |
| **Distributed Monolith** | Lock-step deployment; bi-directional dependencies; shared databases | Coordinated releases required; cascading failures | Extract boundaries before services; explicit Process Manager[^847^] | 5 |
| **Floating-Point Money** | IEEE 754 types for financial calculations | Unreconciled balances; compounding rounding errors; regulatory penalties | Fixed-point or Decimal types[^996^][^997^]; defer rounding to output | 6 |
| **Excel Hell** | Critical rules in spreadsheets via email; no source of truth | Different numbers for same metric; no reproducibility | DMN tools with version control and governance[^526^] | 7 |
| **Infinite DAG** | Self-referential or mutually recursive calculation definitions | Unbounded recursion; engine hangs on specific inputs | Static analysis; execution timeouts; max recursion depth | 6 |
| **Decision Black Hole** | Opaque, unversioned rule store; decisions not explainable | Regulatory audit failures; inexplicable customer decisions | Rules as code: version control, tests, pipelines, audit trails[^994^] | 7 |
| **Anemic Domain Model** | Entities are data bags; behavior in procedural service scripts | Rules scattered across methods; duplicate logic | Move behavior to entities; Domain Model when justified[^30^] | 2, 9 |
| **Feature Flag Abuse** | Flags used for core business logic; permanent configuration | Rules invisible to domain experts; test matrices explode | Reserve flags for release management; externalize rules to DMN[^643^] | 4 |
| **Tacit Knowledge Trap** | Rules with no documented rationale; SME departure triggers crises | Rewrites miss edge cases; 70% rewrite failure rate[^1342^] | BDD workshops, rule harvesting, ADRs, executable specs[^1385^] | 12 |

At the code layer, God Validator and Floating-Point Money are implementation pathologies — local mistakes with local fixes. At the system layer, Distributed Monolith, Zombie Flags, and Infinite DAG are integration pathologies. At the organizational layer, Excel Hell, Decision Black Hole, Anemic Domain Model, and Tacit Knowledge Trap are governance pathologies. Feature Flag Abuse is cross-layer: deployment convenience becomes a configuration crisis. Across all ten, a reasonable short-term decision compounds into a long-term liability because no one owned the exit strategy.

### 12.3 Refactoring Triggers

#### 12.3.1 When to Migrate

Refactoring responds to measurable thresholds. **Complexity threshold exceeded:** Cognitive Complexity exceeding 15 in over 20% of business logic functions indicates systemic decay[^26^]. Microsoft Research found code churn correlates with defect density at Pearson r=0.889[^129^]. **Change frequency increased:** A rule changing twice per month that previously changed twice per year has outgrown its pattern. Fowler's principle: migrate from Transaction Script to Domain Model when "business logic complexity justifies it"[^30^]. **Team structure changed:** Conway's Law constrains architecture to mirror communication structures[^375^]; when teams reporting to different directors share a bounded context, the boundary is a political fault line.

**Table 12.2: Safe Migration Patterns**

| Trigger | Pattern | Verification | Rollback |
|:---|:---|:---|:---|
| Component replacement | **Branch by Abstraction** — abstraction layer, gradual client migration[^1412^] | A/B comparison behind feature flags | Revert abstraction to old implementation |
| Whole-system replacement | **Strangler Fig** — new system alongside legacy, incremental transfer[^1342^] | Shadow mode: both run, outputs compared[^1345^] | Route to legacy until parity verified |
| High-risk rule change | **Dark Launch** — new logic runs in prod, results logged only | Discrepancy rate alert between old and new | Disable dark-launch routing |
| Schema change for rules | **Expand and Contract** — introduce new structure, dual-write, migrate, remove old[^1376^] | Each step reversible; backward compatibility always | Roll back any single step |

#### 12.3.2 Safe Migration and Deployment Safety

Branch by Abstraction operates at the component level; Strangler Fig at the system level[^1408^]. Both enable parallel running — during which "hidden business logic — edge cases, rounding rules, exception handling — that has been operating covertly for years comes to light"[^1342^]. Dark launch extends this: new logic executes against real traffic with outputs compared but not served.

Knight Capital lost $440 million because one of eight servers ran stale code[^579^]. Nygard observed that "the most effective patterns to combat cascading failures are Circuit Breakers and Timeouts"[^1418^]. Three patterns are non-negotiable: **blue-green deployment** with atomic switch and instant rollback[^1362^]; **canary deployment** to a small subset before expansion; **circuit breakers** that trip on failure threshold and fail fast[^1418^]. Knight Capital's script silently failed on SSH errors, then reported success[^601^] — fix communication before software.



---