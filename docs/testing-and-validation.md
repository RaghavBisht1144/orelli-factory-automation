# Testing and Validation

## Purpose

The project uses direct PostgreSQL verification and controlled end-to-end tests to confirm that workflows do not only return successful messages, but actually produce the expected database state.

Testing is designed around:

- read-only verification queries
- controlled synthetic test data
- confirmation-token execution
- audit checks
- rollback / undo validation
- preservation of unrelated records

## Testing Principles

1. PostgreSQL is treated as the final source of truth.
2. Workflow success messages are not accepted as sufficient proof by themselves.
3. Important mutations are verified directly in the database.
4. Synthetic test purchase orders are used where possible.
5. Tests check both intended changes and unintended side effects.
6. Undo operations are verified independently.
7. Master records should remain preserved unless explicitly targeted.

## SKU Mapping Test

A synthetic purchase order was used with four unmapped product lines:

- 092 Brown
- 092 Black
- 255 Brown
- 255 Black

The AI Copilot inspected the SKU master and recommended:

- 092 Brown -> TF092 BROWN
- 092 Black -> TF092 BLACK
- 255 Brown -> LP255 BROWN
- 255 Black -> LP255 BLACK

No changes were made during the recommendation phase.

## Single-Line Mapping Test

A single mapping was proposed and executed through a confirmation token.

The test verified:

- purchase_order_lines.sku was updated
- the corresponding mapping request became RESOLVED
- resolved_sku_id was populated
- unrelated PO lines remained unchanged
- unrelated mapping requests remained unchanged
- the action was recorded in the AI pending-action audit table

## Single-Line Undo Test

The user requested:

Undo the SKU mapping I just did.

The AI:

1. Identified the previous executed action.
2. Read the current database state.
3. Constructed the inverse change.
4. Generated a new confirmation token.
5. Executed only after confirmation.

PostgreSQL verification confirmed:

- target SKU returned to NULL
- mapping request returned to OPEN
- resolved_sku_id was cleared
- resolved_at was cleared
- master SKU remained present
- original action remained audited
- undo action was separately audited

Final verification:

PASS

## Four-Line Batch Mapping Test

The AI proposed a single batch action for all four open mapping requests.

Expected mappings:

- Line 1 -> TF092 BROWN
- Line 2 -> TF092 BLACK
- Line 3 -> LP255 BROWN
- Line 4 -> LP255 BLACK

After confirmation, direct PostgreSQL verification confirmed:

- 4 product lines were mapped correctly
- 4 mapping requests became RESOLVED
- 4 resolved_sku_id values referenced valid master SKU rows
- no additional product lines were changed

## Four-Line Batch Undo Test

The user then requested a natural-language batch undo.

The AI generated an inverse proposal for all four lines.

After confirmation, PostgreSQL verification confirmed:

- 4 product-line SKU values returned to NULL
- 4 mapping requests returned to OPEN
- 4 resolved_sku_id values were cleared
- 4 resolved_at values were cleared
- all 4 master SKU rows remained present
- the undo action was marked EXECUTED

Final verification result:

PASS

## SQL Schema Recovery Test

During SKU recommendation testing, the AI initially attempted to query a non-existent relation:

sku_master

The actual live table was:

public.shoe_skus

The SQL read tool was updated so that:

- SQL execution errors are returned as recoverable context
- the AI can inspect information_schema
- the AI can inspect pg_catalog
- guessed schema names are avoided
- the corrected query can be retried

After the patch, the same natural-language SKU recommendation request succeeded.

## Confirmation Token Test

The confirmation system was tested for both single and batch mutations.

The system verifies:

- the token exists
- the token matches the stored pending action
- the action has not already been executed
- the requesting identity is valid where required
- the exact stored mutation is executed

## Audit Validation

Relevant AI actions are stored in:

ai_copilot_pending_actions

Audit records can include:

- token
- target system
- summary
- status
- created_at
- confirmed_at
- executed_at
- result_json
- error_text

This provides a traceable history of AI-proposed and executed mutations.

## Runtime DDL Validation

A previous failure occurred when the runtime n8n database role attempted schema-changing operations on control tables.

The architecture was corrected so that:

- schema changes occur during deployment
- runtime workflows perform reads and DML only
- the runtime application role receives only required permissions

This avoids database ownership failures during normal Telegram interactions.

## Test Strategy Going Forward

Additional tests should continue to cover:

- PO intake
- BOM processing
- material purchases
- production updates
- production batches
- wages
- advances
- payments
- QC
- dispatch
- expense recording
- Google Sheets synchronization
- report generation
- AI analysis

For each mutation test, verification should include:

1. target rows changed correctly
2. unrelated rows unchanged
3. audit record created
4. resulting state valid
5. rollback or inverse action tested where applicable

## Test Evidence

Verification scripts are stored under:

scripts/verification/

Only sanitized and reusable verification scripts should be committed to the public repository.
