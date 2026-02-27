# RPA Process Review Agent & Defect Predictor

## Technical Architecture & System Design Document

**Version:** 1.0\
**Generated On:** 2026-02-27\
**Architecture Owner:** AI Agentic Architect (Testing Domain)

------------------------------------------------------------------------

# 1. Objective

Design a lightweight internal web-based tool to analyze Blue Prism
process XML, correlate with Jira defect history, compute technical
complexity, and predict defect probability using a Hybrid (Rule-based +
ML) model.

------------------------------------------------------------------------

# 2. High-Level Architecture

User (Web UI) ↓ FastAPI Backend ↓
-------------------------------------------------- \| 1. XML Complexity
Extractor \| \| 2. PDD Metadata Extractor \| \| 3. Jira Defect Data
Fetcher \| \| 4. Feature Engineering Layer \| \| 5. Hybrid Risk Engine
\| \| 6. Model Training Module (On-Demand) \| \| 7. Local Persistence
Layer (SQLite) \| -------------------------------------------------- ↓
Risk Output (JSON + UI Display)

------------------------------------------------------------------------

# 3. Input Sources

## 3.1 Blue Prism Process XML Export

Technical Complexity Features: - Number of Pages - Total Stages -
Decision Stage Count - Loop Stage Count - Nested Depth - Code Stage
Count - Exception Block Count - Recovery/Resume Presence - Number of
Objects Used - Global Variable Count - Environment Variable Usage -
Hardcoded Values (via XML parsing)

## 3.2 Runtime / Operational Metrics (Optional)

-   Session failure rate
-   Retry count
-   Execution frequency

## 3.3 Jira Defect Metadata

-   Issue Type = Bug
-   Priority
-   Severity
-   Status
-   Linked Story/Feature
-   Root Cause / Resolution (if available)

## 3.4 Zephyr Test Assets

-   Linked test cases
-   Execution coverage indicator

## 3.5 GitHub (Traceability Only)

-   Repository link mapping to process

## 3.6 PDD (Manual Upload)

Minimal Governance Metadata: - Business Criticality - Application
Dependency Count - Expected Transaction Volume - Exception Strategy
Documented (Yes/No) - Compliance Impact (Yes/No)

------------------------------------------------------------------------

# 4. System Design Components

## 4.1 Web Layer

-   FastAPI
-   Single Process Upload
-   Admin-triggered Model Retraining

## 4.2 XML Complexity Engine

Parses XML and computes structural metrics. Calculates:

Complexity Score = (Decision_Count × 2) + (Loop_Count × 3) +
(Nested_Depth × 5) + (Code_Stage_Count × 4) - (Exception_Block_Count ×
3)

------------------------------------------------------------------------

## 4.3 PDD Metadata Extractor

-   PDF/DOCX parsing
-   Rule-based keyword extraction
-   Structured metadata mapping

------------------------------------------------------------------------

## 4.4 Jira Integration Layer

REST API: GET /rest/api/3/search

Fetch: - Defects linked via hierarchy - Process name from issue
summary - Historical defect count - MTTR - Reopen rate

------------------------------------------------------------------------

# 5. Feature Engineering

Final ML Dataset Schema:

  Feature                 Source
  ----------------------- --------
  Decision_Count          XML
  Loop_Count              XML
  Nested_Depth            XML
  Code_Stage_Count        XML
  Exception_Block_Count   XML
  Dependency_Count        PDD
  Business_Criticality    PDD
  Defect_Count            Jira
  MTTR                    Jira
  Reopen_Rate             Jira

Label: - High Risk (1) - Low Risk (0)

------------------------------------------------------------------------

# 6. Hybrid Risk Engine

## 6.1 Rule-Based Layer

Triggers: - High Nested Depth - No Centralized Exception Handling - High
Defects in Previous Releases - High Dependency Count

## 6.2 ML Layer

Model: Logistic Regression or Random Forest - Outputs Defect Probability
(%) - Retrained On-Demand

------------------------------------------------------------------------

# 7. Output Design

User receives:

-   Complexity Score
-   Defect Probability (%)
-   Risk Category (Low / Medium / High)
-   Defect Probability Areas:
    -   High Nested Depth
    -   Previous Release Defects
    -   Missing Centralized Exception Handling

------------------------------------------------------------------------

# 8. Persistence Strategy

Local SQLite Database:

Tables: - Process_Features - Defect_History - Model_Metadata -
Prediction_Audit_Log

Future-ready repository abstraction for enterprise DB migration.

------------------------------------------------------------------------

# 9. Model Lifecycle

-   Admin clicks "Retrain Model"
-   Dataset refreshed from stored features
-   Model retrained
-   Version saved with timestamp
-   Prediction engine uses latest version

------------------------------------------------------------------------

# 10. Non-Functional Requirements

-   Lightweight internal deployment
-   Single-process analysis per run
-   Audit traceability of predictions
-   Future enterprise DB ready
-   Deterministic + Explainable hybrid model

------------------------------------------------------------------------

# 11. Phased Copilot Agent Prompt Structure

Phase 1 -- Build XML Parser\
Phase 2 -- Build PDD Metadata Extractor\
Phase 3 -- Integrate Jira API Fetcher\
Phase 4 -- Implement Feature Engineering\
Phase 5 -- Build Hybrid Risk Engine\
Phase 6 -- Implement Retraining Module\
Phase 7 -- Build Web UI & Reporting

------------------------------------------------------------------------

# End of Technical Architecture Document
