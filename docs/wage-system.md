# Worker Wage System

## Purpose

The wage subsystem connects worker production activity to piece-rate compensation, advances, payments, outstanding balances, and wage reporting.

The system is designed to maintain an auditable record of:

- worker production
- payable work
- compensation
- advances
- payments
- outstanding balances

## Main Database Objects

Important wage-related structures include:

- wage_workers
- wage_production_entries
- wage_production_allocations
- wage_worker_allocations
- worker_compensation_entries
- wage_payments
- wage_payment_groups
- wage_payment_group_workers
- wage_worker_advance_transactions
- wage_worker_advance_entry_sessions
- wage_control_audit
- wage_write_commands
- wage_operation_workloads

## Worker Production

Worker production entries represent factory work completed by individual workers.

These records can include:

- worker
- purchase order
- SKU or article
- operation
- quantity
- applicable rate
- calculated compensation

Production data becomes the basis for wage calculation.

## Piece-Rate Compensation

The wage system supports piece-rate compensation.

Conceptually:

Completed quantity
x
Applicable operation rate
=
Worker compensation

Relevant functions include:

- fn_daily_worker_rate
- fn_daily_worker_rate_article
- fn_wage_allocate_production_entry
- fn_ai_factory_execute_piece_wage

## Worker Allocation

Production work may need to be allocated to specific workers.

Relevant structures include:

- wage_worker_allocations
- wage_production_allocations

These allow payable production to be associated with the correct worker.

## Compensation Entries

Worker compensation is recorded using:

- worker_compensation_entries

The AI/backend layer also includes:

- fn_ai_factory_execute_compensation

This allows compensation-related operations to be executed through structured backend logic.

## Worker Advances

The wage system supports worker advances separately from normal wage payments.

Important objects include:

- wage_worker_advance_transactions
- wage_worker_advance_entry_sessions

Relevant function:

- fn_wage_record_worker_advance_transaction
- fn_ai_factory_execute_advance

Advances can then be included when calculating the worker's final payable balance.

## Wage Payments

Worker payments are represented through:

- wage_payments
- wage_payment_groups
- wage_payment_group_workers

This supports both individual and grouped payment workflows.

Relevant backend function:

- fn_ai_factory_execute_payment

## Wage Balance

The system calculates worker balances using database views rather than relying only on manual calculations.

Important views include:

- vw_wage_payment_balance
- vw_wage_worker_advance_balance
- vw_wage_worker_advance_ledger
- vw_wage_worker_allocation_balance
- vw_wage_worker_po_payable_after_advance
- vw_wage_payable_production_entries
- vw_wage_payment_item_balance
- vw_wage_sku_operation_balance

These views allow the system to answer:

- How much has a worker earned?
- How much has already been paid?
- How much advance has been taken?
- What amount remains payable?
- Which production entries remain unpaid?

## Wage Summary

The AI Factory Copilot can retrieve wage summaries through the dedicated wage-summary workflow.

Sanitized workflow:

n8n/wages/wage-summary.json

This allows management to query wage information using natural language.

## Worker Wage PDF

The system includes an automated worker wage-report workflow.

Sanitized workflow:

n8n/wages/worker-wage-pdf.json

The workflow integrates with:

- PostgreSQL
- Google Sheets
- Google Drive
- Google Slides
- Telegram

It can generate structured wage reports for worker-level review.

## Weekly Wage Summary

The factory also has a weekly wage-summary workflow.

Sanitized workflow:

n8n/wages/weekly-wage-pdf.json

This provides a broader factory-level view of wage activity.

## Wage Reporting Views

Important reporting views include:

- vw_wage_reporting_items
- vw_wage_worker_advance_ledger
- vw_wage_worker_advance_balance
- vw_wage_payment_balance
- vw_wage_payment_group_worker_summary
- vw_wage_worker_po_payable_after_advance

These views reduce the need to reproduce wage calculations independently in every workflow.

## AI Wage Operations

The AI/backend architecture includes functions for operations such as:

- compensation
- piece wages
- advances
- payments

Relevant functions include:

- fn_ai_factory_execute_compensation
- fn_ai_factory_execute_piece_wage
- fn_ai_factory_execute_advance
- fn_ai_factory_execute_payment
- fn_ai_wage_prepare
- fn_ai_wage_payment_prepare

The AI determines intent and prepares structured information.

The backend remains responsible for deterministic execution.

## Wage Safety

Wage-changing actions are treated as business mutations.

The intended AI flow is:

1. User gives a natural-language instruction.
2. AI identifies the worker and requested operation.
3. Current wage state is inspected.
4. Exact proposed changes are generated.
5. User receives a confirmation token.
6. Only confirmed actions execute.
7. The resulting transaction remains auditable.

## Auditing

The wage subsystem includes dedicated audit structures such as:

- wage_control_audit
- wage_write_commands

This allows wage-related operations to remain traceable.

## Google Sheets Synchronization

Wage information is also synchronized into operational Google Sheets views where required.

Relevant areas include:

- wage entries
- worker wage summaries
- weekly wage summaries
- wage payments
- wage balances

PostgreSQL remains the authoritative source.

## Design Goal

The wage subsystem is intended to replace fragmented manual wage tracking with a structured pipeline:

Worker production
-> Rate resolution
-> Compensation
-> Advance adjustment
-> Payment
-> Balance
-> Report
-> Audit

The resulting wage information is reusable by:

- factory management
- reporting workflows
- Google Sheets
- PDF reports
- AI Factory Copilot
