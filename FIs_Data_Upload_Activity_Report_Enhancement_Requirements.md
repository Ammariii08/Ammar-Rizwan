# FIs Data Upload Activity Report Enhancement
## Requirement Document

**Document Version:** 1.0  
**Date:** June 16, 2026  
**Prepared By:** Data Analytics Team  
**Status:** In Progress  

---

## Executive Summary

This document outlines the enhancement requirements for the **FIs Data Upload Activity Report** on Cognos. The report currently tracks data upload and ETL processing activities for Financial Institutions (FIs) and Microfinance Institutions (MFIs), but requires updates to support the new eCIB V2 data format and incorporate Real-Time Data Layer (RTDL) weekly upload tracking for NBFCs.

---

## 1. Background & Context

### 1.1 Current Report Overview
The FIs Data Upload Activity Report provides visibility into:
- Bank names and their FI classification types (NBFC, CB, MFI, DFI, Modaraba)
- Latest data upload activity timestamps
- ETL (Extract, Transform, Load) processing status and completion dates
- Data upload frequency and compliance tracking

### 1.2 Existing Problem Statement

Currently, the Cognos report has two critical limitations:

**Issue #1: eCIB V2 Format Data Tracking Gap**
- Several banks have transitioned to the eCIB V2 data format (starting December 2025)
- For these eCIB V2 clients, the "Latest Month" and "ETL Till" columns remain static at December 2025
- ETL processes are running for these clients, but the report does not reflect the actual progress
- **Impact:** Lack of visibility into ETL processing status for eCIB V2 format clients

**Issue #2: NBFC Real-Time Data Layer (RTDL) Weekly Uploads Not Captured**
- Multiple NBFC banks have begun uploading data in weekly batches through the Real-Time Data Layer (RTDL)
- The report only captures monthly ETL completion for NBFC clients
- Weekly upload activities and their ETL processing status are completely invisible in the current report
- **Impact:** Incomplete data visibility; inability to track weekly ETL cycles for RTDL data

---

## 2. Scope of Changes

### 2.1 What Will Be Updated

#### **Requirement 1: eCIB V2 Data Format Support**
- Extend the ETL tracking mechanism to include clients reporting data in eCIB V2 format
- Update the "Latest Month" column to reflect the most recent data upload month for eCIB V2 clients
- Update the "ETL Till" column to reflect the latest month for which ETL processing has been completed for eCIB V2 clients
- Ensure data from both consumer and corporate eCIB V2 formats are tracked

#### **Requirement 2: RTDL Weekly Data Layer Support**
- Add a new column: **"RTDL Latest Week"** (positioned after the "Latest Month" column)
  - Displays the latest week in which NBFC clients uploaded data through RTDL
  - Format: Date (e.g., 07-Apr-2026 representing the week ending on that date)
  - Applies to: NBFC clients with weekly RTDL uploads

- Add a new column: **"RTDL ETL Till"** (positioned after the "ETL Till" column)
  - Displays the latest week for which ETL processing has been completed for RTDL data
  - Format: Date (e.g., 25-Mar-2026 representing the week ending on that date)
  - Applies to: NBFC clients with weekly RTDL uploads

---

## 3. Business Benefits & Value

### 3.1 Improved Data Visibility
- **Complete Coverage:** The report will now track all active data formats (legacy formats, eCIB V2, and RTDL) in a single view
- **Accurate Status:** eCIB V2 clients will show current ETL processing status instead of static December 2025 dates
- **Real-Time Insights:** NBFC clients uploading weekly data will have weekly ETL progress visible

### 3.2 Enhanced Decision Making
- **Timely Alerts:** Identify data upload delays and ETL processing bottlenecks immediately
- **Client Compliance Tracking:** Easily identify which clients are uploading and processing data on schedule
- **Performance Monitoring:** Track ETL cycle times at both monthly and weekly granularity

### 3.3 Operational Efficiency
- **Single Source of Truth:** One consolidated report for all data upload and ETL activities
- **Reduced Manual Tracking:** Eliminates need for separate reports or manual verification for eCIB V2 and RTDL data
- **Faster Issue Resolution:** Clear visibility enables faster identification and resolution of data pipeline issues

---

## 4. Technical Specifications

### 4.1 Data Source Updates
The Cognos report must query:
- **Legacy Formats:** Consumer, Corporate, MFI data uploads (existing logic)
- **eCIB V2 Formats:** Consumer and Corporate eCIB V2 data uploads and ETL status
- **RTDL Data:** Weekly upload timestamps and weekly ETL completion dates for NBFC clients

### 4.2 Column Definitions

| Column Name | Data Type | Description | Applicable To |
|---|---|---|---|
| S# | Numeric | Serial number | All FIs |
| FI Name | Text | Bank/Institution name | All FIs |
| FI Type | Text | Classification (NBFC, CB, MFI, DFI, Modaraba) | All FIs |
| Latest Login | DateTime | Last login timestamp | All FIs |
| Last Upload Activity | DateTime | Most recent data upload timestamp | All FIs |
| First Month | Date | Month of first data submission | All FIs |
| Latest Month | Date (MMM YYYY) | Latest month for which monthly data was uploaded | Monthly Format Clients |
| **RTDL Latest Week** | Date (DD-MMM-YYYY) | Latest week ending date for RTDL data upload | NBFC with RTDL |
| ETL Till | Date (MMM YYYY) | Latest month for which monthly ETL processing completed | Monthly Format Clients |
| **RTDL ETL Till** | Date (DD-MMM-YYYY) | Latest week ending date for which RTDL ETL processing completed | NBFC with RTDL |
| Data Missing | Status | Flag for missing/incomplete data | All FIs |

---

## 5. Updated Report Format

### 5.1 New Column Structure (Example with all data types)

```
Sr | FI Name              | FI Type | Latest Login | Last Upload | First Month | Latest Month | RTDL Latest Week | ETL Till | RTDL ETL Till | Data Missing
---|----------------------|---------|--------------|-------------|-------------|--------------|------------------|----------|---------------|-------------
1  | OLP Financial Svcs   | NBFC    | Tue, Jun 16  | Tue, Jun 16 | Jul 2018    | Dec 2024     | 14-Jun-2026     | Dec 2024 | 31-May-2026   | No
2  | United Bank Limited  | CB      | Mon, Jun 15  | Tue, Jun 15 | Nov 2018    | Dec 2024     | N/A             | Dec 2024 | N/A           | No
3  | Zarai Taraqqi Bank   | NBFC    | Tue, Jun 15  | Mon, Jun 15 | Dec 2025    | May 2026     | 07-Jun-2026     | Apr 2026 | 31-May-2026   | Yes
4  | Muawin               | NBFC    | Mon, Jun 15  | Mon, Jun 15 | Feb 2021    | Jun 2026     | 14-Jun-2026     | Jun 2026 | 07-Jun-2026   | Yes
5  | OPMCL                | MFI     | Tue, Jun 16  | Tue, Jun 16 | Jun 2012    | Aug 2025     | N/A             | Apr 2026 | N/A           | No
```

### 5.2 Report Display Rules

**For NBFC Clients with RTDL Weekly Uploads:**
- Display both "Latest Month" AND "RTDL Latest Week" columns
- Display both "ETL Till" AND "RTDL ETL Till" columns
- Allows tracking of both monthly and weekly data pipelines

**For Monthly Format Clients (Traditional):**
- Display "Latest Month" and "ETL Till" columns only
- "RTDL Latest Week" and "RTDL ETL Till" columns display "N/A"
- Maintains backward compatibility

**For eCIB V2 Format Clients:**
- "Latest Month" and "ETL Till" columns are updated dynamically (no longer stuck at Dec 2025)
- "RTDL Latest Week" and "RTDL ETL Till" columns display "N/A"
- Ensures accurate tracking of eCIB V2 ETL progress

---

## 6. Acceptance Criteria

### 6.1 Functional Requirements
- [ ] eCIB V2 consumer format clients show updated "Latest Month" and "ETL Till" values
- [ ] eCIB V2 corporate format clients show updated "Latest Month" and "ETL Till" values
- [ ] New "RTDL Latest Week" column displays for NBFC clients with weekly uploads
- [ ] New "RTDL ETL Till" column displays for NBFC clients with weekly uploads
- [ ] Legacy clients (Consumer, Corporate, MFI monthly formats) continue to display correctly
- [ ] Data updates reflect ETL processing within 24 hours of completion

### 6.2 Data Quality Requirements
- [ ] No null values in date fields; use "N/A" for clients without weekly RTDL data
- [ ] Date formats are consistent (MMM YYYY for monthly, DD-MMM-YYYY for weekly)
- [ ] Data refresh frequency remains optimal (no performance degradation)

### 6.3 User Experience Requirements
- [ ] Report remains easy to scan and understand
- [ ] New columns are clearly labeled and self-explanatory
- [ ] Hover-over tooltips explain RTDL vs. Legacy data tracking
- [ ] Report can be exported to Excel without formatting issues

---

## 7. Implementation Considerations

### 7.1 Phased Rollout
- **Phase 1:** Update eCIB V2 format tracking (addresses Issue #1)
- **Phase 2:** Add RTDL weekly columns for NBFC clients (addresses Issue #2)
- **Phase 3:** Testing, validation, and user acceptance testing

### 7.2 Data Migration
- Historical data for eCIB V2 clients should be back-populated where available
- RTDL data tracking begins from the week data ingestion started

### 7.3 Documentation & Training
- Update Cognos report documentation with new column definitions
- Brief stakeholders on interpretation of weekly vs. monthly ETL tracking
- Create FAQ for common questions about eCIB V2 and RTDL data

---

## 8. Success Metrics

- **Coverage:** 100% of active FIs tracked in the report (legacy + eCIB V2 + RTDL)
- **Accuracy:** Zero discrepancies between report and actual ETL completion dates
- **Timeliness:** ETL completion reflected in report within 24 hours
- **User Adoption:** Positive feedback from data team on visibility and usability

---

## Quick Reference: Before & After Comparison

### Current State (Problematic)
```
eCIB V2 Clients:
- Latest Month: Dec 2025 (STATIC - NOT UPDATING)
- ETL Till: Dec 2025 (STATIC - NOT UPDATING)
- Issue: Cannot see ETL progress beyond Dec 2025

NBFC RTDL Clients:
- No visibility of weekly uploads
- No visibility of weekly ETL processing
- Issue: Complete blind spot for weekly data pipeline
```

### After Enhancement (Proposed)
```
eCIB V2 Clients:
- Latest Month: May 2026 (DYNAMIC - UPDATES WITH ACTUAL DATA)
- ETL Till: Apr 2026 (DYNAMIC - REFLECTS ACTUAL ETL STATUS)
- Benefit: Full visibility of eCIB V2 ETL progress

NBFC RTDL Clients:
- RTDL Latest Week: 14-Jun-2026 (SHOWS LATEST UPLOAD)
- RTDL ETL Till: 07-Jun-2026 (SHOWS ETL PROGRESS)
- Benefit: Complete visibility of weekly data pipeline
```

---

**Document End**