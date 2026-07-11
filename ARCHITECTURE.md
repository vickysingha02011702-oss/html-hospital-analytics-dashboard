# ARCHITECTURE.md

# Hospital Analytics Platform Architecture

## Purpose

This document describes how the project is organized so that every
contributor (human or AI) follows the same structure.

------------------------------------------------------------------------

# High-Level Architecture

User │ ▼ Data Workspace │ ├── Upload Manager ├── Dataset Manager ├── CSV
/ Excel Parser ├── Relationship Detector └── Data Quality Engine │ ▼
Analytics Engine │ ├── KPI Engine ├── Statistics Engine ├── Insight
Generator └── Chart Data Generator │ ▼ Dashboard Module │ ▼
Documentation Module

------------------------------------------------------------------------

# Recommended Folder Structure

src/ ├── css/ ├── js/ │ ├── app.js │ ├── navigation.js │ ├── upload.js │
├── parser.js │ ├── relationships.js │ ├── analytics.js │ ├──
dashboard.js │ ├── documentation.js │ └── utils.js ├── assets/ ├──
reports/ └── datasets/

------------------------------------------------------------------------

# Module Responsibilities

navigation.js - Sidebar navigation - Tab switching

upload.js - Drag & drop - File picker - File validation

parser.js - CSV / Excel parsing - Dataset metadata

relationships.js - Detect relationships - Build relationship summary

analytics.js - KPIs - Statistics - Business insights

dashboard.js - Render charts - Render KPI cards

documentation.js - Generate documentation - Export Markdown report

utils.js - Shared helper functions only

------------------------------------------------------------------------

# Development Rules

-   One feature at a time.
-   One responsibility per module.
-   Never duplicate logic.
-   Do not redesign the UI unless requested.
-   Keep implementations simple and maintainable.
