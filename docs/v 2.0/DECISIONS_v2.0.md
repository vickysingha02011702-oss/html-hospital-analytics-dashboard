# DECISIONS.md
**Project:** Universal Analytics Platform
**Documentation Version:** 2.0
**Purpose:** Record important architectural and technical decisions, along with the reasoning behind them.

---

# Decision 001 — Universal Analytics Platform

Decision:
Build a Universal Analytics Platform instead of a Hospital-only dashboard.

Reason:
The same analytics engine should work for healthcare, sales, HR, finance, manufacturing, and other structured datasets. The Hospital Analytics project serves as the bundled demonstration project.

---

# Decision 002 — Documentation-First Development

Decision:
Project documentation is created and maintained before major implementation.

Reason:
Clear documentation reduces ambiguity, keeps AI coding agents aligned, and minimizes rework.

---

# Decision 003 — State-Driven Application

Decision:
The application operates in one of three states:
- DEMO
- USER_PROJECT
- EMPTY

Reason:
State management is simpler, safer, and more maintainable than dynamically rebuilding the interface.

---

# Decision 004 — Dynamic Content Generation

Decision:
Dashboards, KPIs, filters, documentation, insights, DAX measures, and reports are generated from uploaded datasets.

Reason:
The platform must adapt to different industries without hardcoded business logic.

---

# Decision 005 — Dynamic Dashboard Strategy

Decision:
Generate one dashboard per independent dataset. Generate combined dashboards only when meaningful relationships exist.

Reason:
This balances usability with flexibility.

---

# Decision 006 — Metadata-Driven Filters

Decision:
Generate dashboard filters from dataset metadata rather than predefined lists.

Reason:
Every dataset has different dimensions. Filters should adapt automatically.

---

# Decision 007 — Clear Separation of Responsibilities

Decision:
Each JavaScript module has a single responsibility.

Examples:
- navigation.js → Navigation
- upload.js → Upload management
- parser.js → Parsing
- analytics.js → KPI generation
- dashboard.js → Dashboard rendering
- documentation.js → Documentation generation

Reason:
Improves readability, testing, and maintenance.

---

# Decision 008 — Minimal Code Philosophy

Decision:
Prefer the simplest implementation that satisfies the requirement.

Reason:
Readable and maintainable code is preferred over clever or overly complex code.

---

# Decision 009 — Documentation Ownership

Decision:
Documentation responsibilities are divided.

Architecture:
Maintained by Project Architect.

Project Specification:
Maintained by Product Owner / Architect.

Decisions:
Maintained by Architect.

Tasks:
Updated during implementation.

Changelog:
Updated after completed work.

Reason:
Prevents documentation drift and accidental architectural changes.

---

# Decision 010 — Documentation Synchronization

Decision:
Whenever a significant architectural or requirement change is approved, the corresponding documentation must be updated before implementation continues.

Reason:
The documentation must remain the single source of truth.

---

# Decision 011 — Preserve Working Functionality

Decision:
Never redesign or rewrite working components unless explicitly required.

Reason:
Reduces regressions and keeps development incremental.

---

# Decision 012 — AI-Assisted Development Workflow

Decision:
Use AI tools in specialized roles.

ChatGPT:
- Architecture
- Planning
- Technical review
- Prompt engineering

Claude:
- UI/UX prototyping
- Visual design

Codex:
- Implementation
- Debugging
- Refactoring

Reason:
Specialized responsibilities produce higher-quality results than relying on a single AI for everything.

---

# Decision 013 — Future Architecture Changes

Decision:
Architecture changes are expected to be rare.

Reason:
After Documentation Version 2.0, implementation should follow the architecture. Future changes should occur only when genuine limitations or better designs are discovered.

---

# Decision Log Rules

When adding a new decision:

1. Assign the next sequential decision number.
2. Record the decision.
3. Explain why it was made.
4. Reference affected documentation if applicable.
5. Do not record routine implementation details.
