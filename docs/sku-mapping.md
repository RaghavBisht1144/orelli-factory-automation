# SKU Mapping

## Purpose

The SKU mapping system connects customer-facing purchase-order line items to the factory's internal SKU master.

This is necessary when incoming customer material codes, article numbers, colors, or product descriptions do not directly match the factory's internal SKU naming.

## Main Database Objects

The SKU mapping flow primarily uses:

- purchase_orders
- purchase_order_lines
- sku_mapping_requests
- shoe_skus

## SKU Master

The internal SKU master is stored in:

shoe_skus

Important fields include:

- id
- sku_code
- style
- product
- is_active
- created_at

## Mapping Request Lifecycle

When a purchase-order line does not yet have an internal SKU:

- purchase_order_lines.sku remains NULL
- a sku_mapping_requests row is created
- the request status is OPEN

Once a mapping is confirmed:

- purchase_order_lines.sku is populated
- sku_mapping_requests.status becomes RESOLVED
- resolved_sku_id is stored
- resolved_at is populated

## AI-Assisted Matching

The AI Factory Copilot can inspect:

- current PO lines
- customer material codes
- colors
- descriptions
- existing active SKUs

It can then recommend the best existing SKU match without changing the database.

Example:

092 Brown
-> TF092 BROWN

092 Black
-> TF092 BLACK

255 Brown
-> LP255 BROWN

255 Black
-> LP255 BLACK

## Read-Only Recommendation Flow

User request
-> AI identifies current PO
-> AI reads unmapped lines
-> AI reads shoe_skus
-> AI compares article/material family and color
-> AI returns recommended matches
-> No database mutation occurs

## Single-Line Mapping

For a single mapping request:

1. The AI reads the current PO line.
2. The AI verifies the target SKU exists.
3. The AI prepares the exact mutation.
4. A confirmation token is generated.
5. The user sends CONFIRM <token>.
6. The mapping executes.
7. The action is audited.

## Batch Mapping

The Copilot also supports multiple mappings in one confirmed action.

Example batch:

- Line 1: NULL -> TF092 BROWN
- Line 2: NULL -> TF092 BLACK
- Line 3: NULL -> LP255 BROWN
- Line 4: NULL -> LP255 BLACK

The intended batch behavior is:

- all target purchase-order lines are updated
- all target mapping requests become RESOLVED
- resolved SKU references are stored
- timestamps are recorded
- master SKUs are not modified
- unrelated PO lines remain unchanged

## Confirmation-Gated Mutation

A mapping request does not execute immediately from natural-language input.

The AI first produces a proposal.

Example:

Confirmation required

Line 1:
NULL -> TF092 BROWN

Reply:
CONFIRM <token>

or

CANCEL <token>

Only the confirmation message executes the stored mutation.

## Undo

The AI Copilot can reverse an executed mapping using natural language.

Example:

Undo the batch SKU mapping I just did.

The Copilot then:

1. Locates the previous executed action.
2. Reads the current PO and mapping-request state.
3. Reconstructs the inverse mutation.
4. Generates a new confirmation token.
5. Waits for explicit confirmation.
6. Executes the inverse mutation.
7. Preserves the original audit history.

## Verified Single-Line Undo

The single-line test verified:

- mapped SKU changed back to NULL
- mapping request changed RESOLVED -> OPEN
- resolved_sku_id was cleared
- resolved_at was cleared
- master SKU remained intact
- original executed action remained in audit history
- undo action was separately audited

## Verified Four-Line Batch Mapping

A synthetic PO was used to test four SKU mappings together.

Mappings:

- 092 Brown -> TF092 BROWN
- 092 Black -> TF092 BLACK
- 255 Brown -> LP255 BROWN
- 255 Black -> LP255 BLACK

PostgreSQL verification confirmed:

- 4 product lines mapped
- 4 mapping requests resolved
- 4 resolved SKU IDs existed in the master
- confirmation token executed successfully

## Verified Four-Line Batch Undo

The same batch was reversed through natural language.

PostgreSQL verification confirmed:

- 4 product lines returned to NULL SKU
- 4 mapping requests returned to OPEN
- 4 resolved_sku_id values were cleared
- 4 resolved_at timestamps were cleared
- all 4 master SKU rows remained present
- undo action was marked EXECUTED

Final verification result:

PASS

## SQL Schema Discovery

During testing, the AI initially assumed a non-existent table named sku_master.

The live schema instead used:

public.shoe_skus

To prevent this class of failure, the SQL read tool was updated so the AI:

- does not guess unknown relation names
- uses information_schema or pg_catalog
- treats SQL schema errors as recoverable
- corrects the query and retries

## Safety Properties

The SKU mapping system is designed so that:

- read-only recommendations do not mutate data
- writes require confirmation
- undo also requires confirmation
- master SKU records are preserved unless explicitly targeted
- executed actions remain auditable
- unrelated PO lines are not modified

## Related Workflows

Relevant sanitized workflow exports include:

- n8n/main/ai-factory-copilot.json
- n8n/helpers/sql-read-tool.json
- n8n/helpers/change-proposal-tool.json
- n8n/po/po-intake.json
