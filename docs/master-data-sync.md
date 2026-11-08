# Master Data Synchronization

## Purpose

The Master Data Sync subsystem keeps selected PostgreSQL-backed factory data synchronized with Google Sheets used for operational visibility, management reporting, and day-to-day factory workflows.

PostgreSQL remains the authoritative source of business data.

Google Sheets acts as a synchronized operational interface rather than the primary database.

## Main Workflow

Sanitized n8n workflow:

n8n/sync/master-data-sync.json

The workflow coordinates data movement between PostgreSQL and configured Google Sheets tabs.

## Main Responsibilities

The synchronization layer supports areas such as:

- Purchase-order control
- Department updates
- Worker production
- Size progress
- Daily factory summaries
- Wage entries
- Worker wage summaries
- Weekly wage summaries
- Wage payments
- Wage balances
- Grindery information
- Bot audit information
- System configuration
- SKU master information
- Factory expenses

## PostgreSQL as Source of Truth

The architecture follows this rule:

PostgreSQL
-> authoritative business state

Google Sheets
-> synchronized operational view

This avoids treating spreadsheet state as the only record of factory activity.

## Synchronization Flow

Typical synchronization flow:

PostgreSQL data
-> reporting or master view
-> n8n Master Data Sync
-> target Google Sheets tab
-> sync audit / queue handling

## Sync Queue

The system includes:

- sheet_sync_queue

This provides a structured place for synchronization work that needs to be processed.

The queue helps separate operational database changes from the external sheet update process.

## Sync Audit

The system also includes:

- sheet_sync_audit

This allows synchronization activity to be tracked and inspected.

Audit information is useful for:

- identifying failed syncs
- confirming completed syncs
- troubleshooting spreadsheet inconsistencies
- tracing synchronization history

## Master Data Views

Several PostgreSQL views support synchronized factory reporting.

Examples include:

- vw_master_po_control
- vw_master_po_control_base
- vw_master_po_history
- vw_master_po_list
- vw_master_daily_factory_summary
- vw_master_department_progress
- vw_master_department_updates
- vw_master_grinderies
- vw_master_system_config
- vw_master_bot_audit_log

These views provide structured data for downstream synchronization and reporting.

## Wage Synchronization

The Master Data Sync workflow also integrates wage information.

Relevant synchronized areas include:

- Wage entries
- Worker wage summaries
- Weekly wage summaries
- Wage payments
- Wage balances

The wage calculations themselves remain backed by PostgreSQL structures and views.

## Production Synchronization

Production-related synchronized data can include:

- Department progress
- Department updates
- Worker production
- Size progress
- PO control
- Factory summaries

This gives management a spreadsheet-based operational view without replacing the database backend.

## Grindery Synchronization

The synchronization layer also exposes grindery-related information for management visibility.

Relevant reporting views include:

- vw_grindery_consumption_summary
- vw_grindery_cycle_live_production
- vw_master_grinderies

## Error Handling

External services can fail independently of the database.

For this reason, synchronization should not make PostgreSQL dependent on Google Sheets availability.

The preferred model is:

Database transaction completes
-> synchronization work is queued or triggered
-> Google Sheets is updated
-> result is audited

This allows the operational database to remain reliable even if an external API temporarily fails.

## Security

The public repository does not contain:

- Google credentials
- service-account private keys
- production Google file IDs
- live credential IDs
- client spreadsheet contents

Sanitized workflow exports use placeholder Google file IDs.

## Design Principles

1. PostgreSQL remains authoritative.
2. Google Sheets is a synchronized operational interface.
3. Business calculations belong in PostgreSQL where practical.
4. Synchronization activity should be auditable.
5. External API failure should not corrupt authoritative business state.
6. Public workflow exports must not expose Google credentials or production file identifiers.

## Related Database Objects

Important objects include:

- sheet_sync_queue
- sheet_sync_audit
- client_sheet_bom_source
- client_sheet_inventory_source
- client_sheet_procurement_source
- client_sheet_projects

## Related Workflow

n8n/sync/master-data-sync.json
