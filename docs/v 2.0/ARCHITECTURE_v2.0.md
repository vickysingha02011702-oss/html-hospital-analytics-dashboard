# ARCHITECTURE.md
**Version:** 2.0
**Status:** Architecture Frozen (Initial)

# 1. Architecture Overview
## Purpose
This project is a Universal Analytics Platform capable of generating dashboards, documentation, KPIs, insights and Power BI guidance from uploaded datasets. The bundled Hospital Analytics project is only the default demonstration project.

# 2. Project Philosophy
- Data-driven, not industry-driven.
- Modular architecture.
- Minimal, maintainable code.
- Dynamic content generation.
- Documentation-first development.

# 3. Core Design Principles
- Separation of concerns.
- Single responsibility.
- Reusable components.
- State-driven UI.
- Fail gracefully.

# 4. Application States
## DEMO
- Demo hospital project loaded.
- Dashboards, reports and documentation available.

## USER_PROJECT
- User datasets become the active project.
- Dashboards, KPIs, filters, insights and documentation are generated dynamically.

## EMPTY
- No datasets loaded.
- Workspace active.
- Dashboard and Documentation remain visible but inactive.

State transitions:
Launch -> DEMO
Load Data -> USER_PROJECT
Clear Project -> EMPTY
Load Demo -> DEMO

# 5. Project Lifecycle
Upload -> Validate -> Load -> Profile -> Detect Relationships -> Generate Analytics -> Generate Dashboards -> Generate Documentation -> Export Report

# 6. Module Responsibilities
navigation.js: navigation and tab switching
upload.js: upload queue, validation, reset
parser.js: CSV/XLSX parsing and profiling
relationships.js: relationship detection
analytics.js: KPI and insight generation
dashboard.js: dashboard, charts and filters
documentation.js: markdown documentation export
utils.js: shared helpers

# 7. Processing Pipeline
Upload
↓
Validate
↓
Load
↓
Profile
↓
Relationship Detection
↓
Analytics
↓
Dashboards
↓
Documentation
↓
Export

# 8. Dynamic Content Generation
Never hardcode:
- Dashboard names
- Dashboard count
- KPIs
- Charts
- Filters
- Documentation
- Insights
- DAX measures

Everything is generated from uploaded datasets.

# 9. Dashboard Strategy
Generate one dashboard per dataset unless relationships justify combined dashboards.
Executive Overview exists only when multiple related datasets are present.

# 10. Filter Strategy
Generate filters from dataset metadata.
Every dashboard supports:
- Show/Hide Filters
- Apply Filters
- Reset Filters

# 11. Documentation Engine
Generate:
- Executive Summary
- Dataset Summary
- KPIs
- Insights
- Relationships
- Data Model
- DAX Measures
- Power BI Guide
Export as Markdown.

# 12. Project Reset Behaviour
Reset Filters: filters only.
Clear Project: switch to EMPTY and remove generated content.
Load Demo: restore bundled Hospital Analytics.

# 13. UI State Rules
Do not remove interface components.
React to application state by enabling, disabling, hiding or showing placeholders.

# 14. Error Handling
Gracefully handle:
- Unsupported files
- Empty datasets
- Corrupt files
- Missing columns
- Relationship failures

# 15. Performance
- Parse once.
- Cache metadata.
- Avoid duplicate computation.

# 16. AI Behaviour Rules
Generate insights only from available data.
Never invent KPIs or unsupported conclusions.

# 17. Documentation Governance
Architecture changes -> ARCHITECTURE.md
Requirement changes -> PROJECT_SPEC.md
Implementation changes -> TASKS.md + CHANGELOG.md
Technical decisions -> DECISIONS.md

# 18. Development Rules
- One feature at a time.
- Preserve working functionality.
- No UI redesign unless requested.
- Keep code simple.
- Modify minimum files.

# 19. Future Roadmap
- Project save/load
- Authentication
- Themes
- Plugins
- AI insight explanations
- Scheduled reports

# 20. Architecture Decision
Architecture takes priority over implementation speed.
