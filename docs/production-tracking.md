# Production Tracking

## Purpose

The production subsystem tracks factory progress from purchase-order release through department-level execution, scheduling, batch recording, quality control, and dispatch preparation.

It provides structured operational data for both workflow automation and management analysis.

## Main Responsibilities

The production system supports:

- Production progress updates
- Department-level production tracking
- Production schedules
- Production planning
- Production rescheduling
- Batch recording
- Whole-PO production updates
- Historical production state
- Production parameter requests
- Production wizard/session flows
- AI-assisted production updates

## Main Database Objects

Important production-related objects include:

- production_progress_updates
- production_schedules
- production_plan_results
- production_planning_settings
- production_stages
- production_update_requests
- production_update_wizard_sessions
- production_parameter_requests
- production_reschedule_audit
- production_reschedule_wizard_sessions
- po_historical_production_state
- whole_po_production_sessions
- ai_production_batches
- ai_production_events
- ai_production_update_commands

## Production Update Flow

A production update generally follows this path:

Purchase order
-> Resolve PO reference
-> Identify production stage or department
-> Validate requested update
-> Record progress
-> Update production state
-> Refresh operational views
-> Make updated state available to reporting and AI analysis

## AI Production Updates

The AI layer can interpret natural-language production instructions and convert them into structured production updates.

Relevant database functions include:

- fn_ai_production_resolve_po_reference
- fn_ai_production_update_prepare
- fn_ai_production_batch_record
- fn_ai_production_batch_record_core

The AI is responsible for understanding intent.

The backend remains responsible for deterministic validation and execution.

## Batch Production Recording

Batch production operations allow multiple production records to be handled together.

The workflow supports:

- batch-level input
- multiple batch items
- validation
- production event creation
- controlled execution
- auditability

## Department Tracking

Department-level updates are used to track operational progress across factory stages.

Relevant functions and structures include:

- department_daily_updates
- department_po_daily_updates
- department_master
- department_wizard_sessions
- department_daily_v2_sessions
- fn_department_daily_v2_confirm

These records support both factory-floor operations and management-level reporting.

## Production Scheduling

The scheduling subsystem supports:

- planned production dates
- effective schedules
- rescheduling
- audit history
- production planning settings

The view:

vw_production_schedule_effective

provides an effective schedule representation for downstream use.

## Rescheduling

Production changes can create rescheduling records rather than silently overwriting planning history.

Relevant objects include:

- production_reschedule_audit
- production_reschedule_wizard_sessions

This allows operational changes to remain traceable.

## Historical Production State

Historical PO production state is retained using:

- po_historical_production_state

This supports:

- comparisons over time
- reconstruction of previous operational states
- analysis
- reporting

## Whole-PO Updates

The system also supports updates across an entire PO rather than requiring every production line to be handled independently.

Relevant objects include:

- whole_po_production_sessions
- fn_whole_po_partial_update

This is useful when a department updates a larger portion of an order at once.

## AI Factory Analysis Integration

Production data is exposed to the AI Factory Copilot through read tools and analysis views.

The AI can use production information to answer questions such as:

- What is the current production state of this PO?
- Which department is behind?
- What work is currently pending?
- Which orders are at risk?
- Where are production bottlenecks?
- Which schedules have been changed?
- What is the likely next operational priority?

## Production Analytics

Production data contributes to management views such as:

- vw_ai_factory_analysis_po
- vw_ai_factory_analysis_department
- vw_factory_daily_factory_summary
- vw_master_department_progress
- vw_master_department_updates

These views support operational summaries and AI-assisted analysis.

## Safety and Validation

Production mutations should not be performed directly from free-form AI output.

The intended architecture is:

1. AI interprets the user request.
2. Current production state is read.
3. Structured parameters are prepared.
4. Validation is performed.
5. If the action is AI-initiated, confirmation is required where applicable.
6. Backend logic executes deterministically.
7. Resulting production state is available for verification.

## Related n8n Workflows

Sanitized workflow exports include:

- n8n/production/production-update.json
- n8n/production/production-progress.json
- n8n/main/ai-factory-copilot.json
- n8n/helpers/factory-tool-gateway.json

## Design Goal

The production subsystem is designed to provide a reliable operational record rather than just a messaging layer.

Factory progress should remain:

- structured
- auditable
- queryable
- analyzable
- reusable by downstream workflows
- accessible to the AI Copilot
