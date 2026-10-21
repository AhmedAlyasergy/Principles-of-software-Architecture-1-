# Nexus Logistics System - Architecture Repository

This repository contains the software architecture deliverables for the **Nexus Logistics System**, developed for **CSE4203: Principles of Software Architecture** at the Faculty of Computers and Information Technology, AAST.

---

## Team Members & Contributions (Contribution Table)

| Task No. | Task Name | Description / Deliverable | Team Member |
| :---: | :--- | :--- | :--- |
| **1** | System Overview | One-page description of the proposed system, problem solved, user roles, and architectural significance. | **Jana Samir** |
| **2** | Stakeholders and Concerns | Table of key stakeholders (users, admins, developers, operators, regulators) and their primary concerns. | **Jana Samir** |
| **3** | System Context | C4 Model Level 1 Context Diagram showing system boundaries, actors, and external system interactions. | **Ahmed Alyasergy** |
| **4** | Functional Requirements | Eliciting main use cases/stories (12 stories), MoSCoW prioritization, and Milestone 3 prototype flags. | **Reem Hussein** |
| **5** | Quality Attribute Scenarios | Formulating 6-part concrete, testable quality attribute scenarios (Performance, Availability, Security, etc.). | **Nour Maatouk** |
| **6** | Constraints | Defining technical, business, and organizational constraints governing system architecture. | **Nour Maatouk** |
| **7** | ASRs & Utility Tree | Ranking top requirements, constructing the Utility Tree, and prioritized ASR justification. | **Ahmed Alyasergy** |

---

# M1 — Proposal & Architectural Requirements

## 1. System Overview
Nexus is an enterprise-grade logistics and fulfillment engine designed to synchronize global retail operations. The system unifies suppliers, warehouses, and multi-channel sales platforms into a single synchronized flow using a "thick" architectural backend built with **Spring Boot** and **Firebase Cloud (Firestore)**. 

* **The Problem It Solves:** Eliminates fragmented logistics data, asynchronous stock updates, and delayed decision-making across regional hubs.
* **Who Uses It:** Branch Managers (Cairo, Alexandria, Giza), Global Managers (M00), Pricing/Order Managers, Logistics/Shipping Managers, and external Suppliers.
* **Architectural Interest:** Combines multiple business modules through shared cloud data, decoupled design patterns (Singleton, Strategy, Observer, Decorator, Factory, Command, Adapter, Bridge), and strict transaction-level validation safeguards.

---

## 2. Stakeholders and Concerns

| Stakeholder | Key Concerns |
| :--- | :--- |
| **Branch Managers** | Accurate stock quantities, timely low-stock threshold alerts, simple dashboard access, and rapid restock request approvals. |
| **Global Manager (M00)** | Real-time visibility across all regional hubs, efficient supply decisions, and instant coordination with suppliers. |
| **Pricing / Order Managers** | Accurate dynamic price calculations (Normal, VIP, Black Friday, gift wrap, insurance), multi-model order checkouts (Normal, Bulk, Subscription), and valid state cancellations. |
| **Shipping / Logistics Managers** | Correct geographic shipping method selection, reliable tracking references, and synchronization with legacy supplier systems. |
| **Developers / Maintenance Team** | High modifiability, low coupling, extendable architecture via design patterns, and clean separation of concerns. |
| **Business Owner** | Operational efficiency, minimized out-of-stock scenarios, system scalability, and adherence to the 12-week schedule. |

---

## 3. System Context
The system context is documented using the **C4 Model Level 1 Context Diagram**, illustrating Nexus as a central enterprise system interacting with key actors (Branch Managers, Pricing Managers, Global Administrators, Order Management Staff, and Suppliers) and external system dependencies (Firebase Cloud Firestore).
*(Source diagram files are available in the `/diagrams` folder).*

---

## 4. Functional Requirements
The system defines 12 core user stories prioritized using the **MoSCoW** method, with explicit mapping for Milestone 3 prototype implementation:

1. **FR-01: Branch Authentication & Navigation** — Branch Manager | **Must Have** | *Implemented in Prototype*
2. **FR-02: Real-Time Inventory Traversal** — Branch Manager / System | **Must Have** | *Implemented in Prototype*
3. **FR-03: Automated Low-Stock Alert Broadcasting** — Nexus System | **Must Have** | *Implemented in Prototype*
4. **FR-04: Restock Request & Approval Workflow** — Branch Manager / M00 | **Must Have** | *Implemented in Prototype*
5. **FR-05: Dynamic Pricing Calculation** — Pricing Manager | **Must Have** | *Implemented in Prototype*
6. **FR-06: Multi-Model Order Creation** — Order Manager | **Must Have** | *Implemented in Prototype*
7. **FR-07: Order Checkout & Lifecycle Validation** — Order Manager / System | **Must Have** | *Implemented in Prototype*
8. **FR-08: Safe Command-Based Order Cancellation** — Order Manager | **Should Have** | *Implemented in Prototype*
9. **FR-09: Geographic Shipping Method Selection** — Shipping Manager | **Must Have** | *Implemented in Prototype*
10. **FR-10: Legacy Supplier Data Synchronization** — Supplier Integration System | **Must Have** | *Implemented in Prototype*
11. **FR-11: Restock Fulfillment & Stock Updates** — Nexus System / Supplier | **Should Have** | *Implemented in Prototype*
12. **FR-12: Automated Multi-Carrier Rate Comparison** — Shipping Manager | **Could Have** | *Deferred (Future Scope)*

---

## 5. Quality Attribute Scenarios

* **Scenario 1 (Performance):** 
  * *Source:* 20 concurrent branch managers and administrators.
  * *Stimulus:* Request stock traversal or trigger a pricing calculation query simultaneously under normal load.
  * *Artifact:* User interface and Spring Boot backend REST API / Inventory & Pricing modules.
  * *Environment:* Normal operational load.
  * *Response:* The system processes the request, retrieves cloud data, computes prices/stock, and renders dashboard data.
  * *Response Measure:* 95% of requests complete with a response time of less than 1 second. *(Priority: H, M)*

* **Scenario 2 (Availability & Data Integrity):** 
  * *Source:* External Cloud Database (Firestore / Firebase Service).
  * *Stimulus:* Network interruption or temporary connection timeout occurs during a transaction processing request.
  * *Artifact:* FirebaseService connection layer and InventoryService transaction handler.
  * *Environment:* Cloud connectivity degradation.
  * *Response:* The system detects faults via connection monitoring, logs errors, and throws protected exceptions to prevent data corruption or negative stock.
  * *Response Measure:* Fault detected and logged within 3 seconds, ensuring 0 corrupted or negative inventory records. *(Priority: H, H)*

* **Scenario 3 (Security):** 
  * *Source:* Unauthorized external user or unauthenticated actor.
  * *Stimulus:* Attempts to access restricted global manager endpoints (M00) or modify active order lifecycle states directly via API.
  * *Artifact:* Session management and Spring Boot Controller security routing layer.
  * *Environment:* External threat scenario.
  * *Response:* The system authenticates requests, identifies insufficient privileges, blocks access, and logs security events.
  * *Response Measure:* 100% of unauthorized access attempts blocked instantly with zero unauthorized state modifications. *(Priority: H, M)*

* **Scenario 4 (Modifiability):** 
  * *Source:* Software Developer.
  * *Stimulus:* A request arrives to introduce a new promotional pricing strategy or additional order model (e.g., Seasonal Flash-Sale Pricing).
  * *Artifact:* Pricing Engine module (`PricingStrategy` interface and concrete implementations).
  * *Environment:* Design time / code maintenance phase.
  * *Response:* The developer implements the new strategy by adding a new class implementing the interface without modifying existing core pricing or order logic.
  * *Response Measure:* New strategy integrated, code-reviewed, and unit-tested within 1 person-day with zero regressions in existing modules. *(Priority: H, L)*

* **Scenario 5 (Testability):** 
  * *Source:* QA / Test Engineer.
  * *Stimulus:* Automated integration test suite executed after a new build increment.
  * *Artifact:* Order Management module (`OrderFactory` and `CancelOrderCommand`).
  * *Environment:* Staging / Test environment.
  * *Response:* The system executes order creation and safe cancellation workflows, exposing internal object states through logs.
  * *Response Measure:* Faults localized and test suite execution completes in under 2 minutes, achieving at least 90% path coverage. *(Priority: M, L)*

---

## 6. Constraints

* **Technical Constraint (Database):** The system must use Firebase Cloud (Firestore) as the main cloud database for global inventory persistence.
* **Technical Constraint (Backend/Frontend):** The backend must be built using Spring Boot with REST APIs connected to the frontend user interface.
* **Business Constraint (Shipment Confirmation):** Shipment updates must wait for explicit confirmation from external suppliers before showing status changes to users.
* **Technical Constraint (Legacy Integration):** The shipping adapter acts as a translator, allowing communication with legacy supplier APIs without requiring changes to core business logic.
* **Organizational Constraint (Team & Schedule):** The project must be completed by a 5-student team within the 12-week schedule, keeping the scope focused on core enterprise logistics.

---

## 7. Architecturally Significant Requirements (ASRs) & Utility Tree
The architecture is heavily driven by **Performance**, **Availability**, and **Modifiability**, mapped structurally via the **Utility Tree** (prioritized by Importance and Difficulty):
1. **Performance (H, M):** Low latency response ($<1\text{s}$) under concurrent operational load.
2. **Availability (H, H):** Robust cloud fault detection and zero-negative-stock interlocks.
3. **Modifiability (H, L):** Decoupled strategy and factory patterns enabling quick feature extensions ($\le 1$ person-day).
4. **Security (H, M):** Role-based access control protecting M00 and order lifecycle endpoints.
5. **Testability (M, L):** High automated path coverage ($\ge90\%$) for order management modules.

---
