## D. Pattern Index

### D.1 Alphabetical Listing

Chain of R. (4), C. Router (4), Decision Table (7), Dec-as-Service (7), Decision Black Hole ^[A]^ (7), Dependency DAG (6), DbC (3), Distributed Monolith ^[A]^ (5), DMN/DRD (7), Durable Execution (5), D. Router (4), Excel Hell ^[A]^ (7), Fail-Fast (3), FP Money ^[A]^ (6), God Validator ^[A]^ (3), Guard Clauses (3), Hard-Coded (7), Infinite DAG ^[A]^ (6), Lookup Table (6), Money Pattern (6), P. Calc. (6), Policy-as-Code (7), P. Manager (5), Routing Slip (5), Saga (5), Specification (3), Strategy (4), Tacit Knowl. ^[A]^ (12), Tx Outbox (5), Zombie Flags ^[A]^ (4)

^A^ = anti-pattern.

### D.2 By Category

**Validation (3):** Guard Clauses, Specification, DbC, Fail-Fast | ^A^ God Validator

**Routing (4):** C. Router, Strategy, Chain of R., D. Router | ^A^ Zombie Flags

**Orchestration (5):** Saga, P. Manager, Durable Execution, Tx Outbox, Routing Slip | ^A^ Distributed Monolith

**Calculation (6):** Money Pattern, Dependency DAG, P. Calc., Lookup Table | ^A^ FP Money, Infinite DAG

**Decision (7):** Hard-Coded, Decision Table, DMN/DRD, Policy-as-Code, Dec-as-Service | ^A^ Excel Hell, Decision Black Hole

**Organizational (12):** ^A^ Tacit Knowl.

### D.3 By Tag

**Composable:** Specification (3), Strategy (4), Chain of R. (4), Saga (5), P. Manager (5), Dependency DAG (6), DMN/DRD (7), C. Router (4), Routing Slip (5)

**Externalizable:** Decision Table (7), DMN/DRD (7), Policy-as-Code (7), Dec-as-Service (7), P. Calc. (6), Lookup Table (6), Durable Execution (5)

**Testable:** All Validation (3), Decision Table (7), DMN/DRD (7), Money Pattern (6), P. Calc. (6), Lookup Table (6)

**High-risk:** Saga (5), Durable Execution (5), Tx Outbox (5), Dec-as-Service (7), D. Router (4), Hard-Coded (7)

**Anti-pattern:** God Validator (3), Zombie Flags (4), Distributed Monolith (5), FP Money (6), Infinite DAG (6), Excel Hell (7), Decision Black Hole (7), Tacit Knowl. (12)

Validation patterns are **testable**; composable patterns cluster in Orchestration and Routing.



---