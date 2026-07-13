# PROJECT_SPEC

## Project Vision

Build a production-quality HTML Hospital Analytics Platform that allows
users to upload one or more datasets, analyze them, generate executive
dashboards, and create professional documentation.

## Objectives

-   Interactive web application, not a static page.
-   Support multiple CSV/Excel files.
-   Detect relationships where possible.
-   Generate dashboards only from available data.
-   Produce Power BI recreation guidance.

## Main Modules

### Data Workspace

-   Drag & drop upload
-   File picker
-   Multiple datasets
-   Dataset cards
-   Preview
-   Data quality summary
-   Relationship summary

### Dashboard

-   Executive Overview
-   Combined Dashboard (only if related datasets exist)
-   One dashboard per dataset

### Documentation

-   Executive Summary
-   KPIs
-   Insights
-   Recommendations
-   Data Model
-   DAX Measures
-   Power BI Guide
-   Data Dictionary
-   Download detailed Markdown report

## Project Principles

1.  Business value before visual appeal.
2.  Keep code as simple as possible.
3.  Never solve a problem with 20 lines if 3 will do.
4.  Do not redesign the UI unless requested.
5.  Build incrementally.
6.  Preserve working functionality.

## Coding Standards

-   Modular JavaScript
-   Readable code
-   Minimal dependencies
-   One responsibility per function

## Current Status

Completed: - UI Prototype

In Progress: - Navigation - Dataset Upload

Pending: - CSV Parsing - Relationship Detection - Dashboard Generation -
Documentation
