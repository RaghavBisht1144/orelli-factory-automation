# AI Factory Copilot

## Purpose

The AI Factory Copilot provides a natural-language interface for interacting with factory operations through Telegram.

Instead of requiring rigid commands, users can ask questions or request actions conversationally.

## Supported Domains

The Copilot can reason across:

- Purchase orders
- SKU mapping
- Production
- Inventory
- BOM
- Procurement
- Suppliers
- Worker production
- Wages
- Worker advances
- Factory expenses
- Quality control
- Dispatch
- Grindery operations
- Factory analytics

## Main Architecture

Telegram
-> Unified AI Copilot
-> AI reasoning
-> Select appropriate tool
-> Read factory state
-> Return answer or prepare mutation proposal

## Main Tools

### Factory Read Gateway

Provides predefined business-level reads and reports.

### PostgreSQL Read Tool

Provides read-only SQL access to the factory database.

Permitted read operations include:

- SELECT
- WITH
- EXPLAIN
- SHOW

### Google Sheets Read Tool

Allows the Copilot to inspect configured operational Google Sheets data.

### Change Proposal Tool

Used when a request would modify business data.

Instead of executing immediately, the tool stores the exact proposed action and generates a confirmation token.

## Natural-Language Reads

Read requests can execute immediately.

Examples include:

- What is the current state of this PO?
- Which SKU mappings are still open?
- What inventory risks do we have?
- What is the wage summary for this worker?
- Which department is behind?
- What happened in the factory today?

## Natural-Language Mutations

Write requests are handled differently.

Example:

Map all open lines in this PO to the recommended SKUs.

The Copilot:

1. Reads the current PO state.
2. Reads the SKU master.
3. Determines the intended mappings.
4. Constructs the exact mutation.
5. Stores the pending action.
6. Returns a confirmation token.
7. Waits for explicit confirmation.

## Confirmation

A typical response is:

Confirmation required

Reply:

CONFIRM <token>

or:

CANCEL <token>

The mutation is not executed until the correct confirmation token is received.

## Conversation Context

The Copilot maintains context to support follow-up requests.

Context can include:

- recent conversation turns
- current PO
- recent executed actions

This allows requests such as:

Undo the batch SKU mapping I just did.

without requiring the user to repeat every identifier.

## Natural-Language Undo

Undo is treated as a mutation.

The Copilot:

1. Identifies the relevant previous action.
2. Reads the current database state.
3. Reconstructs the inverse operation.
4. Creates a new pending action.
5. Generates a new confirmation token.
6. Executes only after confirmation.

## SQL Schema Discovery

The Copilot should not guess unknown table or column names.

When necessary, it can inspect:

- information_schema
- pg_catalog

If a SQL query fails because of a wrong schema assumption, the SQL tool returns the error as recoverable context.

The AI can then:

1. inspect the live schema
2. correct the query
3. retry the read
4. continue the original task

## Example SKU Reasoning

For a synthetic four-line PO, the Copilot identified:

- 092 Brown -> TF092 BROWN
- 092 Black -> TF092 BLACK
- 255 Brown -> LP255 BROWN
- 255 Black -> LP255 BLACK

It returned these recommendations without changing the database.

The mappings were later executed through a confirmation-gated batch action.

## Auditability

Executed AI actions are recorded so that the system can retain:

- token
- target system
- action summary
- status
- creation time
- confirmation time
- execution time
- result information
- error information

## Related Workflows

Sanitized workflow exports include:

- n8n/main/ai-factory-copilot.json
- n8n/helpers/factory-read-gateway.json
- n8n/helpers/sql-read-tool.json
- n8n/helpers/google-sheets-read-tool.json
- n8n/helpers/change-proposal-tool.json
- n8n/helpers/factory-tool-gateway.json
