# PROJECT_SPEC.md
**Project:** Universal Analytics Platform
**Documentation Version:** 2.0
**Status:** Active Development

## 1. Project Vision
Build a browser-based analytics platform that accepts one or more structured datasets and automatically generates interactive dashboards, KPIs, business insights, documentation and a Power BI implementation guide.

The bundled Hospital Analytics project is the default demonstration project.

## 2. Objectives
- Upload CSV/XLSX datasets
- Detect dataset relationships
- Generate dashboards dynamically
- Generate KPIs and insights
- Generate downloadable documentation
- Help recreate the solution in Power BI

## 3. Target Users
- Data Analysts
- Business Analysts
- Students
- Recruiters
- Business Stakeholders

## 4. Functional Requirements

### Workspace
- Upload datasets
- Validate files
- Load datasets
- Remove datasets
- Clear Project
- Load Demo Project

### Dashboard
- Dynamic dashboard generation
- Executive Overview (when applicable)
- Interactive charts
- Dynamic filters
- Reset filters

### Documentation
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

## 5. Dashboard Rules
- One dashboard per independent dataset.
- Combine dashboards only when relationships exist.
- Dashboard names and count are generated automatically.

## 6. Dynamic Content
Never hardcode:
- Dashboard names
- Dashboard count
- KPIs
- Charts
- Filters
- Insights
- Documentation
- DAX Measures

## 7. Application States
DEMO
USER_PROJECT
EMPTY

Behaviour follows ARCHITECTURE.md.

## 8. User Workflow
Launch → Explore Demo or Upload → Load → Analyze → Dashboard → Download Report → Clear Project / Load Demo

## 9. Non-Functional Requirements
- Responsive
- Fast
- Modular
- Maintainable
- Graceful error handling
- Minimal dependencies

## 10. Success Criteria
The platform should:
- Accept user datasets
- Generate dashboards automatically
- Produce documentation
- Export Markdown reports
- Avoid hardcoded industry logic

## 11. Out of Scope
- Authentication
- Cloud storage
- Multi-user collaboration
- Database backend
- Streaming data
- Machine learning predictions

## 12. Development Principles
- Documentation-first
- One feature at a time
- Preserve working functionality
- Test every feature
- Keep documentation synchronized
