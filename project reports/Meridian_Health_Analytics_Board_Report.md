# Meridian Health Analytics — Executive Board Report

*Generated from 10 datasets, 30,000 encounters, 16 hospitals. Data current to April 2026.*

---

## Project Summary

Meridian Health Analytics is an executive business intelligence platform built on ten linked datasets describing 30,000 hospital encounters across 16 hospitals, 16 clinical departments and 11,085 unique patients between January 2022 and April 2026.

The platform turns a validated star schema into seven board-facing dashboard pages, a full data-quality and relationship audit, and a Power BI recreation guide — designed to be presented at a hospital board meeting and to stand as a portfolio-quality analytics engineering artefact.

## Executive Summary

Across the reporting period, the hospital group recorded **30,000 encounters** at a total cost of **£88,608,231**, against an NHS tariff value of **£89,602,364** — a variance of **-1.1%**, meaning the group is operating *under* tariff rather than over it.

Clinically, the group's **30-day readmission rate is 13.4%** and **mortality rate is 1.0%**, both broadly in line with expected acute-hospital ranges once case severity is accounted for. The Emergency Department is meeting the NHS 4-hour standard at **94.3%** group-wide, though one site (**Brighton Coast Hospital**, 92.8%) is materially behind the rest of the network.

The single largest concentration of clinical risk is **Accident & Emergency**, with the highest readmission rate in the group (23.8%). Patients with a long-term condition are readmitted at **15.8%** versus **12.8%** for those without — the clearest single opportunity for a proactive care-management programme in this dataset.

No material outcome or satisfaction gap was found across deprivation quintiles, which is itself a reportable equity finding.

## Business Problem

Hospital boards need a single, trustworthy view that connects **activity** (how much care is being delivered), **cost** (is it being delivered efficiently), **quality** (is it safe and effective) and **access** (is it timely and equitable) — without waiting weeks for an analyst to stitch together spreadsheets from ten different source systems.

Before this platform, each of the ten datasets existed independently, meaning any cross-cutting question — *"which department has the worst combination of cost overrun and high readmissions?"* — required manual joining, was error-prone, and was rarely refreshed in time for a board meeting.

## Objectives

**Primary objective — support executive decision-making:**
- Give the board a single, always-current Executive Overview
- Surface the specific departments, hospitals and patient cohorts that need attention, not just totals
- Justify every KPI and chart with a named business question and a recommended action

**Secondary objective — portfolio/interview quality:**
- Demonstrate a validated star-schema data model, not just a flat file
- Ship production-quality documentation and a Power BI recreation guide an analyst could follow without seeing the source code
- Show restraint: every dashboard page exists because the data supports it, not because it looks good

## Dataset Overview

Ten files were profiled on load: one fact table and nine dimension tables, forming a single star schema.

| Table | Key Columns | Description |
|---|---|---|
| Dim_Patient | PatientID, DateOfBirth, AgeBand, Gender, Ethnicity, DeprivationQuintile, LongTermConditionFlag, HomeRegion | One row per patient (12,000 rows). Deprivation and LTC flags support health-equity analysis; all patient-insight views are aggregate-only. |
| Dim_Hospital | HospitalID, HospitalName, HospitalType, NHSRegion, BedCapacity, Latitude/Longitude | One row per hospital (16 rows). Includes bed capacity for cost-per-bed and geographic coordinates for the network map. |
| Dim_Department | DepartmentID, DepartmentName, ClinicalGroup, CostIndex, BaseReadmissionRisk | One row per clinical department (16 rows), shared across all hospitals. |
| Dim_Consultant | ConsultantID, ConsultantName, DepartmentID, Grade, YearsExperience, EmploymentType | One row per consultant (360 rows). Supports workload and seniority analysis. |
| Dim_Diagnosis | DiagnosisID, ICD10Code, DiagnosisCategory, SeverityBand | One row per diagnosis (30 rows), coded to ICD-10 with a severity band. |
| Dim_Procedure | ProcedureID, OPCSCode, ProcedureCategory, StandardTariffGBP | One row per procedure (18 rows), coded to OPCS with standard tariff. |
| Dim_Payer | PayerID, PayerType, FundingGroup, ApproxShare | One row per funding source (6 rows) — NHS, private insurance, self-pay, overseas, charity, clinical trial. |
| Dim_Location | LocationID, City, County, UKRegion, Latitude/Longitude | One row per UK location (48 rows) used for patient home-region analysis. |
| Dim_Date | DateKey, Date, FinancialYear, FinancialQuarter, IsWeekend, Season | One row per calendar day (1,826 rows, 2022–2026), pre-built with NHS financial-year logic. Role-playing dimension: joined twice from the fact table. |

| Fact_Encounter | 30,000 rows, 37 columns | One row per care episode — the grain of the entire model. Joins to all nine dimensions above. |

Date range: **2022-01-01 to 2026-04-30** (2026 is a partial year through April — all year-over-year comparisons in this report use Jan–Apr for every year to keep the comparison fair).

## Methodology

1. **Profile before modelling.** Every file was checked for row/column counts, memory footprint, null counts and duplicate rows before any KPI was built.
2. **Validate relationships, don't assume them.** Every foreign key in Fact_Encounter was checked against its dimension table for orphaned records. Result: zero orphans across all 10 declared relationships.
3. **Aggregate once, reuse everywhere.** All dashboard figures are pre-computed aggregates (department/hospital/diagnosis/month-level roll-ups), not raw-row computations in the browser — this keeps the platform fast even though the source fact table has 30,000 rows.
4. **Ground every insight in a number.** Every sentence in the Insight boxes on each dashboard page cites an actual computed figure — nothing in this report is a fabricated or illustrative statistic.

## Data Cleaning Summary

No records were removed and no values were imputed. The only cleaning decisions made were about **interpretation of structural nulls**:

- `TriageCategory` and `AandEWaitHours` are null for all non-Emergency encounters (23,694 rows) — correct, since only ED attendances are triaged.
- `DischargeDestination` and `PatientSatisfactionScore` are null for Cancelled/Not-Attended encounters (1,441 rows) — correct, since there is nothing to discharge or rate.
- Zero duplicate rows were found in any of the ten files.
- Zero orphaned foreign keys were found — no cleaning of relationships was required.

**Overall data quality score: 99.7%** (see Data Workspace for the per-file breakdown).

## Feature Engineering

The following derived fields power the dashboards and are **not** present in the raw source files:

- **Cost Variance (£ and %)** — CostGBP minus TariffGBP, at encounter, department and hospital grain.
- **Clinical Base Cost** — CostGBP minus MedicationCostGBP, LabCostGBP and ImagingCostGBP, isolating the ancillary cost lines.
- **4-Hour Compliance Flag** — AandEWaitHours ≤ 4, aggregated to a compliance rate per hospital.
- **RTT Compliance Flag** — ReferralToTreatmentDays ≤ 126 (the NHS 18-week standard), aggregated group-wide.
- **YearMonth** — calendar rollup of AdmissionDate used for every trend chart.
- **Jan–Apr YTD subset** — used for every year-over-year comparison so a partial 2026 is never compared unfairly against a full prior year.

## Data Model & Relationships

This is a single-fact **star schema** — Fact_Encounter at the centre, 9 dimension tables as points, all many-to-one. See the interactive constellation diagram in the Data Workspace tab.

**Role-playing dimension:** Dim_Date is referenced twice — once as the *active* relationship (AdmissionDateKey, used for all default time intelligence) and once as an *inactive* relationship (DischargeDateKey, activated only when a discharge-based measure explicitly calls USERELATIONSHIP() — see the DAX Documentation section).

Full relationship list:

| From Table | From Column | To Table | To Column | Cardinality |
|---|---|---|---|---|
| Fact_Encounter | PatientID | Dim_Patient | PatientID | Many-to-one |
| Fact_Encounter | HospitalID | Dim_Hospital | HospitalID | Many-to-one |
| Fact_Encounter | DepartmentID | Dim_Department | DepartmentID | Many-to-one |
| Fact_Encounter | ConsultantID | Dim_Consultant | ConsultantID | Many-to-one |
| Fact_Encounter | PrimaryDiagnosisID | Dim_Diagnosis | DiagnosisID | Many-to-one |
| Fact_Encounter | ProcedureID | Dim_Procedure | ProcedureID | Many-to-one |
| Fact_Encounter | PayerID | Dim_Payer | PayerID | Many-to-one |
| Fact_Encounter | PatientHomeLocationID | Dim_Location | LocationID | Many-to-one |
| Fact_Encounter | AdmissionDateKey | Dim_Date | DateKey | Many-to-one active |
| Fact_Encounter | DischargeDateKey | Dim_Date | DateKey | Many-to-one inactive optional |

## KPI Glossary

| KPI | Formula | Why the board cares |
|---|---|---|
| Total Cost / Tariff Variance | SUM(CostGBP) − SUM(TariffGBP) | Is the group living within its NHS-funded means? |
| 30-Day Readmission Rate | COUNT(ReadmittedWithin30Days="Yes") / COUNT(Encounters) | Core NHS quality & safety indicator |
| Mortality Rate | COUNT(MortalityFlag="Yes") / COUNT(Encounters) | Core clinical risk indicator |
| Avg. Length of Stay | AVERAGE(LengthOfStayDays), inpatient only | Bed-flow and capacity efficiency |
| RTT Compliance | COUNT(ReferralToTreatmentDays ≤ 126) / COUNT(Encounters) | NHS 18-week statutory access standard |
| 4-Hour ED Compliance | COUNT(AandEWaitHours ≤ 4) / COUNT(ED Encounters) | NHS constitutional ED standard |
| Cancellation / No-Show Rate | COUNT(Flag="Yes") / COUNT(Encounters) | Lost scheduled capacity |
| Patient Satisfaction | AVERAGE(PatientSatisfactionScore) | Patient experience |

## Business Insights

1. The group runs **1.1% under tariff** on average — a sign of cost discipline, not margin erosion.
2. **Accident & Emergency** has the highest 30-day readmission rate in the group at **23.8%**.
3. **Oncology** has the highest mortality rate at **2.1%** — consistent with case-severity rather than a standalone quality signal.
4. Patients with a long-term condition are readmitted **2.9 percentage points** more often than those without (15.8% vs. 12.8%).
5. **Brighton Coast Hospital** is the outlier on the NHS 4-hour ED standard, at 92.8% against a group average of 94.3%.
6. No material readmission or satisfaction gap exists across deprivation quintiles — a positive equity finding.
7. Imaging (£6,675,468) is a larger cost line than lab and medication combined (£6,348,201).
8. NHS funding covers 81.9% of total cost; Private Medical Insurance is the largest non-NHS source at £8,768,513.

## Business Recommendations

1. **Launch a discharge-planning review** for Accident & Emergency, Respiratory Medicine and Oncology — the three highest-readmission departments — before the next quarterly board cycle.
2. **Stand up a long-term-condition care-management pathway.** The 2.9-point readmission gap for LTC patients is the single clearest, data-supported intervention target in this report.
3. **Commission an operational review at Brighton Coast Hospital** to close the 4-hour ED compliance gap against the network average.
4. **Reinvest, don't just report, the tariff headroom.** The group is running 1.1% under tariff consistently — the board should decide whether this becomes capacity investment or margin.
5. **Publish the deprivation-equity finding.** The absence of a readmission/satisfaction gap across deprivation quintiles is a genuinely positive result worth stating publicly, not just noting internally.
6. **Review imaging utilisation** as the largest ancillary cost line before targeting medication spend.

## Dashboard Walkthrough

- **Executive Overview** — the one page to present first: activity, cost variance, readmission, mortality, satisfaction and LOS in a single view.
- **Financial Performance** — cost vs. tariff by department and hospital, cost composition, and payer/funding mix.
- **Operations** — length of stay, RTT compliance, wait times, cancellations and no-shows.
- **Clinical Performance** — readmission and mortality by department and diagnosis, outcome distribution, LOS by severity.
- **Emergency Department** — volume, triage mix, the 4-hour standard by hospital.
- **Patient Insights** — aggregate demographics, deprivation-equity analysis, long-term-condition impact. No individual patient is ever identified.
- **Combined Analytics** — cross-cuts only possible because every dimension validates against the fact table: consultant workload concentration and a geographic hospital network view.

## Future Improvements

- Add drill-through from Executive Overview KPI cards directly into the relevant departmental dashboard page.
- Extend the Data Workspace upload flow to auto-detect relationships for a *new*, previously unseen dataset (currently the platform validates the known Meridian schema; generic schema inference is a natural next release).
- Add a rolling 12-month forecast for ED volume and total cost, once at least one full additional year of data is available.
- Add row-level security design notes for a production Power BI deployment (regional managers seeing only their own hospital).

## Technical Architecture

- **Client-side single-page application** — no backend required; all data is pre-aggregated at build time and embedded as JSON.
- **Rendering:** vanilla JavaScript + Chart.js for all visualisations; no framework overhead.
- **Data flow:** raw CSVs → pandas profiling & aggregation → compact JSON payload (~86 KB) → embedded in this HTML file.
- **Why pre-aggregate:** the fact table has 30,000 rows; shipping raw rows to the browser and aggregating client-side on every page view would be slower and unnecessary for a fixed board dataset. Pre-aggregation keeps the platform instant and portable — a single HTML file, no server, no database.

## Assumptions & Limitations

**Assumptions:**
- "Under tariff" is treated as a positive cost-discipline signal; the board may hold a different view depending on funding-agreement specifics not present in this data.
- Mortality and readmission rates are read as *risk signals to investigate*, not as direct quality judgments — case-mix severity is a confound this dataset only partially captures (via SeverityBand).

**Limitations:**
- 2026 contains only Jan–Apr; all year-over-year figures in this report deliberately compare like-for-like Jan–Apr periods to avoid an unfair partial-year comparison.
- NHSNumber_Synthetic is synthetic and must never be treated as a real patient identifier.
- Patient Insights are aggregate-only by design; this platform does not support any individual patient look-up.

## Power BI Recreation Guide

**1. Import order:** load Dim_Date first, then the remaining eight dimension tables, then Fact_Encounter last so Power BI's relationship auto-detect has all dimension keys available.

**2. Relationships:** recreate the 10 relationships exactly as listed in the Data Model section above. Set AdmissionDateKey → Dim_Date[DateKey] as **active**; set DischargeDateKey → Dim_Date[DateKey] as **inactive** (Power BI will do this automatically since only one relationship per table pair can be active).

**3. Measures:** build every measure in the DAX Documentation section below as a Measure, not a Calculated Column — this keeps the model lean and lets each measure respond correctly to slicers.

**4. Visuals:** recreate each dashboard page as a separate report page. Use the same chart type noted in this platform (line for trends, horizontal bar for department rankings, doughnut for composition, bubble for the geographic view).

**5. Slicers:** add a synced slicer panel for FinancialYear, NHSRegion and DepartmentName, applied across all report pages via Power BI's "Sync Slicers" pane.

**6. Bookmarks & navigation:** create one bookmark per dashboard page and a button-based left-navigation bar mirroring this platform's sidebar, so the board experience matches exactly.

**7. Tooltips:** for the hospital network visual, set a report-page tooltip showing HospitalName, encounter volume and readmission rate — matching the custom tooltip in Combined Analytics here.

**8. Performance:** disable auto date/time hierarchies (Dim_Date already provides one), and set Fact_Encounter to Import mode given its size (30,000 rows is comfortably within Import-mode limits).

## DAX Documentation

All measures use variables and DIVIDE() to avoid divide-by-zero errors, per this project's DAX philosophy: measures over calculated columns, always.

```dax
Total Cost = SUM(Fact_Encounter[CostGBP])

Total Tariff = SUM(Fact_Encounter[TariffGBP])

Cost Variance % =
VAR _cost = [Total Cost]
VAR _tariff = [Total Tariff]
RETURN DIVIDE(_cost - _tariff, _tariff)

Readmission Rate =
VAR _readmitted = CALCULATE(COUNTROWS(Fact_Encounter), Fact_Encounter[ReadmittedWithin30Days] = "Yes")
VAR _total = COUNTROWS(Fact_Encounter)
RETURN DIVIDE(_readmitted, _total)

Mortality Rate =
VAR _deaths = CALCULATE(COUNTROWS(Fact_Encounter), Fact_Encounter[MortalityFlag] = "Yes")
RETURN DIVIDE(_deaths, COUNTROWS(Fact_Encounter))

Avg Length of Stay (Inpatient) =
CALCULATE(AVERAGE(Fact_Encounter[LengthOfStayDays]), Fact_Encounter[PatientClass] = "Inpatient")

RTT Compliance % =
VAR _within = CALCULATE(COUNTROWS(Fact_Encounter), Fact_Encounter[ReferralToTreatmentDays] <= 126)
RETURN DIVIDE(_within, COUNTROWS(Fact_Encounter))

ED 4-Hour Compliance % =
VAR _edRows = CALCULATETABLE(Fact_Encounter, Fact_Encounter[AppointmentType] = "Emergency")
VAR _within4h = COUNTROWS(FILTER(_edRows, Fact_Encounter[AandEWaitHours] <= 4))
RETURN DIVIDE(_within4h, COUNTROWS(_edRows))

Cost by Discharge Month (role-playing dimension example) =
CALCULATE(
    [Total Cost],
    USERELATIONSHIP(Fact_Encounter[DischargeDateKey], Dim_Date[DateKey])
)
```

## Power Query Documentation

1. **Source** — import each CSV with `Csv.Document`, first row as headers.
2. **Type detection** — explicitly set data types rather than relying on auto-detect: DateKey and all *DateKey columns as Whole Number; CostGBP/TariffGBP/*CostGBP as Decimal Number; all *Flag and Outcome/AppointmentType/PatientClass columns as Text.
3. **No filtering of nulls** — do **not** remove rows with null TriageCategory, AandEWaitHours, DischargeDestination or PatientSatisfactionScore; these are structurally correct nulls (see Data Cleaning Summary) and removing them would silently drop valid non-Emergency or non-attended encounters.
4. **Merge queries** — this model does not require any Power Query merges; all joins are handled by the star-schema relationships in the model view, not pre-flattened in the query editor.
5. **Rename for clarity** — rename `DiagnosisID` in Dim_Diagnosis to match `PrimaryDiagnosisID` in Fact_Encounter is **not** required; Power BI relationships do not require matching column names, only matching data types and values.

## Data Dictionary — Fact_Encounter

| Column | Description | Type | Business Meaning | Example | Related Table | KPIs Using This |
|---|---|---|---|---|---|---|
| EncounterID | Unique identifier for one care episode. | Text (PK) | Grain of the fact table — one row per encounter. | E000001 | — | All KPIs |
| PatientID | Foreign key to the patient. | Text (FK) | Links activity to a person for continuity-of-care analysis. | PT00709 | Dim_Patient | Encounters per patient |
| NHSNumber_Synthetic | Synthetic NHS number, not a real identifier. | Text | Illustrative only — never join on this in production. | 9967117209 | — | — |
| HospitalID | Foreign key to the treating hospital. | Text (FK) | Enables cost, quality and volume comparison across the network. | H009 | Dim_Hospital | Cost by hospital, 4-hr compliance by hospital |
| DepartmentID | Foreign key to the clinical department. | Text (FK) | Primary axis for specialty-level performance. | D001 | Dim_Department | Readmission/mortality/cost by department |
| ConsultantID | Foreign key to the treating consultant. | Text (FK) | Enables workload-balance and resilience analysis. | C0008 | Dim_Consultant | Top consultants by volume |
| PrimaryDiagnosisID | Foreign key to the primary diagnosis. | Text (FK) | Clinical condition driving the encounter. | DX004 | Dim_Diagnosis | Readmission by diagnosis category |
| ProcedureID | Foreign key to the procedure performed. | Text (FK) | Links to tariff/procedure cost benchmarks. | PR005 | Dim_Procedure | Cost vs. standard tariff |
| PayerID | Foreign key to the funding source. | Text (FK) | Distinguishes NHS vs. private/other funding. | P001 | Dim_Payer | Funding mix |
| PatientHomeLocationID | Foreign key to the patient's home location. | Text (FK) | Enables geographic/equity analysis. | L026 | Dim_Location | Regional access patterns |
| AdmissionDateKey | Date key for admission (active relationship to Dim_Date). | Integer (FK) | Drives all time-series trending. | 20220101 | Dim_Date (active) | All monthly/yearly trends |
| DischargeDateKey | Date key for discharge (inactive relationship to Dim_Date). | Integer (FK) | Needed for discharge-based measures via USERELATIONSHIP. | 20220104 | Dim_Date (inactive) | Discharge-based LOS validation |
| AdmissionDate | Calendar date of admission. | Date | Human-readable mirror of AdmissionDateKey. | 2022-01-01 | — | — |
| DischargeDate | Calendar date of discharge. | Date | Used to derive LengthOfStayDays. | 2022-01-04 | — | Length of stay |
| AdmissionMonth | Year-month of admission. | Text | Convenience grouping column for monthly charts. | 2022-01 | — | Monthly volume/cost trends |
| AppointmentType | Category of care episode. | Text | Outpatient, Emergency, Diagnostic, Elective Inpatient, Day Case or Maternity. | Emergency | — | Activity mix |
| PatientClass | Whether the encounter required a bed. | Text | Inpatient vs. Non-Inpatient — the core capacity-planning split. | Inpatient | — | Bed-day planning |
| AdmissionSource | Route by which the patient arrived. | Text | e.g. NHS 111, GP referral, transfer. | NHS 111 | — | Referral pathway analysis |
| DischargeDestination | Where the patient went after discharge. | Text (nullable) | Null for Cancelled/Not-Attended encounters — structural, not missing. | Home | — | Discharge planning |
| TriageCategory | ED triage acuity, 1 (Immediate) to 5 (Non-urgent). | Text (nullable) | Only populated for Emergency encounters. | 3 - Urgent | — | ED triage mix, wait-by-triage |
| ReferralToTreatmentDays | Days from referral to treatment. | Integer | The NHS statutory RTT measure (18-week / 126-day standard). | 24 | — | RTT compliance |
| WaitTimeMinutes | Wait time in minutes for the encounter. | Integer | General access/experience measure across all encounter types. | 174 | — | Wait time by encounter type |
| AandEWaitHours | A&E-specific wait, arrival to treatment. | Decimal (nullable) | Only populated for Emergency encounters; basis for the 4-hour standard. | 3.32 | — | 4-hour compliance |
| LengthOfStayDays | Total inpatient stay length in days. | Integer | Core operational efficiency and bed-flow measure. | 9 | — | Avg. LOS by department/severity |
| BedDays | Bed-days consumed (mirrors LOS for this model). | Integer | Aggregate capacity consumption. | 9 | — | Total bed days |
| CostGBP | Actual total cost of the encounter. | Decimal | Primary financial performance measure. | 13255.17 | — | Total cost, cost by department/hospital |
| TariffGBP | NHS national tariff value for the encounter. | Decimal | Benchmark cost should be charged against. | 14617.33 | — | Cost vs. tariff variance |
| MedicationCostGBP | Medication cost component. | Decimal | Sub-component of CostGBP. | 156.88 | — | Cost composition |
| LabCostGBP | Laboratory cost component. | Decimal | Sub-component of CostGBP. | 177.11 | — | Cost composition |
| ImagingCostGBP | Imaging cost component. | Decimal | Sub-component of CostGBP; largest ancillary cost line. | 0.00 | — | Cost composition |
| PatientSatisfactionScore | Patient-reported satisfaction, 0–10. | Decimal (nullable) | Null for Cancelled/Not-Attended encounters. | 7.1 | — | Avg. satisfaction |
| ReadmittedWithin30Days | Whether the patient returned within 30 days. | Text (Yes/No) | The core NHS quality/safety indicator used throughout Clinical Performance. | No | — | Readmission rate (all cuts) |
| Outcome | Clinical outcome of the encounter. | Text | Recovered, Improved, Ongoing, Referred, Deteriorated, Deceased, Not attended, Cancelled. | Ongoing treatment | — | Outcome distribution |
| MortalityFlag | Whether the encounter ended in death. | Text (Yes/No) | Core clinical risk indicator. | No | — | Mortality rate |
| CancelledFlag | Whether the encounter was cancelled. | Text (Yes/No) | Lost-capacity indicator. | No | — | Cancellation rate |
| NoShowFlag | Whether the patient did not attend. | Text (Yes/No) | Lost-capacity indicator, distinct from cancellation. | No | — | No-show rate |
| CreatedBySystem | Source system that generated the record. | Text | Data lineage / audit trail field. | A&E System | — | — |

## Data Dictionary — Dimension Tables

| Table | Key Columns | Description |
|---|---|---|
| Dim_Patient | PatientID, DateOfBirth, AgeBand, Gender, Ethnicity, DeprivationQuintile, LongTermConditionFlag, HomeRegion | One row per patient (12,000 rows). Deprivation and LTC flags support health-equity analysis; all patient-insight views are aggregate-only. |
| Dim_Hospital | HospitalID, HospitalName, HospitalType, NHSRegion, BedCapacity, Latitude/Longitude | One row per hospital (16 rows). Includes bed capacity for cost-per-bed and geographic coordinates for the network map. |
| Dim_Department | DepartmentID, DepartmentName, ClinicalGroup, CostIndex, BaseReadmissionRisk | One row per clinical department (16 rows), shared across all hospitals. |
| Dim_Consultant | ConsultantID, ConsultantName, DepartmentID, Grade, YearsExperience, EmploymentType | One row per consultant (360 rows). Supports workload and seniority analysis. |
| Dim_Diagnosis | DiagnosisID, ICD10Code, DiagnosisCategory, SeverityBand | One row per diagnosis (30 rows), coded to ICD-10 with a severity band. |
| Dim_Procedure | ProcedureID, OPCSCode, ProcedureCategory, StandardTariffGBP | One row per procedure (18 rows), coded to OPCS with standard tariff. |
| Dim_Payer | PayerID, PayerType, FundingGroup, ApproxShare | One row per funding source (6 rows) — NHS, private insurance, self-pay, overseas, charity, clinical trial. |
| Dim_Location | LocationID, City, County, UKRegion, Latitude/Longitude | One row per UK location (48 rows) used for patient home-region analysis. |
| Dim_Date | DateKey, Date, FinancialYear, FinancialQuarter, IsWeekend, Season | One row per calendar day (1,826 rows, 2022–2026), pre-built with NHS financial-year logic. Role-playing dimension: joined twice from the fact table. |

## Glossary

| Term | Meaning |
|---|---|
| RTT | Referral to Treatment — the NHS statutory access standard (18 weeks / 126 days) |
| 4-Hour Standard | NHS constitutional standard: 95% of A&E attendances seen within 4 hours |
| LOS | Length of Stay — nights spent as an inpatient |
| Tariff | The NHS national reference price for a given procedure/encounter |
| Role-playing dimension | One dimension table referenced by more than one relationship from a fact table (here: Dim_Date via Admission and Discharge) |
| Star schema | A data model with one central fact table joined many-to-one to surrounding dimension tables |
| LTC | Long-Term Condition flag on the patient record |

## References

- NHS England — Referral to Treatment (RTT) waiting times statistics methodology
- NHS England — A&E attendances and emergency admissions statistics (4-hour standard)
- NHS national tariff payment system documentation
- Kimball, R. — *The Data Warehouse Toolkit* (star schema design methodology referenced throughout the Data Model section)

