# Apex Plastics — <span title="Direct-to-Consumer">D2C</span> Target Architecture Assessment

**Programme:** Apex Direct (Direct-to-Consumer Digital Transformation)  
**Role lens:** Salesforce Solution Architect / Enterprise Architecture  
**Document type:** Architecture assessment & target-state design (pre-deck mock-up)  
**Author:** Kyle Cockcroft  
**Version:** 1.0 · August 2026 

---

## Document control

| Field | Value |
|---|---|
| **Primary audiences** | Executives (value, risk, <span title="Return on Investment">ROI</span>); Business stakeholders (process, change); Technical stakeholders (feasibility, integration) |
| **Architecture thesis** | *Shopify owns the cart; Salesforce owns the customer; <span title="MuleSoft Anypoint Platform">MuleSoft</span> owns the contracts; <span title="SAP ERP">SAP</span> stays off the synchronous <span title="Direct-to-Consumer">D2C</span> path until a cloud <span title="Enterprise Resource Planning">ERP</span> replaces it.* |
| **Decision horizon** | Phase 1 launch **<span title="Quarter 3">Q3</span> 2026**; platformize through **<span title="Quarter 2">Q2</span> 2027**; <span title="Enterprise Resource Planning">ERP</span> modernisation **<span title="Quarter 3">Q3</span> 2027–<span title="Quarter 1">Q1</span> 2028** |
| **Evaluation posture** | Short-term pragmatism *with* explicit technical debt; long-term vision protected by versioned <span title="Application Programming Interface">API</span> contracts and domain ownership |

### How to use this document

| Reader | Start here | Then |
|---|---|---|
| <span title="Chief Executive Officer">CEO</span> / <span title="Chief Financial Officer">CFO</span> | Section 1 Executive summary → Section 12 Risks → Section 13 Roadmap | Section 5 Commerce / Salesforce decisions (tables only) |
| Sales / Marketing / Ops leads | Section 3 Requirements traceability → Section 6–9 capability maps | Section 11 Operating model & change |
| <span title="Chief Technology Officer">CTO</span> / <span title="Information Technology">IT</span> / Architects | Section 4 Gaps → Section 5–10 full design → Section 14 <span title="Architecture Decision Records">ADRs</span> → Section 15 Citations | Mermaid diagrams throughout; **Appendix C** for acronyms |

<details>
<summary><strong>Table of contents</strong> — expand a group to jump to a section</summary>

> On GitHub you can also use the file **Outline** control (top-right of the rendered view) for a floating heading list.

<details>
<summary>Document control · Sections 1–5 (strategy &amp; platforms)</summary>

- [Document control](#document-control)
  - [How to use this document](#how-to-use-this-document)
- [1. Executive summary](#1-executive-summary)
  - [Recommended target shape (one page)](#recommended-target-shape-one-page)
  - [Verdict in four sentences](#verdict-in-four-sentences)
  - [Decisions requested from leadership](#decisions-requested-from-leadership)
- [2. Evaluation criteria &amp; architectural approach](#2-evaluation-criteria--architectural-approach)
  - [Pragmatism vs vision](#pragmatism-vs-vision--the-deliberate-balance)
- [3. Business requirements → architectural decisions](#3-business-requirements--architectural-decisions)
  - [3.1 Stakeholder requirement traceability](#31-stakeholder-requirement-traceability)
  - [3.2 Non-negotiable constraints](#32-non-negotiable-constraints)
- [4. Current-state assessment &amp; critical gaps](#4-current-state-assessment--critical-gaps)
  - [4.1 Current-state landscape](#41-current-state-landscape)
  - [4.2 Critical gaps](#42-critical-gaps-in-the-existing-architecture-materials)
  - [4.3 Gap severity heatmap](#43-gap-severity-heatmap-programme-risk)
- [5. E-commerce platform strategy](#5-e-commerce-platform-strategy)
  - [5.1 Intended use of the commerce platform](#51-intended-use-of-the-commerce-platform)
  - [5.2 Options evaluated](#52-options-evaluated)
  - [5.3 SFCC revisit gates](#53-salesforce-commerce-cloud--revisit-gates-phase-3)
  - [5.4 Launch catalog &amp; assortment](#54-launch-catalog--assortment-closes-g3)
  - [5.5 Content &amp; education hub](#55-content--education-hub-closes-g11)

</details>

<details>
<summary>Sections 6–10 (Salesforce · fulfilment · integration · <span title="Master Data Management">MDM</span> · security)</summary>

- [6. Salesforce platform strategy](#6-salesforce-platform-strategy)
  - [6.1 Role of Salesforce in Apex Direct](#61-role-of-salesforce-in-apex-direct)
  - [6.2 Edition &amp; clouds](#62-edition--clouds--decisions)
  - [6.3 Sales Cloud](#63-sales-cloud--functionality-map)
  - [6.4 Service Cloud](#64-service-cloud--returns--support-closes-g4)
  - [6.5 Experience Cloud](#65-experience-cloud--retailer-portal-closes-g6)
  - [6.6 Marketing Cloud](#66-marketing-cloud--intended-use)
  - [6.7 Analytics &amp; D2C P&amp;L](#67-analytics--d2c-pl-closes-g5)
- [7. Fulfilment, ERP &amp; adjacent systems](#7-fulfilment-erp--adjacent-systems)
  - [7.1 SAP R/3 interim strategy](#71-sap-r3-interim-strategy)
  - [7.2 D2C inventory truth](#72-d2c-inventory-truth)
  - [7.3 WMS vs 3PL](#73-wms-vs-3pl-decision-framework-closes-g12)
- [8. Integration architecture](#8-integration-architecture)
  - [8.1 Why hub-and-spoke](#81-why-hub-and-spoke-reject-more-p2p)
  - [8.2 Preferred hub — MuleSoft](#82-preferred-hub--mulesoft-anypoint)
  - [8.3 API-led layering](#83-api-led-layering)
  - [8.4 Happy-path D2C order](#84-happy-path-d2c-order-phase-2-steady-state)
  - [8.5 Integration matrix](#85-integration-matrix)
  - [8.6 Commercial / margin guards](#86-commercial--margin-guards-in-the-fabric-closes-g14)
- [9. Master data management](#9-master-data-management)
  - [9.1 Domain ownership](#91-domain-ownership-golden-record-map)
  - [9.2 Customer identity](#92-customer-identity-anti-duplicate-flow-closes-g9)
  - [9.3 MDM + PIM tooling](#93-mdm--pim-tooling)
  - [9.4 Channel conflict data model](#94-channel-conflict-data-model-closes-g1)
- [10. Security, compliance &amp; operability](#10-security-compliance--operability)
  - [10.1 PCI DSS](#101-pci-dss)
  - [10.2 POPIA &amp; consent](#102-popia--consent-closes-g2)
  - [10.3 Security &amp; edge](#103-security--edge)
  - [10.4 Service objectives](#104-service-objectives-closes-g10)

</details>

<details>
<summary>Sections 11–15 (operating model · risk · roadmap · <span title="Architecture Decision Records">ADRs</span> · citations)</summary>

- [11. Operating model &amp; change](#11-operating-model--change)
  - [11.1 Delivery &amp; run model](#111-delivery--run-model)
  - [11.2 Organisational change](#112-organisational-change-closes-g7)
  - [11.3 Governance artefacts](#113-governance-artefacts)
- [12. Risks &amp; mitigations](#12-risks--mitigations)
- [13. Phased roadmap](#13-phased-roadmap)
  - [13.1 Timeline overview](#131-timeline-overview)
  - [13.2 Phase detail](#132-phase-detail)
  - [13.3 Budget roll-up](#133-budget-roll-up-technology-programme)
  - [13.4 Phase exit criteria](#134-phase-exit-criteria-closes-g8)
  - [13.5 First 30 days](#135-first-30-days-immediate-action-plan)
- [14. Architecture Decision Records](#14-architecture-decision-records)
- [15. Citations &amp; further reading](#15-citations--further-reading)

</details>

<details>
<summary>Appendices A–C (catalogue &amp; glossary)</summary>

- [16. Appendix A — Capability ↔ platform catalogue](#16-appendix-a--capability--platform-catalogue)
- [17. Appendix B — Glossary (short)](#17-appendix-b--glossary-short)
- [18. Appendix C — Glossary (long)](#18-appendix-c--glossary-long)
  - [C.1 Business &amp; commercial](#c1-business--commercial)
  - [C.2 Salesforce &amp; CRM](#c2-salesforce--crm)
  - [C.3 Commerce, payments, tax &amp; content](#c3-commerce-payments-tax--content)
  - [C.4 Integration &amp; middleware](#c4-integration--middleware)
  - [C.5 Data, identity &amp; master data](#c5-data-identity--master-data)
  - [C.6 ERP, warehouse &amp; operations](#c6-erp-warehouse--operations)
  - [C.7 Security, infrastructure &amp; operability](#c7-security-infrastructure--operability)
  - [C.8 Architecture method &amp; programme governance](#c8-architecture-method--programme-governance)
  - [C.9 Separation-of-concerns terms](#c9-separation-of-concerns-terms-used-as-labels-not-acronyms)

</details>

</details>

---

## 1. Executive summary

Apex Plastics is a R425M <span title="Business-to-Business">B2B</span> manufacturer (380 accounts, ~8,500 <span title="Stock Keeping Units">SKUs</span>) launching **Apex Direct** to serve homeowners, small contractors, farmers, and outdoor buyers who need smaller baskets and faster fulfilment than retail channels provide. Targets: **R60M <span title="Year 1">Y1</span>**, **R175M <span title="Year 3">Y3</span> (~30% of revenue)**, launch **<span title="Quarter 3">Q3</span> 2026**.

The current estate cannot absorb that model as-is: **14-year <span title="SAP R/3">SAP R/3</span>** with no <span title="Application Programming Interface">API</span> layer, **homegrown Access <span title="Warehouse Management System">WMS</span>**, **Salesforce Professional** with weak adoption and no <span title="Enterprise Resource Planning">ERP</span> link, **15 fragile point-to-point integrations**, a **7-person <span title="Information Technology">IT</span> team**, and **zero <span title="Payment Card Industry">PCI</span> / <span title="Direct-to-Consumer">D2C</span> payment muscle memory**. Simultaneously, the top **12 retailers = 65% of revenue** — channel conflict is an existential business risk, not a <span title="User Experience">UX</span> preference.

### Recommended target shape (one page)

```mermaid
flowchart TB
  subgraph EXP["Experience layer<br/>engagement / sale"]
    direction TB
    SH["Shopify Plus<br/>storefront · cart · checkout"]
    RP["Experience Cloud<br/>Retailer Portal"]
    SC["Sales Cloud + CPQ"]
    SVC["Service Cloud"]
    MC["Marketing Cloud<br/>Engagement"]
  end

  subgraph C360["Customer 360 hub"]
    SF["Salesforce Enterprise<br/>Account · Contact · Order<br/>Case · Consent"]
  end

  subgraph FAB["Integration fabric"]
    MS["MuleSoft Anypoint<br/>API-led · Events<br/>Batch · Observability"]
  end

  subgraph SOR["Systems of record / ops"]
    direction TB
    MDM["MDM<br/>Golden Customer"]
    PIM["PIM<br/>Shopper Product Content"]
    WMS["D2C WMS<br/>Consumer-pack ATP"]
    SAP["SAP R/3 interim<br/>B2B finance / stock"]
    PAY["Payments + Fraud"]
    TAX["Tax engine<br/>ZA VAT + SADC"]
  end

  SH --> MS
  RP --> SF
  SC --> SF
  SVC --> SF
  MC --> SF
  SF <--> MS
  MS <--> MDM
  MS <--> PIM
  MS <--> WMS
  MS <--> SAP
  MS <--> PAY
  MS <--> TAX
  SH -.->|hosted / tokenized<br/>checkout| PAY
```

### Verdict in four sentences

1. **Commerce:** Adopt **<span title="Shopify Plus">Shopify Plus</span>** for Phase 1 as *system of sale*; defer **<span title="Salesforce Commerce Cloud">Salesforce Commerce Cloud</span>** until <span title="Enterprise Resource Planning">ERP</span> is modern and <span title="Total Cost of Ownership">TCO</span> clears.  
2. **Salesforce:** Upgrade to **Enterprise Edition**; make Sales + Service + <span title="Salesforce Marketing Cloud Engagement">Marketing Cloud</span> the **<span title="Salesforce Customer 360">Customer 360</span>** hub (identity, service, journeys, <span title="Business-to-Business">B2B</span> conflict rules, lead escalation).  
3. **Integration & <span title="Master Data Management">MDM</span>:** Stop <span title="Point-to-Point">P2P</span> growth; introduce **<span title="MuleSoft Anypoint Platform">MuleSoft</span>** (preferred) as hub; implement **<span title="Master Data Management">MDM</span> + <span title="Product Information Management">PIM</span>** in Phase 2 with domain ownership defined in Phase 1.  
4. **Roadmap:** Ship a **thin vertical slice** in <span title="Quarter 3">Q3</span> 2026 with *named* <span title="SAP ERP">SAP</span> nightly-batch debt; platformize in Phase 2; swap <span title="Enterprise Resource Planning">ERP</span> behind stable <span title="Application Programming Interfaces">APIs</span> in Phase 3.

### Decisions requested from leadership

1. Approve target stack (<span title="Shopify Plus">Shopify Plus</span> + Salesforce <span title="Enterprise Edition">EE</span> + <span title="Salesforce Marketing Cloud Engagement">Marketing Cloud</span> + <span title="MuleSoft Anypoint Platform">MuleSoft</span> preferred + <span title="Master Data Management">MDM</span>/<span title="Product Information Management">PIM</span> in <span title="Phase 2">P2</span>).  
2. Freeze Phase 1 scope: no <span title="Enterprise Resource Planning">ERP</span> replacement, no <span title="Salesforce Commerce Cloud">SFCC</span>, no full <span title="Master Data Management">MDM</span> on the launch critical path.  
3. Fund next-30-day delivery core: Integration Architect + <span title="Development + Operations">DevOps</span> path, Shopify/<span title="Salesforce">SF</span> <span title="System Integrator">SI</span>, <span title="Qualified Security Assessor">QSA</span>, two spikes (<span title="SAP ERP">SAP</span> interface inventory; <span title="Integration Platform as a Service">iPaaS</span> + identity match <span title="Proof of Concept">PoC</span>).  
4. Mandate Apex-owned, versioned <span title="Application Programming Interface">API</span> contracts (Order, Inventory, Customer, Product) in Git before build.

---

## 2. Evaluation criteria & architectural approach

These criteria drove every include / exclude / defer decision in this assessment. They mirror the programme brief and global architecture practice (<span title="The Open Group Architecture Framework">TOGAF</span> <span title="Architecture Development Method">ADM</span>-style current→target→roadmap; Salesforce Well-Architected; <span title="API-led connectivity">API-led</span> connectivity).

| Criterion | What “good” looks like for Apex | How it influenced decisions |
|---|---|---|
| **Stakeholder requirement fidelity** | <span title="Chief Executive Officer">CEO</span> channel rules, <span title="Chief Financial Officer">CFO</span> volume/tax/<span title="Profit and Loss">P&L</span>, Sales conflict + lead routing, Marketing journeys/<span title="Product Information Management">PIM</span>, Ops unit-pick fulfilment, Finance auto-posting, <span title="Information Technology">IT</span> staffing reality | Requirements → capability → platform mapping (Section 3) |
| **Time-to-value vs vision** | Live storefront by <span title="Quarter 3">Q3</span> 2026 *without* painting into a corner | Shopify <span title="Phase 1">P1</span>; <span title="Salesforce Commerce Cloud">SFCC</span> deferred; <span title="Enterprise Resource Planning">ERP</span> <span title="Phase 3">P3</span>; contracts owned early |
| **Separation of concerns** | System of Sale ≠ System of Engagement ≠ System of Record | Shopify / Salesforce / <span title="SAP ERP">SAP</span>+<span title="Warehouse Management System">WMS</span>+<span title="Master Data Management">MDM</span> split |
| **Integration sustainability** | No growth of the 15 <span title="Point-to-Point">P2P</span> integrations | Hub-and-spoke via <span title="Integration Platform as a Service">iPaaS</span>; <span title="API-led connectivity">API-led</span> layers |
| **Data integrity** | One golden customer; <span title="Direct-to-Consumer">D2C</span> <span title="Available to Promise">ATP</span> ≠ <span title="SAP ERP">SAP</span> pallet qty | <span title="Master Data Management">MDM</span> <span title="Phase 2">P2</span>; <span title="Warehouse Management System">WMS</span> as <span title="Direct-to-Consumer">D2C</span> inventory <span title="System of Record">SoR</span> |
| **Risk & compliance** | <span title="Payment Card Industry">PCI</span>, <span title="Protection of Personal Information Act">POPIA</span>, retailer backlash, <span title="SAP ERP">SAP</span> overload | Hosted checkout; consent model; conflict flags; batch isolation |
| **Operability** | 7 <span title="Full-Time Equivalents">FTEs</span> cannot run enterprise alone | Hybrid: hire key roles + <span title="System Integrator">SI</span> + managed <span title="Security Operations Centre">SOC</span>/<span title="Network Operations Centre">NOC</span> |
| **<span title="Total Cost of Ownership">TCO</span> honesty** | Licence + <span title="System Integrator">SI</span> + run + people + debt interest | Explicit phase budgets; <span title="Boomi (Dell Boomi)">Boomi</span> as <span title="Total Cost of Ownership">TCO</span> alternative |
| **Exit / evolvability** | <span title="Enterprise Resource Planning">ERP</span> and commerce can change without rewriting the estate | Versioned Experience/Process/System <span title="Application Programming Interfaces">APIs</span> |

### Pragmatism vs vision — the deliberate balance

```mermaid
flowchart TB
  subgraph SHORT["Short-term pragmatism — Phase 1"]
    direction TB
    A1[Shopify Plus MVP]
    A2[SF Enterprise + basic MC]
    A3[Nightly SAP batch]
    A4[Connector sync SF↔Shopify]
    A5[Conservative ATP buffers]
  end

  subgraph LONG["Long-term vision — Phase 2/3"]
    direction TB
    B1[MuleSoft API fabric]
    B2[MDM + PIM]
    B3[Near-RT inventory / WMS]
    B4[Cloud ERP behind same APIs]
    B5[Optional SFCC revisit]
  end

  SHORT -->|Named debt + exit criteria| LONG
```

**Rule used throughout:** Accept debt only when (a) it unblocks <span title="Quarter 3">Q3</span> 2026, (b) it is *named* with an owner and retirement phase, and (c) it does not create irreversible lock-in (business logic stays in Salesforce/<span title="MuleSoft Anypoint Platform">MuleSoft</span> contracts, not Shopify scripts or one-off <span title="SAP ERP">SAP</span> RFCs).

---

## 3. Business requirements → architectural decisions

### 3.1 Stakeholder requirement traceability

| Stakeholder need | Architectural response | Phase |
|---|---|---|
| Protect top-12 retailers (65% revenue); <span title="Direct-to-Consumer">D2C</span> for small/custom/rush only | Channel-conflict rules in Salesforce; <span title="Stock Keeping Unit">SKU</span>/geo/basket guards; Marketing suppression for <span title="Business-to-Business">B2B</span>-managed accounts | <span title="Phase 1">P1</span> rules (manual+config) → <span title="Phase 2">P2</span> automated |
| Profitable ~R350 orders (vs R15k <span title="Business-to-Business">B2B</span> <span title="Minimum Order Quantity">MOQ</span>) | Dedicated <span title="Direct-to-Consumer">D2C</span> <span title="Warehouse Management System">WMS</span> / each-pick; consumer packaging; shipping rate cards; optional order-floor rules | <span title="Phase 1">P1</span> packaging <span title="Minimum Viable Product">MVP</span> → <span title="Phase 2">P2</span> <span title="Warehouse Management System">WMS</span> |
| 75k–125k orders/mo must not collapse <span title="SAP ERP">SAP</span> | Keep <span title="SAP ERP">SAP</span> off synchronous <span title="Direct-to-Consumer">D2C</span> path; batch <span title="Phase 1">P1</span> → near-RT <span title="Phase 2">P2</span> → cloud <span title="Enterprise Resource Planning">ERP</span> <span title="Phase 3">P3</span> | <span title="Phase 1">P1</span>–<span title="Phase 3">P3</span> |
| Single customer view + R25k/6-mo lead routing | Salesforce as engagement hub; Order + lifecycle events; Flow/apex routing | <span title="Phase 1">P1</span> basic → <span title="Phase 2">P2</span> hardened |
| <span title="Configure, Price, Quote">CPQ</span> for custom moulding + transactional <span title="Direct-to-Consumer">D2C</span> | <span title="Salesforce Sales Cloud">Sales Cloud</span> + <span title="Configure, Price, Quote">CPQ</span> for complex; Shopify for catalog commerce; no forced unification in <span title="Phase 1">P1</span> | <span title="Phase 1">P1</span>+ |
| Triggered journeys, attribution, <span title="Customer Acquisition Cost">CAC</span>/<span title="Lifetime Value">LTV</span> | <span title="Salesforce Marketing Cloud Engagement">Marketing Cloud</span> + commerce/<span title="Customer Relationship Management">CRM</span> events; analytics mart; <span title="Salesforce Data Cloud">Data Cloud</span> evaluate later | <span title="Phase 1">P1</span> basic → <span title="Phase 2">P2</span> advanced |
| 8,500 <span title="Stock Keeping Units">SKUs</span> need shopper content | <span title="Product Information Management">PIM</span> as content <span title="System of Record">SoR</span>; <span title="Enterprise Resource Planning">ERP</span> retains <span title="Stock Keeping Unit">SKU</span>/cost/<span title="Bill of Materials">BOM</span> | <span title="Phase 2">P2</span> (<span title="Phase 1">P1</span> curated <span title="Minimum Viable Product">MVP</span> catalog) |
| Card pay-before-ship, <span title="Payment Card Industry">PCI</span>, fraud, <span title="South Africa">ZA</span>+<span title="Southern African Development Community">SADC</span> tax | Hosted/tokenized checkout; fraud scoring; tax engine | <span title="Phase 1">P1</span> |
| Separate <span title="Direct-to-Consumer">D2C</span> <span title="Profit and Loss">P&L</span> for board | Chart of accounts / product hierarchy tags; finance extract; <span title="Business Intelligence">BI</span> dimensions | <span title="Phase 1">P1</span> finance tags → <span title="Phase 2">P2</span> <span title="Business Intelligence">BI</span> |
| Sales compensation clarity | *Business* policy first; tech enforces attribution flags (<span title="Direct-to-Consumer">D2C</span> vs retail-assisted) | <span title="Phase 1">P1</span> policy → <span title="Phase 2">P2</span> reporting |
| Retailer portal with availability | <span title="Salesforce Experience Cloud">Experience Cloud</span> site on Salesforce; inventory via <span title="MuleSoft Anypoint Platform">MuleSoft</span> from <span title="Warehouse Management System">WMS</span> | <span title="Phase 2">P2</span> (<span title="Phase 1">P1</span> read-only backlog OK) |
| <span title="Protection of Personal Information Act">POPIA</span> consent for SA digital marketing | Consent objects in Salesforce; journey entry gated; Shopify consent sync | <span title="Phase 1">P1</span> |

### 3.2 Non-negotiable constraints

| Constraint | Implication |
|---|---|
| <span title="Quarter 3">Q3</span> 2026 launch | Commerce platform must be mid-market speed-to-live (Shopify-class), not a 12–18 month suite programme |
| <span title="SAP R/3">SAP R/3</span> 2011, Oracle 11g, no <span title="Application Programming Interface">API</span> layer | Treat <span title="Enterprise Resource Planning">ERP</span> as *batch-capable <span title="System of Record">SoR</span>*, not real-time commerce backend |
| 7-person <span title="Information Technology">IT</span>, maintenance-mode skills | Hybrid delivery; do not staff every specialty permanently |
| Channel conflict existential | Architecture must encode retailer protection, not only marketing copy |
| <span title="Payment Card Industry">PCI</span> is new | Prefer <span title="PCI SAQ A">SAQ A</span> / A-EP patterns; engage <span title="Qualified Security Assessor">QSA</span> in Phase 1 |

---

## 4. Current-state assessment & critical gaps

### 4.1 Current-state landscape

```mermaid
flowchart TB
  subgraph TODAY["As-is — fragile & B2B-centric"]
    WEB[Basic website<br/>45k visits/mo · no cart]
    SFPE[Salesforce PE<br/>~60% adoption · no ERP link]
    SAP[SAP R/3 on-prem<br/>custom · no API layer]
    WMS[Access DB WMS<br/>pallet pick]
    MES[Defunct-vendor MES]
    P2P[15 P2P integrations<br/>files · DB · ad-hoc WS]
  end

  WEB -.->|no commerce| X[Gap]
  SFPE -.->|duplicates 15-20%| SAP
  SAP --- P2P
  WMS --- P2P
  MES --- P2P
```

| Domain | Current state | Fitness for <span title="Direct-to-Consumer">D2C</span> |
|---|---|---|
| Commerce | Brochure site only | **Unfit** |
| <span title="Customer Relationship Management">CRM</span> | <span title="Salesforce">SF</span> Professional, weak adoption, duplicates | **Partially fit** — upgrade + process redesign required |
| Marketing | Trade/<span title="Business-to-Business">B2B</span> mature; digitally immature; no automation | **Unfit** for <span title="Direct-to-Consumer">D2C</span> <span title="Customer Acquisition Cost">CAC</span>/<span title="Lifetime Value">LTV</span> model |
| <span title="Enterprise Resource Planning">ERP</span> / Finance | <span title="SAP R/3">SAP R/3</span>; 3,200 invoices/mo manual-heavy | **Unfit** for 75k–125k <span title="Direct-to-Consumer">D2C</span> orders synchronously |
| Fulfilment | Pallet <span title="Warehouse Management System">WMS</span>; bulk packs | **Unfit** for each-pick unit economics |
| Integration | 15 heterogeneous <span title="Point-to-Point">P2P</span> links | **Unfit** — will compound with +8–10 <span title="Direct-to-Consumer">D2C</span> links |
| Infrastructure | Single-<span title="Internet Service Provider">ISP</span> <span title="Data Centre">DC</span>; no <span title="Content Delivery Network">CDN</span>/<span title="Web Application Firewall">WAF</span> posture | **Unfit** for public internet retail |
| Security / <span title="Payment Card Industry">PCI</span> | No card-acceptance programme | **Unfit** |
| People / ops model | 7 <span title="Information Technology">IT</span> <span title="Full-Time Equivalents">FTEs</span>, maintenance mode | **Unfit** without hybrid augmentation |
| Data | No golden customer; <span title="Enterprise Resource Planning">ERP</span> product data not shopper-ready | **Unfit** |

### 4.2 Critical gaps in the *existing* architecture materials

The discovery brief and technical compendium establish a sound direction. The gaps below are what this assessment closes before decks are built.

| # | Gap | Why it matters | Closed in this doc by |
|---|---|---|---|
| G1 | **Channel-conflict model underspecified** | Retailer backlash can erase <span title="Direct-to-Consumer">D2C</span> <span title="Return on Investment">ROI</span> | Section 9.4 channel conflict data model (+ Section 8.3 <span title="Process API (orchestration layer)">Process API</span> guard) |
| G2 | **<span title="Protection of Personal Information Act">POPIA</span> / consent architecture missing** | Illegal or untrusted marketing kills <span title="Salesforce Marketing Cloud Engagement">MC</span> value | Section 10.2 <span title="Protection of Personal Information Act">POPIA</span> and consent |
| G3 | **<span title="Minimum Viable Product">MVP</span> catalog / assortment strategy absent** | 8,500 <span title="Stock Keeping Units">SKUs</span> cannot all be <span title="Direct-to-Consumer">D2C</span>-ready by <span title="Quarter 3">Q3</span> | Section 5.4 launch catalog and assortment |
| G4 | **Returns / <span title="Return Merchandise Authorisation">RMA</span> process thin** | 3–5% returns vs &lt;0.5% <span title="Business-to-Business">B2B</span> | Section 6.4 <span title="Salesforce Service Cloud">Service Cloud</span> returns and support |
| G5 | **Analytics / attribution stack vague** | Board <span title="Customer Acquisition Cost">CAC</span>/<span title="Lifetime Value">LTV</span>/<span title="Profit and Loss">P&amp;L</span> needs cannot be met by <span title="Customer Relationship Management">CRM</span> alone | Section 6.7 analytics and <span title="Direct-to-Consumer">D2C</span> <span title="Profit and Loss">P&amp;L</span> |
| G6 | **Retailer portal deferred without interim story** | Sales explicitly required availability visibility | Section 6.5 <span title="Salesforce Experience Cloud">Experience Cloud</span> retailer portal |
| G7 | **Compensation / attribution** treated as “policy only” | Sales sabotage risk is real | Section 11.2 organisational change (+ Section 9.4 attribution flags) |
| G8 | **Phase exit criteria / debt retirement gates weak** | Named debt without gates becomes permanent | Section 13.4 phase exit criteria |
| G9 | **Identity model** (guest vs registered vs <span title="Business-to-Business">B2B</span> contact) incomplete | Duplicates and wrong marketing suppression | Section 9.2 customer identity anti-duplicate flow |
| G10 | **Observability / <span title="Recovery Time Objective">RTO</span>-<span title="Recovery Point Objective">RPO</span> / support hours** glossary-only | <span title="Direct-to-Consumer">D2C</span> is 24/7 customer-facing | Section 10.4 service objectives (<span title="Service Level Objectives">SLOs</span>) |
| G11 | **Content/<span title="Content Management System">CMS</span> & education hub** (marketing vision) not placed | Community/content is part of brand control thesis | Section 5.5 content and education hub |
| G12 | **<span title="Third-Party Logistics">3PL</span> vs owned <span title="Warehouse Management System">WMS</span> decision framework** incomplete | R7.5M warehouse automation vs outsource | Section 7.3 <span title="Warehouse Management System">WMS</span> vs <span title="Third-Party Logistics">3PL</span> decision framework |
| G13 | **<span title="Salesforce Commerce Cloud">SFCC</span> revisit criteria** qualitative only | Avoid endless “maybe <span title="Salesforce Commerce Cloud">Commerce Cloud</span>” debates | Section 5.3 <span title="Salesforce Commerce Cloud">SFCC</span> revisit gates |
| G14 | **Order profitability / floor-price guards** missing | R350 <span title="Average Order Value">AOV</span> can destroy margin with shipping | Section 8.6 commercial and margin guards |

### 4.3 Gap severity heatmap (programme risk)

Most gaps (G1–G4, G7–G9, G12, G14) sit in the **same quadrant** — high urgency and high severity before <span title="Quarter 3">Q3</span> 2026. The view below uses a **priority matrix** (grouped lists). Quadrant placement reflects the **architect's estimate** of each gap's urgency, severity, and relative priority. A **cross-functional workshop** should be held to validate and reassess the importance of the gaps outlined in this document before they are used for programme governance or scope decisions. Point labels G1–G14 map to the gap table in Section 4.2.

```mermaid
flowchart TB
  subgraph HIGH["High severity — design before or during Phase 1"]
    direction LR
    subgraph Q2["Plan in Phase 2 · lower urgency"]
      direction TB
      g6["G6 Retailer portal"]
      g10["G10 SLOs"]
    end
    subgraph Q1["Must design now · higher urgency"]
      direction TB
      q1r1["G1 Channel conflict · G3 MVP catalog · G9 Identity"]
      q1r2["G14 Margin guards · G2 POPIA · G7 Comp attribution"]
      q1r3["G8 Exit criteria · G12 3PL vs WMS · G4 Returns RMA"]
      q1r4["G5 Analytics"]
    end
  end

  subgraph LOW["Low severity — monitor or defer"]
    direction LR
    subgraph Q3["Monitor · lower urgency"]
      direction TB
      g13["G13 SFCC gates"]
      g11["G11 CMS content"]
    end
    subgraph Q4["Lightweight P1 · higher urgency"]
      direction TB
      q4none["No gaps scored in this quadrant"]
    end
  end
```

---

## 5. E-commerce platform strategy

### 5.1 Intended use of the commerce platform

| Concern | Owner platform | Why |
|---|---|---|
| Browse, search, merchandising, cart, checkout | **<span title="Shopify Plus">Shopify Plus</span>** | System of *sale* — speed, <span title="Payment Card Industry">PCI</span> reduction, <span title="Direct-to-Consumer">D2C</span> patterns |
| Customer identity, service, <span title="Business-to-Business">B2B</span> relationships, journeys | **Salesforce** | System of *engagement* |
| Inventory truth for <span title="Direct-to-Consumer">D2C</span> <span title="Available to Promise">ATP</span> | **<span title="Direct-to-Consumer">D2C</span> <span title="Warehouse Management System">WMS</span>** (via <span title="MuleSoft Anypoint Platform">MuleSoft</span>) | Consumer-pack quantities, not <span title="SAP ERP">SAP</span> pallets |
| Financial posting / <span title="Business-to-Business">B2B</span> order-to-cash | **<span title="SAP ERP">SAP</span> → Cloud <span title="Enterprise Resource Planning">ERP</span>** | System of *record* for finance |
| Shopper-facing product content | **<span title="Product Information Management">PIM</span>** | <span title="Enterprise Resource Planning">ERP</span> is manufacturing-centric |

**Shopify is not the customer master.** It holds storefront session/customer records that sync to Salesforce/<span title="Master Data Management">MDM</span>. Business rules that protect retailers, escalate leads, and suppress journeys live in Salesforce (+ <span title="MuleSoft Anypoint Platform">MuleSoft</span> process <span title="Application Programming Interfaces">APIs</span>).

### 5.2 Options evaluated

#### Option A — <span title="Shopify Plus">Shopify Plus</span> *(recommended Phase 1)*

| | |
|---|---|
| **What it is** | Enterprise <span title="Software as a Service">SaaS</span> commerce for high-volume <span title="Direct-to-Consumer">D2C</span> brands |
| **Performs in this architecture** | Storefront, cart/checkout, catalog, promos, basic <span title="Order Management System">OMS</span>, app ecosystem, <span title="Content Delivery Network">CDN</span> hosting, payment integrations |
| **Why chosen** | Credible <span title="Quarter 3">Q3</span> 2026 path (typical <span title="Minimum Viable Product">MVP</span> **3–6 months**); hosted/tokenized checkout shrinks <span title="Payment Card Industry">PCI</span> scope; strong mid-market <span title="Direct-to-Consumer">D2C</span> ecosystem in/near SA via partners; exit path preserved if logic stays in <span title="MuleSoft Anypoint Platform">MuleSoft</span>/<span title="Salesforce">SF</span> |

| Pros | Cons |
|---|---|
| Fastest time-to-live aligned to <span title="Quarter 3">Q3</span> 2026 | Not native Salesforce — dual-stack complexity |
| <span title="Payment Card Industry">PCI</span> scope reduction (<span title="PCI SAQ A">SAQ A</span> / A-EP patterns) | <span title="Gross Merchandise Value">GMV</span>-linked cost must be modelled for <span title="Year 3">Y3</span> (R175M) |
| Huge app/partner ecosystem for tax, fraud, subscriptions | Complex <span title="Business-to-Business">B2B</span> price books / <span title="Configure, Price, Quote">CPQ</span> remain outside Shopify |
| Easier to staff/<span title="System Integrator">SI</span> in mid-market <span title="South Africa">ZA</span> context | Risk of “logic in Liquid/apps” lock-in if governance is weak |

#### Option B — <span title="Salesforce Commerce Cloud">Salesforce Commerce Cloud</span> (<span title="Business-to-Consumer">B2C</span>) *(deferred)*

| | |
|---|---|
| **What it is** | Salesforce enterprise commerce suite |
| **Would perform** | Storefront + deeper native <span title="Customer Relationship Management">CRM</span> commerce objects when fully adopted |
| **Why not Phase 1** | Does **not** fix <span title="SAP R/3">SAP R/3</span>; typical complex enterprise builds run **longer** than Shopify <span title="Minimum Viable Product">MVP</span>; Apex critical path is *launch + integration + ops*, not commerce-suite consolidation |

| Pros | Cons |
|---|---|
| Native <span title="Salesforce Customer 360">Customer 360</span> narrative | Longer / heavier implementation for this context |
| One vendor commercial relationship | Still needs <span title="Integration Platform as a Service">iPaaS</span>, <span title="Warehouse Management System">WMS</span>, tax, payments, <span title="Product Information Management">PIM</span> |
| Stronger long-term <span title="Salesforce">SF</span> process unification | Higher <span title="Total Cost of Ownership">TCO</span> before <span title="Gross Merchandise Value">GMV</span> proves out |

#### Option C — Adobe Commerce (Magento) *(rejected for <span title="Phase 1">P1</span>)*

| Pros | Cons |
|---|---|
| Very flexible / robust catalog capabilities | **8–12 month** class programmes common; heavier ops |
| Strong <span title="Business-to-Business">B2B</span>+<span title="Business-to-Consumer">B2C</span> in one platform (theoretically) | Overlaps badly with “keep <span title="Business-to-Business">B2B</span> motion in Salesforce <span title="Configure, Price, Quote">CPQ</span>” |
| | Larger security/patching surface than <span title="Software as a Service">SaaS</span> Shopify |

**Decision (<span title="Architecture Decision Record">ADR</span>-01):** <span title="Shopify Plus">Shopify Plus</span> for Phase 1 system of sale; Adobe out; <span title="Salesforce Commerce Cloud">SFCC</span> on a gated revisit (see Section 5.3).

### 5.3 <span title="Salesforce Commerce Cloud">Salesforce Commerce Cloud</span> — revisit gates (Phase 3+)

Revisit <span title="Salesforce Commerce Cloud">SFCC</span> **only if all** are true:

1. Cloud <span title="Enterprise Resource Planning">ERP</span> live (or irrevocably contracted) and Order/Inventory <span title="Application Programming Interfaces">APIs</span> stable.  
2. <span title="Direct-to-Consumer">D2C</span> <span title="Gross Merchandise Value">GMV</span> and contribution margin sustain <span title="Salesforce Commerce Cloud">SFCC</span> <span title="Total Cost of Ownership">TCO</span> (licence + <span title="System Integrator">SI</span> + rebuild).  
3. Unified Salesforce commercial processes (promotions, shared catalog services, service-commerce) demonstrably outweigh migration cost.  
4. <span title="Shopify Plus">Shopify Plus</span> contractual / <span title="Gross Merchandise Value">GMV</span> economics deteriorate relative to <span title="Salesforce Commerce Cloud">SFCC</span>.  
5. Business accepts a **replatform freeze** window (typically a full planning year).

Until then, invest in **<span title="MuleSoft Anypoint Platform">MuleSoft</span> contracts + Salesforce <span title="Salesforce Customer 360">Customer 360</span>**, not a second storefront programme.

### 5.4 Launch catalog & assortment (closes G3)

Do **not** attempt 8,500 shopper-ready <span title="Stock Keeping Units">SKUs</span> at launch.

| Wave | Scope | Intent |
|---|---|---|
| **<span title="Minimum Viable Product">MVP</span> catalog (<span title="Phase 1">P1</span>)** | ~150–300 high-velocity consumer <span title="Stock Keeping Units">SKUs</span> (tanks, storage, outdoor, waste) with complete imagery/specs | Prove unit economics & ops |
| **Wave 2** | Expand by category; exclude custom moulding from self-serve | Protect <span title="Configure, Price, Quote">CPQ</span> motion |
| **Wave 3** | Long-tail via <span title="Product Information Management">PIM</span>-driven publish | Scale content factory |

**Channel-safe assortment rule:** Prefer <span title="Stock Keeping Units">SKUs</span> / pack sizes retailers under-serve (each-packs, rush, accessories, education-led kits). Flag “retail-sensitive” <span title="Stock Keeping Units">SKUs</span> for geo or login gating if Sales Leadership requires.

### 5.5 Content & education hub (closes G11)

Marketing’s vision (install videos, selectors, forums, webinars) is brand-control infrastructure — but it is **not** on the <span title="Phase 1">P1</span> critical path for checkout.

| Capability | <span title="Phase 1">P1</span> | <span title="Phase 2">P2</span>+ |
|---|---|---|
| Product story content | Shopify theme + curated <span title="Content Management System">CMS</span> sections | <span title="Product Information Management">PIM</span>-driven rich content |
| Education / <span title="Search Engine Optimisation">SEO</span> hub | Lightweight blog or headless <span title="Content Management System">CMS</span> linked from store | Dedicated content site / <span title="Salesforce Experience Cloud">Experience Cloud</span> microsite |
| Community / forums | Out of scope | Evaluate later (moderation cost is high) |
| Webinars | Manual (Zoom + <span title="Salesforce Marketing Cloud Engagement">MC</span> journeys) | Automated nurture in <span title="Salesforce Marketing Cloud Engagement">MC</span> |

---

## 6. Salesforce platform strategy

### 6.1 Role of Salesforce in Apex Direct

**Intended use:** Salesforce is the **<span title="Salesforce Customer 360">Customer 360</span> system of engagement** — the place where <span title="Business-to-Business">B2B</span> relationships, <span title="Direct-to-Consumer">D2C</span> customers, service history, marketing consent, and commercial conflict rules coexist.

```mermaid
flowchart TB
  subgraph EE["Salesforce Enterprise Edition"]
    SALES["Sales Cloud<br/>Accounts · Opps · Lead routing · Retailer mgmt"]
    CPQ["CPQ<br/>Custom moulding / complex quotes"]
    SVC["Service Cloud<br/>Cases · Omni · Knowledge · Returns"]
    MC["Marketing Cloud Engagement<br/>Journeys · triggered messaging"]
    XC["Experience Cloud<br/>Retailer portal"]
    CORE["Shared platform<br/>Automation · Sharing · APIs · Consent"]
  end

  SH[Shopify Plus] -->|orders · customers · events| MS[MuleSoft]
  MS --> CORE
  SALES --> CORE
  CPQ --> CORE
  SVC --> CORE
  MC --> CORE
  XC --> CORE
```

### 6.2 Edition & clouds — decisions

| Component | Decision | Reason | Pros | Cons |
|---|---|---|---|---|
| **Edition** | Upgrade **Professional → Enterprise** | <span title="Professional Edition">PE</span> lacks <span title="Application Programming Interface">API</span> volume, automation depth, and sharing sophistication for <span title="Direct-to-Consumer">D2C</span> + <span title="Integration Platform as a Service">iPaaS</span> | Unlocks <span title="Salesforce Customer 360">Customer 360</span> thesis | Licence uplift |
| **<span title="Salesforce Sales Cloud">Sales Cloud</span>** | Core <span title="Business-to-Business">B2B</span> + <span title="Direct-to-Consumer">D2C</span> lead escalation | Already in estate; relationship sellers stay here | Continuity; conflict ownership | Adoption work required |
| **<span title="Configure, Price, Quote">CPQ</span>** | Retain/expand for custom moulding | Different sales motion from cart checkout | Protects high-margin custom | Two quote paradigms to govern |
| **<span title="Salesforce Service Cloud">Service Cloud</span>** | Mandatory for <span title="Direct-to-Consumer">D2C</span> | Returns 3–5%; product advice at scale; same customer record | True 360 with Sales | New queues, SLAs, staffing |
| **<span title="Salesforce Marketing Cloud Engagement">Marketing Cloud Engagement</span>** | Selected for volume journeys | <span title="Direct-to-Consumer">D2C</span> needs behavioural triggers at scale; <span title="Salesforce">SF</span>-aligned | Native-ish <span title="Salesforce">SF</span> journey story | Cost; needs data discipline |
| **<span title="Salesforce Account Engagement (Pardot)">Account Engagement</span> (<span title="Salesforce Account Engagement (Pardot)">Pardot</span>)** | Budget fallback only | Stronger for <span title="Business-to-Business">B2B</span> MQLs than high-volume cart triggers | Cheaper entry | Weak fit for abandon-cart scale |
| **<span title="Salesforce Commerce Cloud">Commerce Cloud</span>** | Deferred | See Section 5 | — | — |
| **<span title="Salesforce Data Cloud">Data Cloud</span>** | Evaluate Phase 2/3 | <span title="Customer Data Platform">CDP</span> value appears after event volume exists | Unified profile/activation | Premature <span title="Total Cost of Ownership">TCO</span> in <span title="Phase 1">P1</span> |
| **<span title="Salesforce Experience Cloud">Experience Cloud</span>** | Retailer portal in Phase 2 | Sales requirement; inventory from <span title="Warehouse Management System">WMS</span> | Authenticated <span title="Business-to-Business">B2B</span> <span title="User Experience">UX</span> on <span title="Salesforce">SF</span> | Build + licence |

### 6.3 <span title="Salesforce Sales Cloud">Sales Cloud</span> — functionality map

| Function | How it works |
|---|---|
| Account / Contact master *of engagement* | Upserted from Shopify + <span title="SAP ERP">SAP</span> via match rules; golden ID from <span title="Master Data Management">MDM</span> in <span title="Phase 2">P2</span> |
| Retailer hierarchy | Top-12 strategic accounts tagged; partner price plans remain <span title="Business-to-Business">B2B</span>-only |
| Lead routing | When <span title="Direct-to-Consumer">D2C</span> customer spend ≥ **R25k in 6 months** → create/route Lead/Opp to regional sales |
| Opportunity / tender | Unchanged for municipal/government motion |
| Conflict case | “Channel conflict” record type when retailer complains or rule breach detected |
| Commission inputs | <span title="Direct-to-Consumer">D2C</span> vs assisted flags on Order for Finance/Sales ops (policy still human) |

### 6.4 <span title="Salesforce Service Cloud">Service Cloud</span> — returns & support (closes G4)

```mermaid
flowchart TB
  A[Customer request<br/>Shopify / email / phone] --> B[Service Cloud Case]
  B --> C{Type}
  C -->|Product<br/>advice| D[Knowledge<br/>+ agent]
  C -->|Damage /<br/>wrong item| E[RMA Flow]
  C -->|Warranty| F[Warranty<br/>Flow]
  E --> G[WMS return<br/>authorisation]
  E --> H[Refund / exchange<br/>via Shopify]
  G --> I[Inventory<br/>adjust]
  H --> J[Finance posting<br/>via MuleSoft]
```

**<span title="Phase 1">P1</span> minimum:** Case management, email-to-case, knowledge for top <span title="Stock Keeping Units">SKUs</span>, manual <span title="Return Merchandise Authorisation">RMA</span> checklist.  
**<span title="Phase 2">P2</span>:** Omni-channel chat, automated <span title="Return Merchandise Authorisation">RMA</span>, return reasons analytics feeding <span title="Product Information Management">PIM</span> content gaps.

### 6.5 <span title="Salesforce Experience Cloud">Experience Cloud</span> — retailer portal (closes G6)

| Phase | Capability |
|---|---|
| <span title="Phase 1">P1</span> | Sales-assisted PDF/availability reports from buffered <span title="Available to Promise">ATP</span> (interim) |
| <span title="Phase 2">P2</span> | Authenticated retailer portal: catalog visibility, <span title="Available to Promise">ATP</span>, order status, co-op assets |
| <span title="Phase 3">P3</span> | Forecast sharing / collaborative planning if retailers engage |

Do **not** build a second <span title="Business-to-Business">B2B</span> commerce checkout in <span title="Phase 1">P1</span> — it competes with <span title="Direct-to-Consumer">D2C</span> delivery focus and Salesforce <span title="Configure, Price, Quote">CPQ</span>.

### 6.6 <span title="Salesforce Marketing Cloud Engagement">Marketing Cloud</span> — intended use

| Journey / capability | Phase | Notes |
|---|---|---|
| Welcome / post-purchase | <span title="Phase 1">P1</span> | Low risk |
| Abandoned cart | <span title="Phase 1">P1</span> | Requires Shopify events + consent |
| Educational nurture (tank sizing, etc.) | <span title="Phase 1">P1</span>–<span title="Phase 2">P2</span> | Supports brand-control thesis |
| Win-back / replenishment | <span title="Phase 2">P2</span> | Needs purchase cadence data |
| Retailer-protected suppression | <span title="Phase 1">P1</span> flag → <span title="Phase 2">P2</span> automated | **Hard requirement** |
| Paid media audiences | <span title="Phase 2">P2</span> | Export consented segments |

**<span title="Salesforce Account Engagement (Pardot)">Account Engagement</span>** remains a fallback only if <span title="Salesforce Marketing Cloud Engagement">MC</span> commercials break the marketing budget — accept weaker cart-trigger capability if so.

### 6.7 Analytics & <span title="Direct-to-Consumer">D2C</span> <span title="Profit and Loss">P&L</span> (closes G5)

```mermaid
flowchart LR
  SH[Shopify] --> EL[MuleSoft / ETL]
  SF[Salesforce] --> EL
  MC[Marketing Cloud] --> EL
  WMS[WMS] --> EL
  SAP[SAP finance] --> EL
  EL --> WH[(Cloud warehouse<br/>or SF reporting mart)]
  WH --> BI[Board D2C P&L<br/>CAC · LTV · cohort · margin]
```

<span title="Phase 1">P1</span>: tagged finance extracts + Shopify/<span title="Salesforce">SF</span> native reports.  
<span title="Phase 2">P2</span>: proper warehouse + attribution.  
<span title="Salesforce Data Cloud">Data Cloud</span>: only after event quality is proven.

---

## 7. Fulfilment, <span title="Enterprise Resource Planning">ERP</span> & adjacent systems

### 7.1 <span title="SAP R/3">SAP R/3</span> interim strategy

| | |
|---|---|
| **Intended use (interim)** | <span title="Business-to-Business">B2B</span> <span title="System of Record">SoR</span>; <span title="Direct-to-Consumer">D2C</span> **financial** posting destination; not storefront <span title="Available to Promise">ATP</span> |
| **Why retained** | Replacement R12–18M / 18+ months misses <span title="Quarter 3">Q3</span> 2026 |
| **<span title="Phase 1">P1</span> pattern** | Nightly batch orders/settlements; manual exception queue; conservative reconciliation |
| **<span title="Phase 2">P2</span> pattern** | Near-real-time posting via <span title="MuleSoft Anypoint Platform">MuleSoft</span> <span title="System API (system-of-record wrapper)">System API</span> where <span title="SAP ERP">SAP</span> customisations allow |
| **<span title="Phase 3">P3</span> pattern** | Cloud <span title="Enterprise Resource Planning">ERP</span> (<span title="SAP S/4HANA">S/4HANA</span> Cloud, <span title="Microsoft Dynamics 365">D365</span>, or <span title="Oracle NetSuite">NetSuite</span>) behind the **same** OrderSettled contract |

| Pros of interim batch | Cons |
|---|---|
| Protects <span title="SAP ERP">SAP</span> from volume shock | Finance reconciliation debt |
| Buys time for <span title="Enterprise Resource Planning">ERP</span> selection | Overnight lag on financial truth |
| Forces <span title="Application Programming Interface">API</span> thinking early | Temptation to “just add another <span title="Remote Function Call">RFC</span>” — must be governed |

### 7.2 <span title="Direct-to-Consumer">D2C</span> inventory truth

**Hard rule:** Storefront <span title="Available to Promise">ATP</span> = **consumer-pack <span title="Warehouse Management System">WMS</span>** (minus safety buffer), never raw <span title="SAP ERP">SAP</span> pallet quantities.

```mermaid
sequenceDiagram
  autonumber
  participant WMS as D2C WMS
  participant Mule as MuleSoft
  participant SH as Shopify Plus
  participant SF as Salesforce

  WMS->>Mule: InventoryUpdated (each-pack qty)
  Note over Mule: Apply safety buffer / oversell kill-switch
  Mule->>SH: Set available quantity
  Mule->>SF: Service/Sales visibility optional
  alt ATP = 0
    Mule->>SH: Mark OOS / remove buyability
  end
```

### 7.3 <span title="Warehouse Management System">WMS</span> vs <span title="Third-Party Logistics">3PL</span> decision framework (closes G12)

| Option | When preferable | Pros | Cons |
|---|---|---|---|
| **Owned <span title="Direct-to-Consumer">D2C</span> FC + <span title="Warehouse Management System">WMS</span>** | Volume trajectory clear; want process control | Control, data richness | Capex, staffing (60–150 <span title="Full-Time Equivalent">FTE</span> cited), longer stand-up |
| **<span title="Third-Party Logistics">3PL</span> with <span title="Warehouse Management System">WMS</span> integration** | Speed to <span title="Phase 1">P1</span>/<span title="Phase 2">P2</span>; uncertain volume | Faster, opex-heavy, shifts labour risk | Less control; integration still required; margin leakage |
| **Hybrid** | Plant keeps bulk; <span title="Third-Party Logistics">3PL</span> does each-pick | Pragmatic | Two inventory pools to aggregate |

**Recommendation:** Run a **30-day commercial spike** in Phase 1 planning — if <span title="Third-Party Logistics">3PL</span> can meet 2-day metro SLAs and integrate via <span title="MuleSoft Anypoint Platform">MuleSoft</span> events, prefer **<span title="Third-Party Logistics">3PL</span> for Year 1** to protect <span title="Quarter 3">Q3</span>; revisit owned automation when order density justifies R7.5M.

---

## 8. Integration architecture

### 8.1 Why hub-and-spoke (reject more <span title="Point-to-Point">P2P</span>)

Apex already has **15** custom <span title="Point-to-Point">P2P</span> integrations. <span title="Direct-to-Consumer">D2C</span> adds ~**8–10** more connections. Point-to-point yields N×N growth, duplicated logic, and <span title="Enterprise Resource Planning">ERP</span> cutover rewrites.

| Point-to-point (reject) | Hub-and-spoke via <span title="Integration Platform as a Service">iPaaS</span> (adopt) |
|---|---|
| N×N connection growth | Spokes talk only to hub |
| Logic duplicated per pair | Reusable System / Process / Experience <span title="Application Programming Interfaces">APIs</span> |
| No central <span title="Dead Letter Queue">DLQ</span>/monitoring | One observability plane |
| <span title="Enterprise Resource Planning">ERP</span> cutover rewrites every link | Swap adapters; keep contracts |

### 8.2 Preferred hub — <span title="MuleSoft Anypoint Platform">MuleSoft Anypoint</span>

| | |
|---|---|
| **What it is** | Salesforce <span title="Integration Platform as a Service">iPaaS</span> / <span title="Application Programming Interface">API</span> management platform |
| **Performs** | <span title="API-led connectivity">API-led</span> integration, orchestration, <span title="SAP ERP">SAP</span>/<span title="Salesforce">SF</span> connectors, monitoring, governance |
| **Why preferred** | Strategic fit with Salesforce; proven hybrid <span title="SAP ERP">SAP</span>+<span title="Software as a Service">SaaS</span> pattern; enables Phase 3 <span title="Enterprise Resource Planning">ERP</span> swap |

| Pros | Cons |
|---|---|
| <span title="API-led connectivity">API-led</span> discipline matches target architecture | Licence + skills cost |
| Strong Salesforce / <span title="SAP ERP">SAP</span> connector story | Overkill if poorly scoped (avoid “boiling the ocean” in <span title="Phase 1">P1</span>) |
| Governance & reuse for 10+ year estate | Requires Integration Architect ownership |

**Alternative — <span title="Boomi (Dell Boomi)">Boomi</span>:** Acceptable if a 2–3 week spike shows better <span title="Total Cost of Ownership">TCO</span>/skills availability in <span title="South Africa">ZA</span>. Decide with evidence, not brand preference (<span title="Architecture Decision Record">ADR</span>-04).

### 8.3 <span title="API-led connectivity">API-led</span> layering

```mermaid
flowchart TB
  subgraph EXPAPI["Experience APIs"]
    E1[Shopify Experience API]
    E2[Salesforce Experience API]
    E3[WMS / Portal Experience API]
  end

  subgraph PROCAPI["Process APIs"]
    P1[Order Orchestration]
    P2[Inventory Aggregation]
    P3[Customer Match / Merge]
    P4[Product Publish]
    P5[Channel Conflict Guard]
  end

  subgraph SYSAPI["System APIs"]
    S1[Shopify System API]
    S2[Salesforce System API]
    S3[SAP System API]
    S4[WMS System API]
    S5[MDM / PIM System API]
    S6[Payments / Tax System API]
  end

  E1 --> P1
  E1 --> P2
  E2 --> P1
  E2 --> P3
  E2 --> P5
  E3 --> P1
  P1 --> S1
  P1 --> S2
  P1 --> S3
  P1 --> S4
  P1 --> S6
  P2 --> S4
  P2 --> S1
  P3 --> S2
  P3 --> S5
  P4 --> S5
  P4 --> S1
  P4 --> S2
  P5 --> S2
```

| Layer | Responsibility | Example |
|---|---|---|
| **<span title="System API (system-of-record wrapper)">System API</span>** | Unlock one <span title="System of Record">SoR</span> safely | `GET /sap/customers/{id}` |
| **<span title="Process API (orchestration layer)">Process API</span>** | Encode cross-system business process | `POST /orders/orchestrate` |
| **<span title="Experience API (channel-shaped API)">Experience API</span>** | Shape payload for one channel | Shopify webhook → canonical order |

### 8.4 Happy-path <span title="Direct-to-Consumer">D2C</span> order (Phase 2 steady-state)

```mermaid
sequenceDiagram
  autonumber
  actor Buyer
  participant Shopify as Shopify Plus
  participant Pay as Payment Gateway
  participant Tax as Tax Engine
  participant Mule as MuleSoft
  participant SF as Salesforce
  participant MDM as MDM
  participant WMS as D2C WMS
  participant SAP as SAP R/3

  Buyer->>Shopify: Browse / cart
  Shopify->>Tax: Quote tax
  Tax-->>Shopify: Tax lines
  Buyer->>Shopify: Checkout
  Shopify->>Pay: Authorise / capture (hosted)
  Pay-->>Shopify: Tokenized success
  Shopify->>Mule: OrderCreated
  Mule->>MDM: Resolve / create golden customer
  MDM-->>Mule: GoldenCustomerId
  Mule->>SF: Upsert Account/Contact + Order + Items
  Mule->>SF: Evaluate conflict + lead-routing rules
  Mule->>WMS: CreateFulfilmentOrder
  WMS-->>Mule: Accepted
  Mule->>SAP: Post sales / AR / COGS
  WMS->>Mule: ShipmentCreated + tracking
  Mule->>Shopify: Fulfilment update
  Mule->>SF: Shipment facts / MC journey entry
```

**Phase 1 explicit debt:** Shopify↔Salesforce via connector/native sync; **nightly** <span title="SAP ERP">SAP</span> finance batch; <span title="Available to Promise">ATP</span> buffers until <span title="Warehouse Management System">WMS</span> events exist; conflict rules largely configuration + manual Sales ops.

### 8.5 Integration matrix

| From → To | Via | Pattern | Phase |
|---|---|---|---|
| Shopify → Salesforce | Connector <span title="Phase 1">P1</span>; <span title="MuleSoft Anypoint Platform">MuleSoft</span> <span title="Phase 2">P2</span> | Event / upsert | <span title="Phase 1">P1</span>→<span title="Phase 2">P2</span> |
| Shopify → <span title="SAP ERP">SAP</span> | <span title="MuleSoft Anypoint Platform">MuleSoft</span> only | Batch → near-RT | <span title="Phase 1">P1</span>+ |
| Shopify → <span title="Warehouse Management System">WMS</span> | <span title="MuleSoft Anypoint Platform">MuleSoft</span> | Command + events | <span title="Phase 2">P2</span> |
| Salesforce ↔ <span title="Master Data Management">MDM</span> | <span title="MuleSoft Anypoint Platform">MuleSoft</span> | Match/merge | <span title="Phase 2">P2</span> |
| <span title="Product Information Management">PIM</span> → Shopify/<span title="Salesforce">SF</span> | <span title="MuleSoft Anypoint Platform">MuleSoft</span> | Publish | <span title="Phase 2">P2</span> |
| Payments → Shopify | Native hosted | Tokenized | <span title="Phase 1">P1</span> |
| Tax → Shopify/Mule | Tax <span title="Application Programming Interface">API</span> | Quote + commit | <span title="Phase 1">P1</span> |
| <span title="Salesforce Marketing Cloud Engagement">MC</span> ← events | <span title="Salesforce">SF</span> + <span title="MuleSoft Anypoint Platform">MuleSoft</span> | Journey entry | <span title="Phase 1">P1</span>/<span title="Phase 2">P2</span> |

**Hard rule after Phase 2 cutover:** No new direct Shopify↔<span title="SAP ERP">SAP</span>, Shopify↔<span title="Warehouse Management System">WMS</span>, or Salesforce↔<span title="SAP ERP">SAP</span> links outside the hub.

### 8.6 Commercial / margin guards in the fabric (closes G14)

<span title="Process API (orchestration layer)">Process API</span> checks (configurable):

- Minimum order value / shipping subsidy caps  
- Restricted postcodes (fulfilment <span title="Service Level Agreement">SLA</span>)  
- Retail-sensitive <span title="Stock Keeping Unit">SKU</span> gating  
- Fraud score thresholds before capture  

These belong in **Process <span title="Application Programming Interfaces">APIs</span> + Salesforce**, not only in Shopify Scripts — so they survive a commerce replatform.

---

## 9. Master data management

### 9.1 Domain ownership (golden-record map)

```mermaid
flowchart TB
  subgraph OWN["System of record by domain"]
    direction LR

    subgraph CUST["Customer"]
      direction TB
      C["Customer Party<br/>MDM golden record"]
      CN["Consent<br/>Salesforce"]
    end

    subgraph PROD["Product"]
      direction TB
      P["Product identity<br/>ERP SKU / BOM / cost"]
      PC["Product content<br/>PIM"]
    end

    subgraph INV["Inventory"]
      direction TB
      I["D2C ATP<br/>WMS"]
      IB["B2B bulk stock<br/>SAP"]
    end

    subgraph COM["Order and price"]
      direction TB
      O["Order — channel copy<br/>Shopify · SF · ERP"]
      PR["Price<br/>Shopify · CPQ · SAP"]
    end
  end

  classDef cust fill:#e8f4fc,stroke:#1a6ea8,color:#0b3d5c
  classDef prod fill:#eef8ee,stroke:#2d7a3a,color:#1a4d22
  classDef inv fill:#fef6e8,stroke:#b87a1a,color:#5c3d0a
  classDef com fill:#f3f0ea,stroke:#6b5b4a,color:#3d3429
  class C,CN cust
  class P,PC prod
  class I,IB inv
  class O,PR com
```

| Domain | System of Record | Consumers | Notes |
|---|---|---|---|
| **Party / Customer** | <span title="Master Data Management">MDM</span> (<span title="Phase 2">P2</span>); Salesforce engagement master in <span title="Phase 1">P1</span> | <span title="Salesforce">SF</span>, Shopify, <span title="SAP ERP">SAP</span>, <span title="Salesforce Marketing Cloud Engagement">MC</span> | Match on email/mobile/<span title="Value-Added Tax">VAT</span>/company keys |
| **<span title="Business-to-Business">B2B</span> Account hierarchy** | Salesforce | Portal, Sales | Retailer protection flags live here |
| **Product identity** | <span title="Enterprise Resource Planning">ERP</span> | <span title="Product Information Management">PIM</span>, <span title="Salesforce">SF</span> <span title="Salesforce Product2 object">Product2</span>, Shopify | Immutable <span title="Stock Keeping Unit">SKU</span>/<span title="Bill of Materials">BOM</span>/cost |
| **Product content** | <span title="Product Information Management">PIM</span> | Shopify, web, portal | Imagery, guides, <span title="Search Engine Optimisation">SEO</span> copy |
| **<span title="Direct-to-Consumer">D2C</span> <span title="Available to Promise">ATP</span>** | <span title="Warehouse Management System">WMS</span> | Shopify, <span title="Salesforce">SF</span>, portal | Buffered |
| **<span title="Business-to-Business">B2B</span> stock** | <span title="SAP ERP">SAP</span> | Sales, planning | Not for storefront |
| **<span title="Direct-to-Consumer">D2C</span> price** | Shopify price lists (+ <span title="Salesforce">SF</span> reference) | Storefront | Margin floors governed centrally |
| **<span title="Business-to-Business">B2B</span> price / quote** | <span title="SAP ERP">SAP</span> + <span title="Configure, Price, Quote">CPQ</span> | Sales | Never overwrite from Shopify |
| **Consent / preferences** | Salesforce | <span title="Salesforce Marketing Cloud Engagement">MC</span>, Shopify | <span title="Protection of Personal Information Act">POPIA</span> basis |
| **Order** | *Orchestrated* — Shopify sale, <span title="Salesforce">SF</span> engagement, <span title="Enterprise Resource Planning">ERP</span> finance | All | Canonical Order ID from <span title="MuleSoft Anypoint Platform">MuleSoft</span> |

### 9.2 Customer identity anti-duplicate flow (closes G9)

```mermaid
flowchart LR
  A[Shopify Customer] -->|email / mobile / consent| M[Customer Match Process API]
  B[Salesforce Contact] --> M
  C[SAP Customer] --> M
  M --> G[MDM Golden Record]
  G -->|Golden ID| A
  G -->|Golden ID| B
  G -->|Golden ID| C
  G -->|B2B-managed flag| MC[Marketing Cloud suppress]
```

**Survivorship (initial):** verified email > mobile > most-recently-enriched address; **<span title="Business-to-Business">B2B</span>-managed** and **retailer-protected** flags never auto-cleared by <span title="Direct-to-Consumer">D2C</span> activity without Sales workflow.

### 9.3 <span title="Master Data Management">MDM</span> + <span title="Product Information Management">PIM</span> tooling

| Layer | Phase 1 | Phase 2 |
|---|---|---|
| Customer match | Salesforce duplicate rules + <span title="MuleSoft Anypoint Platform">MuleSoft</span> <span title="Proof of Concept">PoC</span> | <span title="Master Data Management">MDM</span> (Informatica / Reltio / Semarchy-class) |
| Product content | Manual curation in Shopify for <span title="Minimum Viable Product">MVP</span> <span title="Stock Keeping Units">SKUs</span> | <span title="Product Information Management">PIM</span> (Akeneo / Salsify-class) |
| Governance | Data stewards named (Sales Ops + Marketing) | Stewardship workflows + DQ scorecards |

**Why not full <span title="Master Data Management">MDM</span> in Phase 1:** Tooling + stewardship rarely fit a commerce <span title="Minimum Viable Product">MVP</span> critical path; but **match keys, golden ID field, and survivorship policy must be designed in Phase 1** so <span title="Phase 2">P2</span> does not rework identity.

### 9.4 Channel conflict data model (closes G1)

Minimum Salesforce fields / flags:

| Flag / object | Purpose |
|---|---|
| `B2B_Managed__c` | Account under active retailer/sales ownership — suppress promo journeys |
| `Retailer_Protected_Geo__c` / <span title="Stock Keeping Unit">SKU</span> lists | Optional gating |
| `Channel_Conflict_Case__c` | Process retailer complaints |
| `D2C_Spend_Rolling_6m__c` | Lead routing threshold |
| `Assisted_by_Sales__c` on Order | Compensation attribution input |

```mermaid
sequenceDiagram
  participant SH as Shopify
  participant M as MuleSoft
  participant SF as Salesforce
  participant MC as Marketing Cloud

  SH->>M: CartAbandoned / Purchase
  M->>SF: Upsert engagement facts
  M->>SF: Read B2B_Managed / protection flags
  alt Not protected
    SF->>MC: Journey entry
  else Protected
    M-->>M: Suppress promotional journey
  end
```

---

## 10. Security, compliance & operability

### 10.1 <span title="Payment Card Industry Data Security Standard">PCI DSS</span>

| Pattern | Choice |
|---|---|
| Card data | **Never** store <span title="Primary Account Number">PAN</span> in Apex systems |
| Checkout | Hosted / tokenized (Shopify + gateway) |
| Target <span title="Self-Assessment Questionnaire">SAQ</span> | A or A-EP per <span title="Qualified Security Assessor">QSA</span> advice |
| Programme | <span title="Qualified Security Assessor">QSA</span> engaged in Phase 1; annual rhythm |

### 10.2 <span title="Protection of Personal Information Act">POPIA</span> & consent (closes G2)

South African <span title="Direct-to-Consumer">D2C</span> marketing without lawful basis is a programme-killer.

| Control | Implementation |
|---|---|
| Capture | Checkout + preference centre; purpose-specific consents |
| Store | Salesforce Consent model (or Custom Consent objects) as <span title="System of Record">SoR</span> |
| Sync | Shopify marketing flags ↔ Salesforce via <span title="MuleSoft Anypoint Platform">MuleSoft</span> |
| Enforce | <span title="Salesforce Marketing Cloud Engagement">MC</span> journeys require active consent + not <span title="Business-to-Business">B2B</span>-managed |
| Rights | Access/erasure process spanning Shopify, <span title="Salesforce">SF</span>, <span title="Salesforce Marketing Cloud Engagement">MC</span>, <span title="Master Data Management">MDM</span> |

### 10.3 Security & edge

| Control | Phase |
|---|---|
| Cloud edge (AWS/Azure), <span title="Web Application Firewall">WAF</span>, <span title="Distributed Denial of Service">DDoS</span> posture | <span title="Phase 1">P1</span> |
| Hybrid connectivity to on-prem <span title="SAP ERP">SAP</span> (<span title="Virtual Private Network">VPN</span>/private link) | <span title="Phase 1">P1</span> |
| Fraud scoring at payment | <span title="Phase 1">P1</span> |
| <span title="Security Information and Event Management">SIEM</span> + managed detection | <span title="Phase 1">P1</span> (managed service) |
| Pen test before launch | <span title="Phase 1">P1</span> gate |
| Bot protection on storefront | <span title="Phase 1">P1</span> |

### 10.4 Service objectives (closes G10)

| Service | Target (initial) | Notes |
|---|---|---|
| Storefront availability | 99.9% monthly | Shopify + edge |
| Checkout success | Monitor conversion funnel | Alert on payment error spikes |
| Order → <span title="Warehouse Management System">WMS</span> accept | &lt; 5 min <span title="Phase 2">P2</span>; best-effort <span title="Phase 1">P1</span> | |
| Inventory publish | &lt; 5 min <span title="Phase 2">P2</span> | Buffers in <span title="Phase 1">P1</span> |
| <span title="Recovery Point Objective">RPO</span> (order events) | ≤ 15 min | Durable queue / replay |
| <span title="Recovery Time Objective">RTO</span> (integration hub) | ≤ 4 hours | Runbook + managed ops |
| Support hours | Storefront 24/7 platform; human support business+extended | Staffing model Section 11 |

---

## 11. Operating model & change

### 11.1 Delivery & run model

| Role | Model |
|---|---|
| Integration Architect | **Hire** (owns contracts, <span title="Architecture Decision Record">ADR</span> governance) |
| <span title="Development + Operations">DevOps</span> / cloud | **Hire or dedicated contractor** |
| Shopify + Salesforce build | **<span title="System Integrator">SI</span>** |
| <span title="MuleSoft Anypoint Platform">MuleSoft</span> build | **<span title="System Integrator">SI</span>** under Architect direction |
| 24/7 monitoring / <span title="Security Operations Centre">SOC</span> | **Managed service** |
| <span title="Qualified Security Assessor">QSA</span> / <span title="Payment Card Industry">PCI</span> | **Specialist** |
| Internal <span title="Information Technology">IT</span> (7) | Keep <span title="SAP ERP">SAP</span>/<span title="Business-to-Business">B2B</span> lights-on; do not divert entirely to <span title="Direct-to-Consumer">D2C</span> |

### 11.2 Organisational change (closes G7)

Technology cannot fix ambiguous commission policy.

| Workstream | Owner | Output before launch |
|---|---|---|
| Retailer communication pack | Retail Relations | Positioning: small/custom/rush; not Builders-shelf clone |
| Sales compensation policy | VP Sales + <span title="Chief Financial Officer">CFO</span> | Written rules for cannibalisation & assisted deals |
| <span title="Direct-to-Consumer">D2C</span> team design | <span title="Chief Executive Officer">CEO</span> / COO | Separate digital <span title="Profit and Loss">P&L</span> team vs. “everyone owns <span title="Direct-to-Consumer">D2C</span>” |
| Data stewardship | Sales Ops + Marketing | Golden record dispute process |
| Returns policy | Service + Finance | Customer-clear <span title="Return Merchandise Authorisation">RMA</span> terms |

### 11.3 Governance artefacts

- Architecture Decision Records (see Section 14)  
- Versioned <span title="OpenAPI Specification">OpenAPI</span> contracts in Apex Git  
- Integration backlog with <span title="Dead Letter Queue">DLQ</span> ownership  
- Monthly architecture review (<span title="Chief Technology Officer">CTO</span> + Integration Architect + <span title="System Integrator">SI</span> leads)

---

## 12. Risks & mitigations

| ID | Risk | Impact | Likelihood | Mitigation |
|---|---|---|---|---|
| R1 | Retailer backlash / channel conflict | Existential revenue | M | Conflict flags, assortment rules, exec sponsor messaging, portal transparency <span title="Phase 2">P2</span> |
| R2 | <span title="SAP ERP">SAP</span> performance / opaque customisations | Finance break; launch slip | H | Week 1–3 interface inventory; nightly batch; volume isolation; kill-switch |
| R3 | Inventory oversell | <span title="Customer Experience">CX</span> damage; margin hit | H | <span title="Warehouse Management System">WMS</span> as <span title="Available to Promise">ATP</span> <span title="System of Record">SoR</span>; safety buffers; <span title="Out of Stock">OOS</span> automation; <span title="Phase 2">P2</span> near-RT mandatory |
| R4 | Unit economics negative at ~R333 <span title="Average Order Value">AOV</span> | Strategy failure | M | <span title="Third-Party Logistics">3PL</span> spike; shipping caps; <span title="Minimum Viable Product">MVP</span> <span title="Stock Keeping Unit">SKU</span> margin model; packaging programme |
| R5 | <span title="Payment Card Industry">PCI</span> / security incident | Legal + brand | M | Hosted checkout; <span title="Qualified Security Assessor">QSA</span>; <span title="Web Application Firewall">WAF</span>; fraud; pen test gate |
| R6 | <span title="Protection of Personal Information Act">POPIA</span> non-compliance | Fines + <span title="Salesforce Marketing Cloud Engagement">MC</span> shutdown | M | Consent <span title="System of Record">SoR</span>; journey gates; <span title="Data Protection Impact Assessment">DPIA</span> |
| R7 | Integration sprawl resumes | Unmaintainable estate | M | Hub mandate; no <span title="Point-to-Point">P2P</span> exceptions without <span title="Architecture Decision Record">ADR</span> |
| R8 | Shopify logic lock-in | Costly replatform | M | No critical business rules only in Liquid; contracts in <span title="MuleSoft Anypoint Platform">MuleSoft</span>/<span title="Salesforce">SF</span> |
| R9 | Skills / 7-<span title="Full-Time Equivalent">FTE</span> overload | Outages; burnout | H | Hybrid hire + <span title="System Integrator">SI</span> + managed ops; protect <span title="Business-to-Business">B2B</span> support capacity |
| R10 | Timeline pressure → skipped debt gates | Permanent temporary | H | Exit criteria Section 13.4; <span title="Chief Technology Officer">CTO</span> <span title="Red / Amber / Green">RAG</span> on debt items |
| R11 | Sales sabotage via ambiguous commission | Adoption failure | M | Policy before go-live; attribution flags |
| R12 | <span title="Master Data Management">MDM</span> delayed forever | Duplicate chaos | M | <span title="Phase 1">P1</span> identity design mandatory; <span title="Phase 2">P2</span> funding ring-fenced |
| R13 | Tax/<span title="Southern African Development Community">SADC</span> edge cases | Compliance / margin | M | Tax engine <span title="Phase 1">P1</span>; finance exception queue |
| R14 | Returns surge | Cost + <span title="Customer Experience">CX</span> | M | Knowledge base; clear specs; <span title="Return Merchandise Authorisation">RMA</span> Flow; reason codes → <span title="Product Information Management">PIM</span> |

```mermaid
flowchart LR
  subgraph RISKS["Top programme risks"]
    direction TB
    R1["R1 · Channel conflict"]
    R2["R2 · SAP fragility"]
    R3["R3 · Oversell"]
    R9["R9 · Skills / capacity"]
    R10["R10 · Debt forever"]
  end

  subgraph MITS["Primary mitigations"]
    direction TB
    M1["Flags · assortment<br/>exec narrative"]
    M2["Batch isolation<br/>interface inventory"]
    M3["WMS ATP · buffers<br/>kill-switch"]
    M9["Hybrid operating<br/>model"]
    M10["Exit criteria<br/>+ CTO RAG"]
  end

  R1 --> M1
  R2 --> M2
  R3 --> M3
  R9 --> M9
  R10 --> M10

  classDef risk fill:#fdecea,stroke:#b42318,color:#7a271a
  classDef mit fill:#eaf7ef,stroke:#1f7a3f,color:#14532d
  class R1,R2,R3,R9,R10 risk
  class M1,M2,M3,M9,M10 mit
```

---

## 13. Phased roadmap

### 13.1 Timeline overview

```mermaid
gantt
  title Apex Direct technology roadmap
  dateFormat  YYYY-MM
  axisFormat  %b %Y

  section Phase 1 — Launch slice
  Discovery spikes SAP + iPaaS     :p1a, 2026-01, 2026-02
  Shopify Plus MVP + payments/tax  :p1b, 2026-02, 2026-07
  Salesforce EE + Service + basic MC :p1c, 2026-02, 2026-07
  PCI / cloud edge / consent       :p1d, 2026-03, 2026-07
  Nightly SAP batch + buffers      :p1e, 2026-04, 2026-08
  Go-live Apex Direct              :milestone, 2026-08, 0d

  section Phase 2 — Platformize
  MuleSoft API fabric              :p2a, 2026-10, 2027-03
  MDM + PIM                        :p2b, 2026-11, 2027-04
  D2C WMS / 3PL near-RT inventory  :p2c, 2026-10, 2027-04
  Advanced MC + retailer portal    :p2d, 2027-01, 2027-05

  section Phase 3 — ERP modernise
  Cloud ERP select + implement     :p3a, 2027-07, 2028-01
  Adapter swap / retire R/3 path   :p3b, 2027-10, 2028-02
  Optional SFCC study              :p3c, 2028-01, 2028-03
```

### 13.2 Phase detail

#### Phase 1 — Thin vertical slice (<span title="Quarter 1">Q1</span>–<span title="Quarter 3">Q3</span> 2026) · Budget R6–8M

**Outcome:** Paying customers on Apex Direct; Salesforce has customer+order engagement; finance gets nightly postings; <span title="Payment Card Industry">PCI</span> programme live; retailers briefed; conflict flags operational.

| Workstream  | In scope                                                       | Explicitly out                        |
| ----------- | -------------------------------------------------------------- | ------------------------------------- |
| Commerce    | <span title="Shopify Plus">Shopify Plus</span> <span title="Minimum Viable Product">MVP</span> catalog, checkout, payments, tax              | <span title="Salesforce Commerce Cloud">SFCC</span>; 8,500 <span title="Stock Keeping Unit">SKU</span> perfection            |
| Salesforce  | <span title="Enterprise Edition">EE</span> upgrade, Sales+Service foundation, lead routing v1, consent | Full Omni, <span title="Salesforce Data Cloud">Data Cloud</span>                 |
| Marketing   | <span title="Salesforce Marketing Cloud Engagement">MC</span> welcome / abandon / post-purchase                           | Advanced personalisation engine       |
| Integration | Connector <span title="Salesforce">SF</span>↔Shopify; nightly <span title="SAP ERP">SAP</span> batch; tax/pay native        | Full <span title="MuleSoft Anypoint Platform">MuleSoft</span> fabric                  |
| Data        | Duplicate rules; golden ID fields reserved                     | Full <span title="Master Data Management">MDM</span>/<span title="Product Information Management">PIM</span>                          |
| Ops         | Packaging for <span title="Minimum Viable Product">MVP</span> <span title="Stock Keeping Units">SKUs</span>; fulfilment interim (<span title="Third-Party Logistics">3PL</span> or pilot FC)   | Full warehouse automation             |
| Security    | <span title="Qualified Security Assessor">QSA</span>, <span title="Web Application Firewall">WAF</span>, fraud, pen test                                      | Mature <span title="Security Information and Event Management">SIEM</span> use-cases beyond baseline |

#### Phase 2 — Platformize (<span title="Quarter 4">Q4</span> 2026–<span title="Quarter 2">Q2</span> 2027) · Budget R4–5.5M

**Outcome:** Hub-and-spoke live; near-RT inventory; <span title="Master Data Management">MDM</span>/<span title="Product Information Management">PIM</span>; advanced journeys; retailer portal; debt from <span title="Phase 1">P1</span> retired on schedule.

#### Phase 3 — Cloud <span title="Enterprise Resource Planning">ERP</span> (<span title="Quarter 3">Q3</span> 2027–<span title="Quarter 1">Q1</span> 2028) · Budget R12.5–17.5M

**Outcome:** Modern <span title="Enterprise Resource Planning">ERP</span>; <span title="SAP R/3">SAP R/3</span> path retired; same Order/Inventory/Customer contracts; optional <span title="Salesforce Commerce Cloud">SFCC</span> business case using Section 5.3 gates.

### 13.3 Budget roll-up (technology programme)

| Phase | Tech budget | Primary spend |
|---|---|---|
| <span title="Phase 1">P1</span> | R6–8M | Commerce <span title="System Integrator">SI</span>, <span title="Salesforce">SF</span> licences/<span title="System Integrator">SI</span>, payments/tax, <span title="Payment Card Industry">PCI</span>/cloud, interim integration |
| <span title="Phase 2">P2</span> | R4–5.5M | <span title="MuleSoft Anypoint Platform">MuleSoft</span>, <span title="Master Data Management">MDM</span>/<span title="Product Information Management">PIM</span>, <span title="Warehouse Management System">WMS</span>/<span title="Third-Party Logistics">3PL</span> integration, portal, advanced <span title="Salesforce Marketing Cloud Engagement">MC</span> |
| <span title="Phase 3">P3</span> | R12.5–17.5M | Cloud <span title="Enterprise Resource Planning">ERP</span> + migration |
| **Note** | *Excludes* full R7.5M warehouse automation & R3M <span title="Year 1">Y1</span> marketing media if tracked separately by <span title="Chief Financial Officer">CFO</span> | Keep <span title="Profit and Loss">P&L</span> views distinct |

### 13.4 Phase exit criteria (closes G8)

| Phase | Must be true to exit |
|---|---|
| **<span title="Phase 1">P1</span> → <span title="Phase 2">P2</span>** | Storefront live; payment/tax stable; nightly <span title="SAP ERP">SAP</span> reconciliation &lt; agreed error rate; consent enforced; conflict flags used by Sales; pen test findings closed or risk-accepted; Integration Architect hired; <span title="OpenAPI Specification">OpenAPI</span> stubs for Order/Inventory/Customer/Product in Git |
| **<span title="Phase 2">P2</span> → <span title="Phase 3">P3</span>** | <span title="MuleSoft Anypoint Platform">MuleSoft</span> hub mandatory path for new integrations; <span title="Warehouse Management System">WMS</span>/<span title="Third-Party Logistics">3PL</span> <span title="Available to Promise">ATP</span> driving storefront; <span title="Master Data Management">MDM</span> golden IDs on ≥95% active <span title="Direct-to-Consumer">D2C</span> customers; <span title="Product Information Management">PIM</span> publishing <span title="Minimum Viable Product">MVP</span>+wave2; retailer portal <span title="Available to Promise">ATP</span> live; <span title="Phase 1">P1</span> connector debt retired |
| **<span title="Phase 3">P3</span> done** | Cloud <span title="Enterprise Resource Planning">ERP</span> posting <span title="Direct-to-Consumer">D2C</span>+<span title="Business-to-Business">B2B</span>; R/3 decommission plan executed; <span title="Salesforce Commerce Cloud">SFCC</span> decision recorded (proceed or explicitly defer) |

### 13.5 First 30 days (immediate action plan)

1. Appoint executive sponsor + Architecture Owner (<span title="Chief Technology Officer">CTO</span>).  
2. Engage Shopify <span title="System Integrator">SI</span> shortlist + Salesforce <span title="System Integrator">SI</span>; issue <span title="MuleSoft Anypoint Platform">MuleSoft</span> vs <span title="Boomi (Dell Boomi)">Boomi</span> spike charter.  
3. Start **<span title="SAP ERP">SAP</span> interface inventory** (custom RFCs, files, tables touching orders/customers/stock).  
4. Engage <span title="Qualified Security Assessor">QSA</span> for <span title="Payment Card Industry">PCI</span> scoping workshop.  
5. Draft retailer communication + commission policy (business).  
6. Select <span title="Minimum Viable Product">MVP</span> assortment (150–300 <span title="Stock Keeping Units">SKUs</span>) with Sales + Marketing + Ops.  
7. Stand up <span title="Architecture Decision Record">ADR</span> log and <span title="Application Programming Interface">API</span> contract repo.

---

## 14. Architecture Decision Records

| ID         | Decision                                                                         | Status   |
| ---------- | -------------------------------------------------------------------------------- | -------- |
| **<span title="Architecture Decision Record">ADR</span>-01** | Commerce = <span title="Shopify Plus">Shopify Plus</span> (<span title="Phase 1">P1</span>); <span title="Salesforce Commerce Cloud">SFCC</span> gated revisit post-<span title="Enterprise Resource Planning">ERP</span>; Adobe rejected for <span title="Phase 1">P1</span> | Proposed |
| **<span title="Architecture Decision Record">ADR</span>-02** | <span title="Customer Relationship Management">CRM</span> hub = Salesforce Enterprise (Sales + Service)                                | Proposed |
| **<span title="Architecture Decision Record">ADR</span>-03** | Marketing = <span title="Salesforce Marketing Cloud Engagement">Marketing Cloud Engagement</span>; <span title="Salesforce Account Engagement (Pardot)">Account Engagement</span> fallback only         | Proposed |
| **<span title="Architecture Decision Record">ADR</span>-04** | <span title="Integration Platform as a Service">iPaaS</span> = <span title="MuleSoft Anypoint Platform">MuleSoft</span> preferred; <span title="Boomi (Dell Boomi)">Boomi</span> via spike evidence                             | Proposed |
| **<span title="Architecture Decision Record">ADR</span>-05** | <span title="Phase 1">P1</span> <span title="SAP ERP">SAP</span> = nightly batch (named debt)                                              | Proposed |
| **<span title="Architecture Decision Record">ADR</span>-06** | <span title="Master Data Management">MDM</span>/<span title="Product Information Management">PIM</span> implement in <span title="Phase 2">P2</span>; identity *design* mandatory in <span title="Phase 1">P1</span>                       | Proposed |
| **<span title="Architecture Decision Record">ADR</span>-07** | <span title="Direct-to-Consumer">D2C</span> inventory <span title="System of Record">SoR</span> = <span title="Warehouse Management System">WMS</span>/<span title="Third-Party Logistics">3PL</span> each-pack, not <span title="SAP ERP">SAP</span> pallets                           | Proposed |
| **<span title="Architecture Decision Record">ADR</span>-08** | <span title="Payment Card Industry">PCI</span> via hosted checkout; <span title="Qualified Security Assessor">QSA</span> in <span title="Phase 1">P1</span>                                               | Proposed |
| **<span title="Architecture Decision Record">ADR</span>-09** | Cloud <span title="Enterprise Resource Planning">ERP</span> deferred to Phase 3                                                    | Proposed |
| **<span title="Architecture Decision Record">ADR</span>-10** | Hybrid delivery (hires + <span title="System Integrator">SI</span> + managed ops)                                       | Proposed |
| **<span title="Architecture Decision Record">ADR</span>-11** | Channel-conflict flags & journey suppression are <span title="Phase 1">P1</span> controls                     | Proposed |
| **<span title="Architecture Decision Record">ADR</span>-12** | <span title="Protection of Personal Information Act">POPIA</span> consent <span title="System of Record">SoR</span> = Salesforce                                                   | Proposed |
| **<span title="Architecture Decision Record">ADR</span>-13** | Prefer <span title="Third-Party Logistics">3PL</span> Year-1 if <span title="Service Level Agreement">SLA</span>/integration spike passes; else owned FC                 | Proposed |
| **<span title="Architecture Decision Record">ADR</span>-14** | Critical commercial rules live in Process <span title="Application Programming Interfaces">APIs</span> + Salesforce, not Shopify-only    | Proposed |
| **<span title="Architecture Decision Record">ADR</span>-15** | <span title="Salesforce Data Cloud">Data Cloud</span> deferred until event quality proven                                   | Proposed |

---

## 15. Citations & further reading

### Commerce

- <span title="Shopify Plus">Shopify Plus</span>: https://www.shopify.com/plus  
- <span title="Salesforce Commerce Cloud">Salesforce Commerce Cloud</span>: https://www.salesforce.com/commerce/  
- Contra Collective — <span title="Shopify Plus">Shopify Plus</span> vs <span title="Salesforce Commerce Cloud">SFCC</span> (2026 framing): https://contracollective.com/blog/shopify-plus-vs-salesforce-commerce-cloud-2026  
- Uncap — <span title="Salesforce Commerce Cloud">SFCC</span> vs <span title="Shopify Plus">Shopify Plus</span>: https://www.uncap.com/comparison/salesforce-commerce-cloud-vs-shopify  
- Ask Phill — <span title="Shopify Plus">Shopify Plus</span> vs <span title="Salesforce Commerce Cloud">SFCC</span>: https://askphill.com/blogs/blog/comparing-shopify-plus-and-salesforce-commerce-cloud  

### Salesforce <span title="Salesforce Customer 360">Customer 360</span>

- Salesforce <span title="Customer Relationship Management">CRM</span>: https://www.salesforce.com/crm/  
- <span title="Salesforce Sales Cloud">Sales Cloud</span>: https://www.salesforce.com/sales/cloud/guide/  
- <span title="Salesforce Service Cloud">Service Cloud</span>: https://www.salesforce.com/service/cloud/guide/  
- <span title="Salesforce Marketing Cloud Engagement">Marketing Cloud</span>: https://www.salesforce.com/marketing/  
- <span title="Salesforce Experience Cloud">Experience Cloud</span>: https://www.salesforce.com/products/experience-cloud/overview/  
- Salesforce Well-Architected: https://architect.salesforce.com/well-architected  
- Salesforce Architect Decision Guides: https://architect.salesforce.com/decision-guides  

### Integration / <span title="API-led connectivity">API-led</span>

- <span title="API-led connectivity">API-led</span> connectivity (Salesforce): https://www.salesforce.com/blog/api-led-connectivity/  
- <span title="MuleSoft Anypoint Platform">MuleSoft Anypoint</span>: https://www.salesforce.com/mulesoft/  
- <span title="MuleSoft Anypoint Platform">MuleSoft</span> — unlock <span title="Integration Platform as a Service">iPaaS</span> with <span title="API-led connectivity">API-led</span> connectivity: https://blogs.mulesoft.com/learn-apis/api-led-connectivity/how-to-unlock-ipaas-potential-with-api-led-connectivity/  
- <span title="Boomi (Dell Boomi)">Boomi</span>: https://boomi.com/  
- <span title="MuleSoft Anypoint Platform">MuleSoft</span> Catalyst / implementation methodology: https://www.mulesoft.com/platform/api/catalyst-methodology  

### Architecture practice

- <span title="The Open Group Architecture Framework">TOGAF</span> <span title="Architecture Development Method">ADM</span> overview (The Open Group): https://www.opengroup.org/togaf  
- <span title="OpenAPI Specification">OpenAPI</span> Specification: https://swagger.io/specification/  
- Thoughtworks — Evolutionary architecture: https://www.thoughtworks.com/architecture  
- MariaDB/enterprise integration patterns reference (EIP): https://www.enterpriseintegrationpatterns.com/  

### <span title="Payment Card Industry">PCI</span> / security

- <span title="Payment Card Industry">PCI</span> SSC: https://www.pcisecuritystandards.org/  
- Shopify <span title="Payment Card Industry">PCI</span> checklist: https://www.shopify.com/enterprise/blog/pci-compliance-checklist  
- Shopify Help — <span title="Payment Card Industry">PCI</span>: https://help.shopify.com/en/manual/payments/pci-dss  

### Privacy (South Africa)

- Information Regulator (<span title="Protection of Personal Information Act">POPIA</span>): https://inforegulator.org.za/  
- <span title="Protection of Personal Information Act">POPIA</span> Act overview (Justice): https://www.justice.gov.za/inforeg/  

### <span title="Master Data Management">MDM</span> / <span title="Product Information Management">PIM</span>

- IBM — What is <span title="Master Data Management">MDM</span>: https://www.ibm.com/topics/master-data-management  
- Akeneo — What is <span title="Product Information Management">PIM</span>: https://www.akeneo.com/what-is-pim/  
- Salsify PXM: https://www.salsify.com/product-experience-management  

### <span title="Enterprise Resource Planning">ERP</span>

- <span title="SAP ERP">SAP</span> <span title="SAP S/4HANA">S/4HANA</span>: https://www.sap.com/products/erp/s4hana.html  
- Microsoft Dynamics 365: https://www.microsoft.com/en-us/dynamics-365  
- Oracle <span title="Oracle NetSuite">NetSuite</span>: https://www.netsuite.com/  

---

## 16. Appendix A — Capability ↔ platform catalogue

| Business capability | Platform | Phase |
|---|---|---|
| <span title="Direct-to-Consumer">D2C</span> browse/buy | <span title="Shopify Plus">Shopify Plus</span> | <span title="Phase 1">P1</span> |
| Payment take & fraud | Gateway + Shopify | <span title="Phase 1">P1</span> |
| Tax calc <span title="South Africa">ZA</span>/<span title="Southern African Development Community">SADC</span> | Tax engine | <span title="Phase 1">P1</span> |
| Customer service / returns | <span title="Salesforce Service Cloud">Service Cloud</span> | <span title="Phase 1">P1</span> |
| <span title="Business-to-Business">B2B</span> sell / tender / <span title="Configure, Price, Quote">CPQ</span> | <span title="Salesforce Sales Cloud">Sales Cloud</span> + <span title="Configure, Price, Quote">CPQ</span> | <span title="Phase 1">P1</span> |
| Journey messaging | <span title="Salesforce Marketing Cloud Engagement">Marketing Cloud</span> | <span title="Phase 1">P1</span> |
| Lead escalation | <span title="Salesforce Sales Cloud">Sales Cloud</span> automation | <span title="Phase 1">P1</span> |
| Channel conflict control | Salesforce flags + <span title="Salesforce Marketing Cloud Engagement">MC</span> suppress | <span title="Phase 1">P1</span> |
| Consent | Salesforce | <span title="Phase 1">P1</span> |
| Order orchestration | <span title="MuleSoft Anypoint Platform">MuleSoft</span> <span title="Process API (orchestration layer)">Process API</span> | <span title="Phase 2">P2</span> (thin <span title="Phase 1">P1</span>) |
| Customer golden record | <span title="Master Data Management">MDM</span> | <span title="Phase 2">P2</span> |
| Product content | <span title="Product Information Management">PIM</span> | <span title="Phase 2">P2</span> |
| <span title="Direct-to-Consumer">D2C</span> pick/pack/ship | <span title="Warehouse Management System">WMS</span> or <span title="Third-Party Logistics">3PL</span> | <span title="Phase 2">P2</span> (interim <span title="Phase 1">P1</span>) |
| Retailer portal | <span title="Salesforce Experience Cloud">Experience Cloud</span> | <span title="Phase 2">P2</span> |
| Financial <span title="System of Record">SoR</span> | <span title="SAP ERP">SAP</span> → Cloud <span title="Enterprise Resource Planning">ERP</span> | <span title="Phase 1">P1</span> batch / <span title="Phase 3">P3</span> modern |
| Board <span title="Direct-to-Consumer">D2C</span> <span title="Profit and Loss">P&L</span> | <span title="Business Intelligence">BI</span> mart | <span title="Phase 1">P1</span> light / <span title="Phase 2">P2</span> full |

---

## 17. Appendix B — Glossary (short)

Essential architectural terms for quick reference. For the full acronym list grouped by theme, see **Appendix C**.

| Term | Meaning here |
|---|---|
| **System of Sale** | Where the cart/checkout happens (Shopify) |
| **System of Engagement** | Where relationships & journeys live (Salesforce) |
| **System of Record (SoR)** | Authoritative master for a domain (ERP / MDM / WMS / PIM) |
| **Named debt** | Accepted shortcut with owner, phase, and exit criteria |
| **ATP** | Available to Promise — sellable quantity you can commit now |
| **POPIA** | South Africa’s Protection of Personal Information Act |

---

## 18. Appendix C — Glossary (long)

Acronyms and abbreviations used throughout this assessment, grouped by platform or theme. Use this when a term appears without expansion in the body.

### C.1 Business & commercial

| Abbreviation | Full name | Plain-English meaning |
|---|---|---|
| **B2B** | Business-to-Business | Apex’s existing wholesale / retailer / tender channel |
| **B2C** | Business-to-Consumer | End-consumer selling (overlaps with D2C) |
| **D2C** | Direct-to-Consumer | Apex selling direct via Apex Direct, not through retailers |
| **AOV** | Average Order Value | Mean basket size (~R333–R350 in early D2C modelling) |
| **CAC** | Customer Acquisition Cost | Marketing spend to win one customer |
| **LTV** | Lifetime Value | Expected value of a customer over their relationship |
| **GMV** | Gross Merchandise Value | Total storefront sales value before deductions |
| **SKU** | Stock Keeping Unit | Unique product/item code (~8,500 in Apex’s catalogue) |
| **MOQ** | Minimum Order Quantity | Smallest order quantity sales/manufacturing accept (B2B ~R15k) |
| **P&L** | Profit and Loss | Financial statement; board wants a separate D2C P&L |
| **ROI** | Return on Investment | Benefit relative to programme cost |
| **TCO** | Total Cost of Ownership | Licence + implementation + run + people over time |
| **CX** | Customer Experience | Quality of the buyer’s end-to-end journey |
| **UX** | User Experience | Ease and clarity of digital interfaces |
| **SEO** | Search Engine Optimisation | Making product/content discoverable in search |
| **KPI** | Key Performance Indicator | Metric used to judge programme or channel success |
| **Y1 / Y3** | Year 1 / Year 3 | First and third year of D2C operation |
| **Q1–Q4** | Quarter 1–4 | Calendar/fiscal quarters (launch target **Q3 2026**) |
| **ZA** | South Africa (ISO country code) | Primary market |
| **SADC** | Southern African Development Community | Regional bloc affecting cross-border tax and shipping |
| **R / ZAR** | South African Rand | Currency for all R-denominated figures in this document |

### C.2 Salesforce & CRM

| Abbreviation | Full name | Plain-English meaning |
|---|---|---|
| **CRM** | Customer Relationship Management | Software for managing customer relationships and interactions |
| **SF / SFDC** | Salesforce (Dot Com) | The Salesforce platform / company |
| **PE** | Professional Edition | Current Apex Salesforce licence tier (limited APIs/automation) |
| **EE** | Enterprise Edition | Recommended upgrade for API volume, automation, and sharing |
| **Sales Cloud** | Salesforce Sales Cloud | CRM for accounts, pipeline, opportunities, B2B selling |
| **Service Cloud** | Salesforce Service Cloud | Cases, knowledge, returns/RMA, omni-channel support |
| **Marketing Cloud / MC** | Salesforce Marketing Cloud Engagement | Journeys, triggered email/SMS, segmentation, suppression |
| **Experience Cloud** | Salesforce Experience Cloud | Branded portal sites on Salesforce (retailer portal) |
| **SFCC / Commerce Cloud** | Salesforce Commerce Cloud | Salesforce enterprise commerce platform (deferred in P1) |
| **CPQ** | Configure, Price, Quote | Complex/custom product quoting (e.g. custom moulding) |
| **Customer 360** | Salesforce Customer 360 | One connected customer view across Salesforce clouds |
| **Data Cloud** | Salesforce Data Cloud | Optional CDP-class platform (evaluate later) |
| **CDP** | Customer Data Platform | Unifies profiles/events for personalisation and analytics |
| **Product2** | Salesforce Product2 object | Standard object for product master records in Salesforce |
| **Flow** | Salesforce Flow | Declarative automation used for routing, RMA, escalations |

### C.3 Commerce, payments, tax & content

| Abbreviation | Full name | Plain-English meaning |
|---|---|---|
| **Shopify Plus** | Shopify Plus | Enterprise Shopify plan chosen as Phase 1 system of sale |
| **OMS** | Order Management System | Orchestrates order capture, fulfilment, and status |
| **PIM** | Product Information Management | Shopper-facing product content (copy, specs, imagery) |
| **CMS** | Content Management System | Manages web/education content beyond pure product data |
| **ATP** | Available to Promise | Sellable quantity you can commit to a customer now |
| **OOS** | Out of Stock | Inventory state: cannot sell |
| **RMA** | Return Merchandise Authorisation | Process/approval to return goods |
| **3PL** | Third-Party Logistics | Outsourced warehousing and fulfilment provider |
| **PAN** | Primary Account Number | Full card number — must not be stored in Apex systems |
| **PCI** | Payment Card Industry | Shorthand for card-acceptance security requirements |
| **PCI DSS** | Payment Card Industry Data Security Standard | Security standard for organisations that take cards |
| **SAQ** | Self-Assessment Questionnaire | PCI compliance questionnaire merchants complete |
| **SAQ A / A-EP** | SAQ A / SAQ A-EP | Lighter PCI questionnaires when checkout is hosted/tokenized |
| **QSA** | Qualified Security Assessor | Accredited firm that assesses PCI compliance |
| **VAT** | Value-Added Tax | Consumption tax (critical for ZA + SADC) |
| **Liquid** | Shopify Liquid | Shopify’s theme/templating language (avoid burying business rules here) |

### C.4 Integration & middleware

| Abbreviation | Full name | Plain-English meaning |
|---|---|---|
| **API** | Application Programming Interface | Contract for systems to exchange data or commands |
| **OpenAPI** | OpenAPI Specification | Standard format for documenting REST APIs (versioned in Git) |
| **iPaaS** | Integration Platform as a Service | Cloud platform for building/running integrations |
| **MuleSoft / Anypoint** | MuleSoft Anypoint Platform | Preferred iPaaS / integration fabric for Apex Direct |
| **Boomi** | Boomi (Dell Boomi) | Alternative iPaaS considered on TCO grounds |
| **P2P** | Point-to-Point | Direct system-to-system link (fragile; stop growing these) |
| **API-led** | API-led connectivity | MuleSoft pattern: Experience / Process / System API layers |
| **Experience API** | Experience API | Channel-shaped API for a consumer (storefront, portal, app) |
| **Process API** | Process API | Orchestration and business rules across systems |
| **System API** | System API | Thin wrapper exposing one system of record cleanly |
| **ETL** | Extract, Transform, Load | Batch data movement and transformation |
| **EDA** | Event-Driven Architecture | Systems react to events (e.g. OrderCreated) |
| **DLQ** | Dead Letter Queue | Holding area for failed messages needing retry/investigation |
| **Idempotency** | *(term, not acronym)* | Processing the same message twice still yields one result |
| **RFC** | Remote Function Call | Classic SAP interface style (often brittle for D2C scale) |
| **REST** | Representational State Transfer | Common modern web API style |
| **Webhook** | Webhook | HTTP callback notifying another system that an event occurred |

### C.5 Data, identity & master data

| Abbreviation | Full name | Plain-English meaning |
|---|---|---|
| **SoR** | System of Record | Authoritative master for a data domain |
| **MDM** | Master Data Management | Discipline/tooling for golden records and data quality |
| **Golden record** | Golden customer/product record | Single trusted master version of an entity |
| **BOM** | Bill of Materials | Structured list of components that make a product |
| **BI** | Business Intelligence | Reporting/analytics for board and operational decisions |
| **DPIA** | Data Protection Impact Assessment | Formal review of privacy risk before processing personal data |
| **POPIA** | Protection of Personal Information Act | South African privacy law governing personal data use |
| **Consent** | Marketing / processing consent | Recorded permission basis required for digital journeys |
| **SSO** | Single Sign-On | One login used across multiple applications |
| **MFA** | Multi-Factor Authentication | Extra verification beyond password (e.g. app code) |

### C.6 ERP, warehouse & operations

| Abbreviation | Full name | Plain-English meaning |
|---|---|---|
| **ERP** | Enterprise Resource Planning | Core finance / supply / manufacturing system |
| **SAP R/3** | SAP R/3 | Apex’s 2011 on-prem ERP (no modern API layer) |
| **S/4HANA** | SAP S/4HANA | Modern SAP ERP — Phase 3 candidate |
| **D365** | Microsoft Dynamics 365 | Microsoft cloud ERP/CRM suite — Phase 3 candidate |
| **NetSuite / NS** | Oracle NetSuite | Cloud ERP — Phase 3 candidate |
| **WMS** | Warehouse Management System | Pick / pack / ship and inventory operations software |
| **MES** | Manufacturing Execution System | Shop-floor production system (Apex’s vendor is defunct) |
| **COGS** | Cost of Goods Sold | Direct cost of products sold |
| **AR** | Accounts Receivable | Money owed by customers |
| **PO** | Purchase Order | Formal buy order (common in B2B matching today) |
| **RT / near-RT** | Real-Time / Near Real-Time | Seconds-to-minutes latency vs nightly batch |
| **ASN** | Advance Shipping Notice | Electronic notice that a shipment is coming / has shipped |

### C.7 Security, infrastructure & operability

| Abbreviation | Full name | Plain-English meaning |
|---|---|---|
| **CDN** | Content Delivery Network | Edge cache for fast content delivery |
| **WAF** | Web Application Firewall | Edge filter against common web attacks |
| **DDoS** | Distributed Denial of Service | Attack that floods a site to take it offline |
| **SIEM** | Security Information and Event Management | Central security log and alert platform |
| **APM** | Application Performance Monitoring | Runtime performance and error observability |
| **VPN** | Virtual Private Network | Encrypted path (e.g. cloud ↔ on-prem SAP) |
| **ISP** | Internet Service Provider | Network connectivity provider |
| **DC** | Data Centre | Physical facility hosting servers (Apex’s current single-ISP DC) |
| **SaaS** | Software as a Service | Vendor-hosted software (Shopify, Salesforce) |
| **IaaS** | Infrastructure as a Service | Cloud compute / network / storage |
| **SOC** | Security Operations Centre | Team/service monitoring and responding to security events |
| **NOC** | Network Operations Centre | Team/service monitoring infrastructure and uptime |
| **SLA** | Service Level Agreement | Agreed performance or support commitment |
| **SLO** | Service Level Objective | Internal target for reliability/latency used for operations |
| **RPO** | Recovery Point Objective | How much data loss is acceptable in a disaster |
| **RTO** | Recovery Time Objective | How quickly a service must be restored |
| **DR** | Disaster Recovery | Capability to recover from major outages |
| **DevOps** | Development + Operations | Practices/tooling for build, deploy, and run automation |

### C.8 Architecture method & programme governance

| Abbreviation | Full name | Plain-English meaning |
|---|---|---|
| **TOGAF** | The Open Group Architecture Framework | Enterprise architecture method (current → target → roadmap) |
| **ADM** | Architecture Development Method | TOGAF’s step-by-step architecture process |
| **ADR** | Architecture Decision Record | Written decision + context + consequences (see Section 14) |
| **MVP** | Minimum Viable Product | Smallest launchable scope that proves value |
| **PoC** | Proof of Concept | Short spike to prove a technical approach |
| **SI** | System Integrator | External delivery partner (Shopify / Salesforce / MuleSoft) |
| **FTE** | Full-Time Equivalent | Headcount measure for staffing |
| **P1 / P2 / P3** | Phase 1 / 2 / 3 | Delivery phases in the roadmap |
| **RAG** | Red / Amber / Green | Status traffic-light rating (e.g. CTO RAG on debt) |
| **Named debt** | Named technical debt | Accepted shortcut with owner, retirement phase, and exit gate |
| **Exit criteria** | Phase exit criteria | Conditions that must be true before advancing or closing debt |
| **CEO / CFO / CTO** | Chief Executive / Financial / Technology Officer | Executive roles referenced in decisions and risks |
| **IT** | Information Technology | Apex internal technology organisation |

### C.9 Separation-of-concerns terms (used as labels, not acronyms)

| Term | Full name | Plain-English meaning |
|---|---|---|
| **System of Sale** | System of Sale | Where browse, cart, and checkout happen (**Shopify**) |
| **System of Engagement** | System of Engagement | Where relationships, service, and journeys live (**Salesforce**) |
| **System of Process** | System of Process | Where orchestration and contracts live (**MuleSoft**) |
| **System of Record** | System of Record | Authoritative master for a domain (**ERP / MDM / WMS / PIM**) |
| **Hub-and-spoke** | Hub-and-spoke integration | All systems connect via a central integration hub, not P2P webs |
| **Thin vertical slice** | Thin vertical slice | End-to-end path shipped early (storefront → pay → fulfil → post) with limited breadth |

---


