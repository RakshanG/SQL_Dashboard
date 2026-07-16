# SQL Dashboards — Airline Delay Analytics

A business-facing analytics layer built on top of real airline on-time performance data, using Databricks SQL to surface genuine, actionable insights for non-technical stakeholders.

## Overview

While earlier projects focused on building and cleaning data (Bronze/Silver/Gold, streaming ingestion), this project focuses on the other half of the data engineering lifecycle: making that data usable and visual for business stakeholders through SQL-based dashboards.

## SQL Dashboard

A SQL aggregation query was built against the `flights` table (real BTS on-time performance data) to surface a genuine business question: **which airlines have the worst average arrival delays, and how much flight volume backs that number?**

```sql
SELECT 
  Reporting_Airline,
  ROUND(AVG(CAST(ArrDelayMinutes AS DOUBLE)), 1) AS avg_delay_minutes,
  COUNT(*) AS total_flights
FROM workspace.default.flights
GROUP BY Reporting_Airline
ORDER BY avg_delay_minutes DESC
```

**Sample finding:** across 14 reporting airlines, average delays ranged from roughly 15 to 26.5 minutes. Notably, Delta (`DL`) combined one of the largest flight volumes (232,126 flights) with one of the better average delay figures (15.0 minutes) — a genuinely useful, non-obvious insight for a stakeholder dashboard, since higher volume doesn't necessarily correlate with worse performance.

This query was turned into a bar chart visualization directly in Databricks SQL, forming the basis of a shareable dashboard.


## Key Engineering Decisions

- **Resolving Unity Catalog table location ambiguity**: initial queries failed because the `flights` table was saved without an explicit catalog/schema qualifier during ingestion, landing it in `workspace.default` rather than the project-specific catalog set up for this pipeline. This reinforced an important Unity Catalog distinction: uploading files to a Volume and saving a table are independent operations — a table's actual location depends entirely on how it was explicitly saved, not on related files' storage location.

## Tech Stack
`Databricks SQL` · `Delta Lake` · `SQL`

## Future Work
- Explore AI/BI Genie Agents for natural-language, no-code querying of this dataset (not available on the Databricks Free Edition tier used for this project)
- Publish the dashboard with scheduled refreshes for stakeholder self-service
- Expand the dashboard to include route-level and seasonal (month-over-month) delay trends
