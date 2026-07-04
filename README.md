# KPI Analytics Dashboard — Research Organization
**Power BI · DAX · Power Query · Star Schema · Local Network Deployment**

> A management analytics system built for a research institute to replace manual reporting with a single, always-current KPI dashboard — deployable on a local network without internet access.📊 [View Dashboard (PDF Preview)](docs/KPI_Dashboard_Preview.pdf)

---

## The Problem

The organization had no unified view of research output. KPI data (administrative tasks, publications, forum participation, analytical notes) lived in separate files across departments. Before every management meeting, someone had to manually collect and reconcile numbers — a process that was slow, error-prone, and produced figures that different stakeholders would dispute.

**The ask:** one place to see plan vs. actual for every researcher, team, department, and research direction — updated on a regular cycle, accessible to management without technical skills.

---

## What Was Built

A three-page Power BI dashboard covering three levels of the organization:

| Page | Audience | Content |
|---|---|---|
| Institute Overview | CEO | Aggregate KPI performance, institute-level trends |
| Departments & Teams | Deputy Directors | Breakdown by department → team → research direction |
| Researchers | Team Leads | Individual rankings, top/bottom performers, plan vs. actual |

**Data model:** Star schema with a unified fact table (`Documents`) consolidating four activity types — administrative tasks, analytical notes, forum participation, publications — via a `DocumentType` discriminator field. Dimension tables: `Departments`, `Teams`, `Researchers`, `Directions`, `Calendar`.

**Data entry:** Excel-based input templates per research direction, consolidated into the master table by a designated data owner using Power Query. No internet required; runs entirely on a local network share.

---

## Technical Implementation

**Data modeling**
- Star schema design with one central fact table replacing four separate source tables
- Relationships: `Departments → Teams → Researchers` (org hierarchy), `Calendar` (time intelligence), `Directions` (research dimension)
- Access control logic designed for tiered visibility: CEO / Deputy Directors / Executors

**DAX measures**
- Plan vs. actual completion rates per researcher, team, department, direction
- `RANKX` for dynamic performer rankings within any filtered context
- `VAR / RETURN` pattern for readable complex measures
- Time intelligence: period-over-period comparisons, YTD aggregations

**Power Query**
- Data consolidation from per-direction Excel input files using folder-based refresh
- Data validation and error handling at the input layer
- Incremental backup workflow for data recovery

**Visualization**
- Parallel Top N / Bottom N blocks with a dynamic `TopN_Parameter` slicer
- Consistent color-coded KPI indicators across all pages
- Optimized for read-only consumption by non-technical management users

---

## The Data Entry Architecture

A deliberate design decision: input data lives outside the `.pbix` file in structured Excel templates, not inside the model.

```
/network-share/
  /inputs/
    Direction_1_Input.xlsx    ← filled by executors
    Direction_2_Input.xlsx    ← filled by executors
    ...
  /master/
    Documents.xlsx            ← consolidated by data owner
    /archive/
      Documents_2026-06-01.xlsx
      Documents_2026-06-15.xlsx
  KPI_Dashboard.pbix          ← read-only for management
```

This means: input data can be updated without touching the model; the data owner controls what gets into the master table; backups are simple file copies with a date suffix.

---

## What This Enabled

- **Single source of truth:** no more competing spreadsheets before board meetings
- **Faster decisions:** management can filter to any department, team, or direction in seconds without waiting for a report to be compiled
- **Accountability transparency:** rankings are calculated by the same formula for everyone — no negotiation about methodology
- **Scalable to 20–100 people** without infrastructure investment (no cloud, no SQL Server required)

---

## Honest Notes on AI-Assisted Development

This project was built with active use of AI tools (Claude, GitHub Copilot) — and I think it's worth being explicit about what that means in practice:

**AI contributed to:**
- Iterating on DAX measure syntax and debugging calculation logic
- Reviewing data model design choices and suggesting alternatives
- Drafting supporting documents (CEO brief, update regulation, Excel template structure)

**I contributed to:**
- Defining the problem and translating stakeholder needs into data requirements
- All architecture decisions: which tables to keep, how to model the org hierarchy, how to structure the input workflow
- Choosing what to measure and why — the KPI logic itself
- Testing, validating, and iterating on the output against real requirements
- Making the judgment calls when AI suggestions didn't fit the actual context

Using AI on this project is analogous to using Stack Overflow for syntax questions or a colleague for a design review — the thinking, the decisions, and the outcome are mine. I believe being explicit about this is more useful to a potential employer than pretending the work was done in isolation.

---

## Files in This Repository

| File | Description |
|---|---|
| `KPI_Dashboard.pbix` | Power BI report file |
| `Documents_Input_Template.xlsx` | Excel data entry template for executors |
| `docs/CEO_Brief.pdf` | One-page description for executive stakeholders |
| `docs/Update_Regulation.pdf` | Data update cycle and roles |
| `README.md` | This file |

---

## Tools & Skills

`Power BI Desktop` · `DAX` · `Power Query (M)` · `Excel` · `Star Schema Design` · `Stakeholder Requirements` · `Local Network Deployment` · `Data Entry Workflow Design`

---

*Built as Portfolio Project #1 | Data Analytics Career Transition | 2026*
