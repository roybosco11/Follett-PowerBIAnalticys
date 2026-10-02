# Follett-PowerBIAnalticys
# Power BI Analytics — Azure DevOps Version Control

## Overview

This repository provides source control and release management for Power BI semantic models and reports using:

- Power BI Desktop
- Power BI Project (`.pbip`) format
- TMDL semantic model definitions
- Git
- Azure DevOps Repos
- Azure Boards
- Azure DevOps Pipelines
- Power BI / Microsoft Fabric
- DEV → QA → STAGE → PROD environments

The primary objective is to provide a controlled, auditable, and repeatable development process for Power BI solutions.

> **Important:** `.pbix` files should not be used as the primary source-controlled artifact. Power BI solutions should be saved using the `.pbip` project format whenever supported.

---

# 1. Development Workflow

The standard development workflow is:

```text
Azure Boards Work Item
        │
        ▼
Create Feature Branch
        │
        ▼
Power BI Desktop
        │
        ▼
Modify PBIP / TMDL
        │
        ▼
Commit Changes
        │
        ▼
Push Feature Branch
        │
        ▼
Create Pull Request
        │
        ▼
Code / Model Review
        │
        ▼
Merge to main
        │
        ▼
Deploy DEV
        │
        ▼
Validate
        │
        ▼
QA
        │
        ▼
STAGE
        │
        ▼
PROD
```

`main` is the authoritative source for deployable Power BI source code.

---

# 2. Repository Structure

```text
PowerBI-Analytics/
│
├── README.md
├── .gitignore
│
├── src/
│   │
│   ├── Sample-Inventory/
│   │   ├── SemanticModels/
│   │   │   └── SalesModel/
│   │   │
│   │   └── Reports/
│   │       ├── ExecutiveSales/
│   │       ├── RegionalSales/
│   │       └── StorePerformance/
│   │
│   ├── Finance/
│   │   ├── SemanticModels/
│   │   └── Reports/
│   │
│   └── Operations/
│       ├── SemanticModels/
│       └── Reports/
│
├── pipelines/
│   ├── templates/
│   ├── validate.yml
│   ├── deploy-dev.yml
│   ├── deploy-qa.yml
│   ├── deploy-stage.yml
│   └── deploy-prod.yml
│
├── config/
│   ├── dev/
│   ├── qa/
│   ├── stage/
│   └── prod/
│
├── scripts/
│   ├── validation/
│   └── deployment/
│
├── tests/
│   ├── semantic-model/
│   └── reports/
│
└── docs/
    ├── architecture/
    ├── standards/
    └── deployment/
```

---

# 3. Folder Responsibilities

## `src/`

Contains the Power BI source code.

All Power BI Desktop development should ultimately be represented here using PBIP-compatible source files.

Example:

```text
src/
└── Sales/
    ├── SemanticModels/
    │   └── EnterpriseSales/
    │
    └── Reports/
        ├── ExecutiveSales/
        ├── RegionalSales/
        └── StorePerformance/
```

A semantic model may support multiple reports.

```text
Enterprise Sales Semantic Model
              │
       ┌──────┼──────────┐
       ▼      ▼          ▼
 Executive  Regional    Store
   Sales      Sales   Performance
```

Do not create duplicate semantic models simply because multiple reports consume the same model.

---

## `pipelines/`

Contains Azure DevOps YAML pipelines and reusable deployment templates.

```text
pipelines/
├── templates/
├── validate.yml
├── deploy-dev.yml
├── deploy-qa.yml
├── deploy-stage.yml
└── deploy-prod.yml
```

Pipelines may eventually perform:

- PBIP validation
- TMDL validation
- Semantic model validation
- Deployment
- Environment configuration
- DEV deployment
- QA promotion
- STAGE promotion
- PROD promotion

---

## `config/`

Contains non-secret environment-specific deployment configuration.

```text
config/
├── dev/
├── qa/
├── stage/
└── prod/
```

Examples of environment-specific configuration include:

- Workspace names
- Workspace IDs
- Database/server names
- Deployment settings
- Parameter mappings

Passwords, client secrets, tokens, connection credentials, or other sensitive information **must not** be committed to this directory.

Use secured Azure DevOps variables, variable groups, or an approved secrets-management solution.

---

## `scripts/`

Contains automation scripts used for validation and deployment.

```text
scripts/
├── validation/
└── deployment/
```

---

## `tests/`

Contains automated or documented validation tests.

```text
tests/
├── semantic-model/
└── reports/
```

Examples include:

- Model validation
- DAX validation
- Naming-standard checks
- Deployment validation
- Report metadata validation

---

## `docs/`

Contains project documentation.

```text
docs/
├── architecture/
├── standards/
└── deployment/
```

Use this directory for architecture decisions, development standards, troubleshooting guidance, and deployment documentation.

---

# 4. Branching Strategy

The repository uses a simplified feature-branch strategy.

```text
main
 │
 ├── feature/*
 ├── bugfix/*
 └── hotfix/*
```

Examples:

```text
feature/1425-gross-margin-kpi
feature/1450-regional-sales
bugfix/1501-ytd-calculation
hotfix/1525-sales-refresh
```

## Main Branch

`main` represents the approved source of truth.

Developers should **not directly modify `main`**.

Changes should normally reach `main` through a Pull Request.

---

# 5. Environment Strategy

Git branches and deployment environments serve different purposes.

Do not create permanent environment branches such as:

```text
dev
qa
stage
prod
```

unless there is a documented project-specific requirement.

The recommended approach is:

```text
                    main
                      │
                      ▼
                    DEV
                      │
                      ▼
                     QA
                      │
                      ▼
                   STAGE
                      │
                      ▼
                    PROD
```

The **same approved source** is promoted through all environments.

Environment-specific differences should be handled through configuration and deployment processes rather than maintaining separate copies of Power BI source code.

---

# 6. Developer Setup

## Prerequisites

Developers should have:

- Power BI Desktop
- Git
- Azure DevOps access
- Azure Repos access
- Visual Studio Code or another Git-capable editor
- Appropriate Power BI/Fabric workspace permissions

Configure Git identity:

```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@company.com"
```

Verify:

```bash
git config --list
```

---

# 7. Clone Repository

Clone the Azure DevOps repository:

```bash
git clone <azure-devops-repository-url>
```

Move into the repository:

```bash
cd PowerBI-Analytics
```

Verify the repository:

```bash
git status
```

---

# 8. Starting New Development

Always start from the latest `main`.

```bash
git checkout main
git pull origin main
```

Create a feature branch.

Example:

```bash
git checkout -b feature/1425-gross-margin-kpi
```

Branch names should include the Azure Boards work-item number whenever possible.

Format:

```text
feature/<work-item>-<description>
bugfix/<work-item>-<description>
hotfix/<work-item>-<description>
```

---

# 9. Power BI Development

Open the appropriate `.pbip` project using Power BI Desktop.

Example:

```text
src/
└── Sales/
    └── SemanticModels/
        └── SalesModel/
            └── SalesModel.pbip
```

Make the required Power BI changes.

Examples:

- Add or modify measures
- Add calculated columns
- Modify relationships
- Add report pages
- Modify visuals
- Modify RLS roles
- Change Power Query
- Update model metadata

Save the project before reviewing Git changes.

---

# 10. Review Changes

Before committing:

```bash
git status
```

Review differences:

```bash
git diff
```

With TMDL, semantic model changes can be reviewed as text.

Example:

```text
definition/
├── model.tmdl
├── relationships.tmdl
├── tables/
│   ├── DimCustomer.tmdl
│   ├── DimProduct.tmdl
│   └── FactSales.tmdl
└── roles/
```

Developers should review the changed files before committing them.

---

# 11. Commit Changes

Stage the changes:

```bash
git add .
```

Commit using a meaningful message:

```bash
git commit -m "1425 Add Gross Margin KPI"
```

Recommended format:

```text
<WorkItem> <Description>
```

Examples:

```text
1425 Add Gross Margin measure
1450 Add Regional Sales page
1501 Correct YTD calculation
1525 Update customer RLS
```

Avoid generic commit messages such as:

```text
changes
update
fix
test
latest
```

---

# 12. Push Feature Branch

Push the branch to Azure DevOps:

```bash
git push -u origin feature/1425-gross-margin-kpi
```

The branch will now be available in Azure Repos.

---

# 13. Create Pull Request

In Azure DevOps:

```text
Repos
  ↓
Pull Requests
  ↓
New Pull Request
```

Configure:

```text
Source:
feature/1425-gross-margin-kpi

Target:
main
```

Recommended PR title:

```text
PBI-1425 - Add Gross Margin KPI
```

The Pull Request should contain:

- Description of the change
- Azure Boards work item
- Impacted semantic model/report
- Testing performed
- Deployment considerations
- Reviewer(s)

---

# 14. Pull Request Review

Reviewers should check:

### Semantic Model

- Measures
- DAX changes
- Relationships
- Data types
- Model structure
- RLS
- Power Query changes
- Naming conventions

### Reports

- Report changes
- Visual changes
- Filters
- Drill-through
- Bookmarks
- Navigation
- Semantic-model connection

### General

- No credentials committed
- No unnecessary generated files
- No unintended changes
- Work item linked
- Testing completed

---

# 15. Merge to Main

After approval, merge the Pull Request into:

```text
main
```

Delete the feature branch after successful merge unless it is still required.

The resulting workflow is:

```text
Developer
    │
    ▼
Feature Branch
    │
    ▼
Commit
    │
    ▼
Push
    │
    ▼
Pull Request
    │
    ▼
Review
    │
    ▼
Approval
    │
    ▼
main
```

---

# 16. Main Branch Protection

The `main` branch should be protected using Azure DevOps Branch Policies.

Recommended initial policies:

| Policy | Recommendation |
|---|---|
| Pull Request | Required |
| Direct Push | Block |
| Minimum Reviewers | 1 minimum |
| Self Approval | Disabled |
| Linked Work Item | Required |
| Comment Resolution | Required |
| Build Validation | Required when CI is implemented |
| Force Push | Disabled |
| Policy Bypass | Restricted |

For critical production solutions, consider requiring two reviewers.

---

# 17. `.gitignore`

Recommended Power BI Git exclusions:

```gitignore
# Power BI local files
**/.pbi/localSettings.json
**/.pbi/cache.abf

# Temporary files
*.tmp
*.temp

# User-specific files
*.user

# Operating System
.DS_Store
Thumbs.db

# VS Code
.vscode/

# Python
__pycache__/
*.pyc

# Environment / Secret files
.env
*.secret
```

Never commit:

```text
Passwords
Client Secrets
Access Tokens
Service Principal Secrets
Personal Access Tokens
Database Credentials
Private Keys
```

---

# 18. Deployment Strategy

The same source should be promoted through:

```text
Azure Repo
    │
    ▼
   main
    │
    ▼
   DEV
    │
    ▼
    QA
    │
    ▼
  STAGE
    │
    ▼
   PROD
```

Do not maintain separate copies of the semantic model for each environment.

Avoid:

```text
SalesModel_DEV
SalesModel_QA
SalesModel_STAGE
SalesModel_PROD
```

in source control.

Instead maintain:

```text
SalesModel
```

and apply environment-specific configuration during deployment.

---

# 19. Initial Deployment Process

During the first phase of implementation, deployment may remain manual.

```text
PR Approved
     │
     ▼
Merge main
     │
     ▼
Deploy DEV
     │
     ▼
DEV Validation
     │
     ▼
Deploy QA
     │
     ▼
QA Validation
     │
     ▼
Deploy STAGE
     │
     ▼
Business/UAT Approval
     │
     ▼
Deploy PROD
```

This allows the team to establish Git and PBIP development practices before introducing full deployment automation.

---

# 20. Future CI/CD

The target state is:

```text
Feature Branch
      │
      ▼
Pull Request
      │
      ▼
Automated Validation
      │
      ▼
PR Approval
      │
      ▼
main
      │
      ▼
Azure DevOps Pipeline
      │
      ▼
DEV
      │
      ▼
QA Approval
      │
      ▼
QA
      │
      ▼
STAGE Approval
      │
      ▼
STAGE
      │
      ▼
Production Approval
      │
      ▼
PROD
```

Azure DevOps pipelines can later automate validation, deployment, configuration transformation, and promotion.

---

# 21. Shared Semantic Model Pattern

When multiple reports use the same semantic model, maintain the semantic model independently.

Example:

```text
Sales/
│
├── SemanticModels/
│   │
│   └── EnterpriseSales/
│       └── EnterpriseSales.SemanticModel/
│
└── Reports/
    │
    ├── ExecutiveSales/
    │
    ├── RegionalSales/
    │
    └── StorePerformance/
```

Architecture:

```text
                EnterpriseSales
                 Semantic Model
                       │
          ┌────────────┼────────────┐
          │            │            │
          ▼            ▼            ▼
     Executive      Regional      Store
       Sales          Sales     Performance
```

This reduces duplication and provides centralized governance for:

- Measures
- Relationships
- Business logic
- RLS
- Calculation groups
- Model definitions

---

# 22. Development Standards

All developers should follow these standards:

1. Start development from the latest `main`.
2. Create a feature or bugfix branch.
3. Associate development with an Azure Boards work item.
4. Use PBIP instead of PBIX for source-controlled development.
5. Use TMDL for semantic-model source control where supported.
6. Review `git diff` before committing.
7. Never commit credentials or secrets.
8. Use meaningful commit messages.
9. Push feature branches to Azure DevOps.
10. Create a Pull Request.
11. Do not directly modify `main`.
12. Obtain required reviewer approval.
13. Merge approved changes to `main`.
14. Promote the same approved source through DEV → QA → STAGE → PROD.

---

# 23. Quick Reference

Start work:

```bash
git checkout main
git pull origin main
git checkout -b feature/1425-gross-margin-kpi
```

After making Power BI changes:

```bash
git status
git diff
git add .
git commit -m "1425 Add Gross Margin KPI"
git push -u origin feature/1425-gross-margin-kpi
```

Then:

```text
Azure DevOps
     ↓
Create Pull Request
     ↓
Review
     ↓
Approve
     ↓
Merge → main
     ↓
Deploy
```

---

# 24. Source of Truth

The Azure DevOps Git repository is the source of truth for Power BI development.

```text
Azure Boards
     │
     ▼
Feature Branch
     │
     ▼
PBIP / TMDL
     │
     ▼
Pull Request
     │
     ▼
main
     │
     ▼
DEV → QA → STAGE → PROD
``
