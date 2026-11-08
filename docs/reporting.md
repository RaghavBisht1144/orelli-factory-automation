# Reporting and PDF Generation

## Purpose

The reporting subsystem converts factory operational data into structured management and worker-facing reports.

The system combines PostgreSQL data, n8n workflows, Google services, and Telegram delivery.

## Main Reporting Areas

Reporting currently includes:

- Worker wage reports
- Weekly wage summaries
- Factory summaries
- Department progress
- Purchase-order status
- Wage balances
- Worker advances
- Grindery summaries
- Operational management views

## Worker Wage Report

The worker wage report workflow produces a worker-level wage summary using structured wage data from PostgreSQL.

Sanitized workflow:

n8n/wages/worker-wage-pdf.json

The workflow integrates with:

- PostgreSQL
- Google Sheets
- Google Drive
- Google Slides
- Telegram

The generated report can include information such as:

- Worker production
- Compensation
- Advances
- Payments
- Outstanding balances
- Wage-period summaries

## Weekly Wage Summary

The weekly wage workflow provides a broader summary of wage activity across the factory.

Sanitized workflow:

n8n/wages/weekly-wage-pdf.json

This workflow can aggregate wage information for management review.

## Reporting Database Views

Reporting relies on PostgreSQL views so calculations remain centralized.

Important views include:

- vw_wage_reporting_items
- vw_wage_worker_advance_ledger
- vw_wage_worker_advance_balance
- vw_wage_payment_balance
- vw_wage_payment_group_worker_summary
- vw_wage_worker_po_payable_after_advance
- vw_master_daily_factory_summary
- vw_master_department_progress
- vw_master_department_updates
- vw_master_po_control
- vw_master_po_history
- vw_grindery_consumption_summary
- vw_grindery_cycle_live_production

## Factory Summaries

The reporting layer can summarize operational information across:

- Active purchase orders
- Production progress
- Department activity
- Inventory
- Worker wages
- Grindery operations
- QC and dispatch
- Procurement

These summaries are also available to the AI Factory Copilot for management analysis.

## Google Sheets Integration

Google Sheets provides operational visibility and supports report-generation workflows.

Relevant synchronized areas include:

- PO control
- Daily department updates
- Worker production
- Size progress
- Factory summaries
- Wage entries
- Worker wage summaries
- Weekly wage summaries
- Wage payments
- Wage balances
- Grindery information
- System configuration

PostgreSQL remains the authoritative database.

## Google Drive and Slides

Some report workflows use Google Drive and Google Slides as document-generation components.

This allows structured factory data to be converted into formatted reports before delivery.

## Telegram Delivery

Telegram is used as a delivery interface for selected reports.

A user can request information through the factory bot or AI Copilot and receive generated outputs through the same operational interface.

## AI Reporting

The AI Factory Copilot can use reporting views and read tools to answer questions such as:

- What is the wage summary for this worker?
- What is outstanding this week?
- Which PO is delayed?
- Which department is behind?
- What inventory risks exist?
- What happened in the factory today?
- What needs management attention?

## Reporting Design Principle

Business calculations should remain in PostgreSQL views and backend functions wherever possible.

The reporting workflow should primarily:

1. Retrieve structured data.
2. Format it.
3. Generate the required report.
4. Deliver it.

This avoids duplicating business logic across multiple n8n workflows.

## Security

Public workflow exports exclude:

- Google credentials
- bot credentials
- production file IDs
- customer data
- worker data
- private report contents

Placeholder IDs are used in sanitized public workflow definitions.

## Related Workflows

- n8n/wages/worker-wage-pdf.json
- n8n/wages/weekly-wage-pdf.json
- n8n/wages/wage-summary.json
- n8n/sync/master-data-sync.json
- n8n/main/ai-factory-copilot.json
