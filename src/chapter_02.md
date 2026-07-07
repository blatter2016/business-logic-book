## 2. A Taxonomy of Business Logic

Every codebase contains business logic, but not all business logic is the same. A pricing algorithm that recomputes thousands of times per hour differs fundamentally from a validation rule unchanged for three years. A kill switch activated during an outage differs from a tax calculation reconciling across forty jurisdictions. Treating these as a single concern — "business rules" — is why so many systems accumulate the compositional complexity described in Chapter 1.

This chapter establishes the classification system the remaining chapters depend upon. The taxonomy you apply determines which patterns you reach for, which metrics you track, and which teams own each rule. The frameworks that follow synthesize Gartner's pace-layered strategy [^639^], the OMG's SBVR [^38^], the DMN standard [^135^], and decades of operating-systems research into a practical decision tool.

### 2.1 Five Core Categories

After reviewing the Business Rules Group's structural/operative/derivation split [^125^], SBVR's necessity vs. obligation distinction [^38^], and DMN's decision-type classification [^135^] — this guide organizes business logic into five categories defined by what the logic *does* rather than how it is expressed.

**Validation Logic: gates, guards, and constraint enforcement.** Validation is the most common and most underestimated category. It begins with simple field-level checks and escalates through cross-field conditional validation into combinatorial territory where $n$ independent checks balloon into $2^n$ interaction states [^716^]. A payment system with five independent validation checks has five test cases. Add two cross-field conditional validations ("if payment method is wire transfer, amount must exceed minimum threshold") and the state space explodes. Validation is the gateway to compositional complexity: every distributed saga begins with a validation rule that grew conditional dependencies.

**Routing and Fanout Logic: branching, strategy selection, dynamic dispatch.** Routing logic determines which downstream path a request follows. Payment method selection, geographic dispatch, and feature-flag-based branching all fall here. The defining characteristic is that each routing decision doubles the testable state space [^795^]. A service with three independent routing dimensions has $2^3 = 8$ possible paths — assuming independence. When routing decisions interact, path coverage becomes practically unattainable.

**Orchestration and Coordination Logic: long-running processes, sagas, compensation.** This is the most complex category. Orchestration manages multi-step processes spanning services, time, and failure domains: order fulfillment, claims processing, loan origination. Unlike validation or routing, orchestration has state — it remembers where it is and what must happen next. The failure modes are severe: "implicit workflow knowledge scattered across services, understood by no one, documented nowhere" is a common pathology in mature systems. Compensation logic (undoing partial work when a step fails) adds essential complexity that cannot be eliminated.

**Calculation and Transformation Logic: pricing, scoring, tax, rating.** This category is computationally intensive with precision requirements. Insurance premiums, credit risk scores, sales tax, and currency conversion share two properties: the mathematics is domain-specific and unforgiving of rounding errors, and rules change on schedules driven by external entities (regulators, rating agencies, tax authorities). Calculation logic often appears simple — "multiply base rate by risk factor" — but hides jurisdictional variation that produces essential complexity no refactoring can remove.

**Decision Management Logic: rulesets, decision tables, policy evaluation.** This is the category where business users need direct control. Eligibility rules, underwriting guidelines, approval hierarchies, and promotional policies are expressed as explicit decision structures that non-engineers must read and modify. The OMG DMN standard [^135^] exists because this category demands notation that is simultaneously executable and business-readable. Decision management is distinguished by governance requirements: who can change it, how it is versioned, and how you prove regulatory compliance.

### 2.2 The Change-Frequency Dimension

The five categories describe what logic does. Equally important is how often it changes. A validation rule checking email format may be stable for years; a promotional eligibility rule may change weekly. Applying the same architectural treatment to both is a common source of mis-engineering.

#### 2.2.1 Gartner's Pace-Layered Strategy Applied to Business Logic

Gartner's pace-layered strategy divides the application portfolio into three layers by rate of change: Systems of Record (years), Systems of Differentiation (months), and Systems of Innovation (weeks) [^639^]. The model adapts directly to business logic classification. Systems of Record logic changes slowly — core entity definitions, structural constraints, regulatory foundations. SBVR calls these *structural rules*: claims of necessity "true by definition" [^38^]. Systems of Differentiation logic changes at medium frequency — pricing strategies, eligibility criteria, workflow routing. The Business Rules Group classified these as *action assertions* and *derivations* [^125^]. Systems of Innovation logic changes fastest — experimental features, A/B variants, promotional campaigns. DMN distinguishes these as operational decisions with high frequency and local scope [^645^]. The mapping is orthogonal to the five categories. A validation rule can be structural (email format, rarely changing) or operational (promotional code validity, changing weekly). The categories and change frequency form independent dimensions.

#### 2.2.2 Policy vs. Mechanism Separation

The separation of policy (what should be done) from mechanism (how it is done) was pioneered in the Hydra operating system at Carnegie-Mellon in the 1970s [^682^] and has since influenced microkernels from Mach to seL4. Business rules are policies — encoding decisions about discounts, risk, approvals. The surrounding code (HTTP handlers, database adapters, message serializers) is mechanism. Separating them allows policies to evolve independently. Manchester Business Rules Management research established business rules as "volatile concepts" — aspects of systems most prone to change [^666^]. Lehman's First Law states that a program reflecting external reality must undergo continual change or become less useful [^688^]; his Second Law adds that complexity increases unless actively reduced [^688^]. Policy-mechanism separation is the architectural response. The Common Closure Principle provides practical guidance: "classes that change for the same reasons and at the same times should be gathered into components" [^745^]. The Stable Dependencies Principle adds that dependencies should flow toward stability [^750^].

#### 2.2.3 The Change Characteristics Matrix

Synthesizing the pace-layered model, policy-mechanism separation, and feature flag lifespan classification [^643^] produces a three-dimensional classification: Logic Type × Change Frequency × Change Trigger. Table 2.1 maps these dimensions to implementation patterns.

**Table 2.1 — Change Characteristics Matrix**

| Logic Type | Change Frequency | Change Trigger | Typical Owner | Implementation Pattern |
|---|---|---|---|---|
| Validation (structural) | Years | Regulatory, standards | Architecture team | Schema constraints, type systems, compile-time checks |
| Validation (operational) | Weeks–months | Product, marketing | Product team | Specification pattern, configurable validators |
| Routing | Months | Business strategy | Engineering lead | Strategy pattern, short-lived feature flags [^643^] |
| Orchestration | Months–years | Process redesign | Platform team | Workflow engine, saga pattern, durable execution |
| Calculation (regulated) | Years | Regulatory change | Compliance team | Externalized rule tables, versioned formula libraries |
| Calculation (commercial) | Weeks–months | Market conditions | Product + Finance | Parameterized engines, DMN decision services [^135^] |
| Decision management | Days–weeks | Policy, risk, promotion | Business analysts | DMN, BRMS, rules-as-code with governance |

The matrix resolves common misallocations. Validation logic changing weekly should not be baked into compiled schema definitions; it needs the Specification pattern or configurable rule engines. Decision logic changing daily should never be embedded in application code — it requires externalization through DMN or a BRMS. Conversely, structural validation should not be managed through dynamic toggle systems; feature flags should not carry core business logic because they create availability dependencies on external services for rules that should be stable [^643^]. The Change Trigger column determines governance: regulatory changes require audit trails, while product experiments need A/B testing infrastructure.

### 2.3 Essential vs. Accidental Complexity

Fred Brooks distinguished essential complexity — inherent to the problem domain — from accidental complexity introduced by design choices [^1131^][^1132^]. A tax engine handling fifty jurisdictions has high essential complexity; the same engine scattered across twelve microservices with inconsistent data models has high accidental complexity. The distinction is a prerequisite for every architectural decision in this guide.

#### 2.3.1 Essential Complexity: Inherent to the Domain

Essential complexity arises from the business domain itself. Multi-jurisdictional tax calculations, insurance underwriting with actuarial tables, regulatory compliance reconciling conflicting requirements — none of this can be simplified without changing the business. Ann Campbell, author of Cognitive Complexity, notes that "there shouldn't be a limit for an application" because essential complexity varies by domain [^1126^]. This justifies elevated thresholds: a tax calculation with Cognitive Complexity of 25 may be appropriate if it encodes twenty-five distinct jurisdictional rules; the same score in an input validator signals design failure. The Cynefin framework offers a related lens: complicated domains accommodate structured complexity, while complex domains require experimental approaches [^638^][^635^].

#### 2.3.2 Accidental Complexity: Introduced by Poor Design

Accidental complexity could be eliminated by better design. A tax engine implemented as a distributed saga with implicit state transitions accumulates accidental complexity atop its essential complexity. Shopify reached a similar conclusion: their scaling challenges were not inherent to e-commerce but arose from architectural choices creating unnecessary coupling. "Boundaries, not distribution, were the fix" [^1295^]. The Boeing 737 MAX case illustrates the catastrophic cost of misclassification: the MCAS system was software compensation for airframe changes — accidental complexity layered atop flight dynamics. When it failed, it replaced engineering rigor with business logic that pilots were not informed existed [^360^][^361^].

#### 2.3.3 The Essential/Accidental Assessment

Before selecting any pattern, classify the complexity you face. Table 2.2 provides a decision prerequisite.

**Table 2.2 — Essential/Accidental Assessment**

| Assessment Criterion | Essential Complexity (Accommodate) | Accidental Complexity (Eliminate) |
|---|---|---|
| Source | Domain rules, regulations, market structure | Implementation structure, coupling, duplication |
| Survives a rewrite? | Yes — it is the business | No — it is an artifact of current design |
| Domain expert can explain? | Yes, in business terms | No — they find workarounds |
| Changes with the domain? | Yes | No — persists across domain changes |
| Appropriate response | Elevated thresholds, targeted patterns, externalized rules | Refactoring, boundary redesign, removal |
| Risk of wrong classification | Under-engineering: brittle systems, compliance gaps | Over-engineering: unnecessary abstractions |

The right-hand column warrants particular attention. Teams routinely mistake poor structure for domain complexity, accepting elevated thresholds for problems a refactoring would resolve. The criterion "Would the complexity survive a rewrite?" is the most discriminating: a clean-sheet redesign facing the same challenge signals essential complexity; a rewrite that would eliminate it signals accidental complexity that should be treated as technical debt.

Applied consistently, this assessment prevents two common failures. The first is over-engineering: applying saga patterns and DMN externalization to simple CRUD workflows whose only real problem is poor naming. The second is under-engineering: accepting a Cognitive Complexity score of 40 in a tax module as "just how the domain is" when the actual problem is twenty unrelated rules in a single function — a classification error Table 2.2 catches at the "Can a domain expert explain it?" criterion. The chapters that follow assume this assessment has been performed; each pattern specifies which complexity type it addresses.



---