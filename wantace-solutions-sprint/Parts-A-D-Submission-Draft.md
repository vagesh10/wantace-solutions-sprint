# Wantace Solutions Sprint — Assignment Submission (Parts A–D)

**Candidate:** Vagesh Kumar Surla  
**Role:** Solutions Engineer Intern

**Part E video presentation:** [Watch the recorded video on Google Drive](https://drive.google.com/file/d/1bQpXwVWlyXD0qi0WbSCXlv-0UkGlTt3B/view?usp=sharing)  
**GitHub repository:** [vagesh10/wantace-solutions-sprint](https://github.com/vagesh10/wantace-solutions-sprint)

This document contains Parts A–D. **Part A is incomplete** because the first-hand audit could not be conducted; the section uses secondary research only. **Part D is provisional** and should be finalized against genuine audit findings or with Wantace's approval.

\newpage

# Part A — Business Audit: Secondary Research Only

**Status: Not a completed first-hand audit.** I was unable to reach a business owner/manager for the required 20-minute interview during the festival period. This section is secondary research and should be accepted as an alternative only if Wantace approves.

## Business profile — published information

- **Business:** Sampurna Tailoring
- **Owner named in the profile:** Pramila Naik
- **Location:** Mailijharan village, Kashipur Block, Odisha
- **Activity:** Tailoring; the public profile describes expansion into fancy dresses and other clothing items.
- **Reported monthly turnover:** ₹32,000; the profile also reports one employment position generated.
- **Source:** Entrepreneurship Development Institute of India (EDII), “Pramila Naik – Sampurna Tailoring,” https://ediindia.org/project-programme-st/pramila-naik-sampurna-tailoring/ (accessed 18 October 2026).

These are facts reported by the public source, not personal observations. It does not state order volume, order intake channels, tracking tools, role ownership, or the most frequent operational problem.

## Order workflow and current tools

**Not verified.** The source does not explain how an order is captured or tracked, who measures/cuts/stitches/checks a garment, how alterations are handled, or how delivery and payment are recorded. The following is only an interview hypothesis, not an observed process:

Customer enquiry → confirm garment/design/measurements/due date → record order and advance → cutting and stitching → fit/quality check and alterations → notify customer and hand over garment → collect balance and close order.

## Main problem, impact and direct quotes

**Unknown.** The source does not identify the business’s most frequent operational issue or quantify time/money lost. No owner interview took place, so two direct quotes are unavailable and none have been invented.

## Evidence and contact permissions

- Hand-drawn verified process map: **not collected**.
- Two photos of actual tracking methods, with permission and personal data blurred: **not collected**.
- Owner’s phone number and permission to share it with Wantace: **not obtained**; no private contact information is included.

**To complete the required audit:** conduct the 20-minute owner/manager interview; validate order volume, intake, stage ownership and tracking tools; record the main issue and its real impact; capture two verbatim quotes; draw the actual process map; take two permitted photos; and ask permission before sharing the business contact details.


\newpage
# Part B — Vardhan Steelware: Phase 1 Requirements

**Solutions Sprint · Draft for discovery and engineering review · 18 October 2026**

## 1. Problem statement

Vardhan Steelware’s immediate problem is not a lack of advanced analytics; it is the lack of a trusted, shared view of each order’s current stage, blockers, and dispatch balance. Suresh says, “pata nahi chalta maal kahan atka hai” and that he must call Ramesh because “Ramesh ki diary mein sab hai.” The previous ERP ran for three months before shop-floor staff stopped using it, so adoption and minimal data entry are core requirements.

## 2. What the supplied evidence shows

| Finding | Evidence in supplied pack | Requirement / interpretation |
|---|---|---|
| Order identity / possible duplicate | Gupta Traders’ Kadai No. 10 appears on 1 Sep as 200 pcs dispatched and again as 200 pcs pending (“repeat msg?”). Jain Crockery’s Kadhai No. 10, 150 pcs at ₹192, appears on 2 Sep and 4 Sep (“again sent on whatsapp”). | These may be duplicate entries or separate orders; confirm before merging. Preserve source references and support duplicate review. |
| Missing customer / incomplete record | A 3 Sep row has no party name: Thali 12 inch, 20 dozen, ₹600, dispatched. | Flag the record for correction; do not silently assign a customer. |
| Unit and rate inconsistency | Verma Utensils’ Kadhai 10 no. is 180 kg, pending, with no rate (“rate confirm karna hai”). | Require unit and rate validation. Do not assume kg and pieces are interchangeable. |
| Payment hold | Sharma Bartan’s 30 dozen glasses are on hold; remarks show old dues of ₹1.8 lakh. WhatsApp says the order should be held until previous payment. | Record hold reason, authority, and release status explicitly with an audit trail. |
| Partial dispatch and return | Mehra Kitchenware’s 300 Kadai order shows 180 dispatched and 120 remaining; the next entry records 120 dispatched. Sharma also has a -6 dozen return due to dents. | Represent dispatches and returns as separate transactions; do not rely on negative order quantity as the only return record. |
| Polishing reconciliation | WhatsApp records 420 kg sent to Pappu and 1,180 pieces received; a later message reports a 6 kg shortage. Bansal’s 120 Tope remain pending for polishing in repeated rows. | Track sent/received quantities with their respective units, discrepancy, age, and resolution. Define the 6 kg reconciliation rule with the client. |
| Client claims vs evidence | Suresh estimates 60–70 orders monthly; Kunal says online is growing 30% monthly, while Suresh says it is under 5% of business. The previous ERP was abandoned after three months. | Validate order volume, the basis of online growth, and adoption constraints; do not silently reconcile conflicting statements. |

## 3. Current process (as understood; confirm on next call)

Distributor / online order → Kunal records order in Google Sheet → production → outsourced polishing with 4–5 contractors → receive and reconcile count/weight (Munshi ji keeps accounts) → packing and dispatch (Ramesh) → payment/hold tracking (Suresh and Munshi ji).

Exact ownership of production updates, quality checks, returns, and order closure is not yet confirmed.

## 4. Phase 1 scope

| Include in Phase 1 | Explicitly out for Phase 1 |
|---|---|
| Unique order ID; customer and item records; validation for quantity, unit, and rate. | AI sales forecasting and advanced predictive dashboards. |
| Simple stage, owner, last-updated timestamp, blocker reason, due date, and a mobile-friendly read view for Suresh. | Full ERP replacement or broad accounting migration. |
| Multiple order items, partial dispatches, returns, and remaining balance. | Complex Amazon/Flipkart integrations; online orders can initially be entered manually. |
| Polishing batch: contractor, quantity sent and unit, quantity received and unit, discrepancy, and resolution status. | Automated conversion between kg and pieces without a verified product-specific conversion rule. |
| Payment hold flag and release authority; basic audit trail for changes. | Automated credit decisions, pricing, or unconfirmed inventory/accounting integrations. |

**Rationale:** Ship the smallest usable workflow, pilot it with a few real orders, and confirm adoption before expanding. The client mentions a Diwali-season deadline, but the exact required date and feasible scope must be confirmed; do not promise delivery until engineering and the client validate it.

## 5. Core data model and relationships

| Entity | Key fields (illustrative) |
|---|---|
| Customer | `customer_id` (PK), name, contact, payment_status |
| Order | `order_id` (PK), `customer_id` (FK), source, order_date, due_date, status, blocker_reason, created_by, updated_at |
| OrderItem | `item_id` (PK), `order_id` (FK), product, specification, ordered_qty, unit, rate, validation_status |
| ProductionBatch | `batch_id` (PK), `item_id` (FK), stage, assigned_to, status, started_at, completed_at |
| PolishingBatch | `polish_id` (PK), `batch_id` (FK), contractor, sent_qty, sent_unit, received_qty, received_unit, shortage_qty/unit, resolution_status |
| Dispatch | `dispatch_id` (PK), `order_id` (FK), dispatch_date, qty, unit, reference |
| Return | `return_id` (PK), `order_id` (FK), `item_id` (FK), qty, unit, reason, date, disposition |
| Payment / Hold | Payment: `payment_id`, `customer_id`, amount, date, reference. Hold: `hold_id`, `order_id`, reason, created_by, released_by, released_at. |

Relationships: one Customer → many Orders; one Order → many OrderItems, Dispatches, and Holds; one OrderItem → many ProductionBatches and Returns; a ProductionBatch may have a PolishingBatch. Store units explicitly and preserve original imported/source rows for review. Exact accounting integration and kg-to-piece reconciliation rules require confirmation.

## 6. Users and phone experience

| Role | Permissions / experience |
|---|---|
| Suresh — owner | Read-only overview of orders by stage, overdue orders, payment holds, blockers, and last update; drill into an order on phone. |
| Kunal — order coordinator | Create/edit orders and customer/item details; review duplicate candidates and missing fields; monitor due dates. |
| Ramesh — dispatch | Update assigned stage, quantities dispatched, partial balance, and dispatch reference with minimal taps. |
| Munshi ji — accounts | Flag payment holds, record payment references, view returns, and review polishing discrepancies. |
| Polishing contractors | No login in initial pilot unless discovery shows a clear need; internal staff record sends/receipts first. |

## 7. Three morning reports for Suresh

1. **Orders pending by stage:** order/customer, current stage, owner, days at stage, due date, and last update.
2. **Blocked orders:** payment hold, missing customer/rate/unit, awaiting polishing, or other blocker; show who must resolve it and the next action.
3. **Outsourced polishing reconciliation:** contractor, batch, quantity/unit sent and received, recorded discrepancy, age of discrepancy, and resolution status.

## 8. Questions for the next client call

1. **Suresh:** What exact date is needed before Diwali, and which two outcomes matter most on day one?
2. **Suresh:** Who has final authority to release payment holds and approve corrections?
3. **Kunal:** Is the Google Sheet the source of truth? Who creates order IDs, and how are WhatsApp orders reconciled?
4. **Kunal:** Are repeated rows duplicate messages or separate orders? Can we inspect source messages for the Gupta and Jain examples?
5. **Ramesh:** What are the actual production, quality, polishing, packing, and dispatch stages, and which can he update on a phone?
6. **Ramesh:** How are partial dispatches, damaged goods, and returns currently recorded?
7. **Munshi ji:** How are customer balances, payment holds, and returns reflected in Tally today?
8. **Munshi ji:** How are kg sent and pieces received reconciled for polishing, and what does the reported 6 kg shortage represent?
9. **All users:** Why did the previous ERP fail in day-to-day use, what devices/connectivity are available, and who will own training and daily adoption?

## 9. Biggest risk and mitigation

**Risk:** Low adoption and unreliable data, repeating the 2023 ERP failure.

**Mitigation:** Co-design with Ramesh and Munshi ji, keep updates to a few essential fields, make the owner view read-only and phone-friendly, pilot a small set of orders, review usage and errors weekly, and expand only after users confirm that the workflow works.

**Status / assumptions to validate:** This is a draft based only on the supplied fictional client pack. It is not a signed-off specification. Confirm process ownership, exact deadline, order identity, units, conversion/reconciliation rules, access permissions, and accounting integration before implementation.

# Part C — Account Executive Handoff

**Account:** Vardhan Steelware

**Stakeholders:** Suresh (owner/sponsor; confirm final purchasing authority); Kunal (order coordinator; maintains the sheet and requests integrations/AI); Ramesh (dispatch; status details kept in a diary); Munshi ji (accounts; validate payment holds/returns in Tally). Four to five external polishing contractors are mentioned; contractor access should not be assumed for Phase 1.

**Pain and evidence:** Suresh cannot tell where goods are stuck and must call Ramesh. A ₹1.2 lakh ERP was used for only three months, signalling adoption/workflow-fit risk. The supplied records show possible repeated orders, a missing customer name, blank rate, payment holds, partial dispatch, a return and a polishing quantity discrepancy. Repeated-looking rows remain candidates for verification, not confirmed duplicates.

**Requests vs. need:** Kunal asks for marketplace integrations and AI sales prediction. The immediate evidenced need is shared order-status visibility, clear ownership/blockers, partial-dispatch balance and polishing send/receive reconciliation. Online sales are reported as under 5%, so integrations should be validated before prioritization.

**Likely objections:** For “we already tried an ERP,” propose a small mobile-friendly pilot with minimal required fields. For a pre-Diwali deadline, confirm the date and must-haves; do not promise a launch before feasibility checks. For AI/integrations, acknowledge the request and first agree on measures for order visibility and data quality.

**Next step:** Run a workflow walkthrough with Suresh, Kunal, Ramesh and Munshi ji. Validate order identity, stages, units, holds, returns and polishing reconciliation against source records; then propose a small pilot and timeline only after technical and user validation.

\newpage

# Part D — Marketing Content (Provisional)

**Important limitation:** The required first-hand audit is incomplete. The content below is a provisional example based on a general operational pain point, not a finding from my own audit. Finalize it against genuine audit findings or use it only if Wantace approves an alternative basis.

## LinkedIn post (under 150 words)

A delayed customer order is not always a production problem. Sometimes, the harder problem is not knowing where the order is right now.

When updates live across notebooks, spreadsheets, and WhatsApp messages, teams can spend valuable time calling each other just to answer three questions:

- What stage is the order at?
- Who owns the next action?
- What is blocking dispatch?

A practical first step is not necessarily a complicated ERP or an AI dashboard. Start with a shared order number, current stage, responsible person, due date, last update, and blocker. Track partial dispatches and returns separately, too.

Make the workflow easy enough for the people who update it every day. Pilot it with a small set of real orders, learn what gets missed, and improve from there.

**Visibility first. Complexity when it is justified.**

## Meta ad

- **Primary text:** Still calling around to find where an order is stuck? Bring order status, next action, and blockers into one simple view. Start small, make updates easy for the team, and improve visibility before adding complexity.
- **Headline:** Know Where Every Order Stands
- **Creative concept:** A two-panel visual: scattered notebook pages and message bubbles on one side; a clean order-status board with fictional Order ID, Stage, Owner, Due Date and Blocker fields on the other. Use fictional sample data only; do not imply a real-business endorsement.

## Language choice

English is proposed for a broad B2B audience and a concise professional tone. If the approved audit audience mainly uses Telugu or another local language, adapt the wording to that audience and explain the choice.
