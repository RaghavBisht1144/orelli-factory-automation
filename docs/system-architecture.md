# System Architecture

## Overview

ORELLI Footwear Factory Automation uses PostgreSQL as the authoritative operational database and n8n as the orchestration layer.

Telegram acts as the primary conversational interface for the AI Factory Copilot.

Google Sheets is used for synchronized operational views, master data, and selected reporting workflows.

## Core Components

### PostgreSQL

PostgreSQL stores the authoritative factory data for:

- Purchase orders
- Purchase order lines
- SKU master
- SKU mapping
- BOM
- Inventory
- Procurement
- Suppliers
- Production
- Production schedules
- Worker production
- Wages
- Worker advances
- Wage payments
- Factory expenses
- Quality control
- Dispatch
- Grindery operations
- AI action history
- Factory analytics

### n8n

n8n handles:

- Workflow orchestration
- Telegram interactions
- Purchase-order PDF processing
- AI tool execution
- Database reads and controlled writes
- Google Sheets synchronization
- Production workflows
- Wage workflows
- PDF/report generation
- Factory analytics
- Confirmation-gated AI mutations

### AI Factory Copilot

The Copilot provides:

- Natural-language factory queries
- Purchase-order analysis
- Production analysis
- Inventory analysis
- Wage analysis
- Cross-domain factory analysis
- PostgreSQL reads
- Google Sheets reads
- Confirmation-gated database changes
- Natural-language undo

## Main Data Flow

User request
-> Telegram
-> AI Factory Copilot
-> Read current factory state
-> Select appropriate tool
-> PostgreSQL or Google Sheets
-> AI response

For mutations:

User request
-> AI reads current state
-> AI generates exact proposed change
-> Confirmation token generated
-> User confirms
-> Deterministic execution
-> Database update
-> Audit record

## Purchase Order Flow

PO PDF
-> n8n PO Intake
-> PDF extraction
-> AI-assisted parsing
-> Validation
-> Purchase order stored in PostgreSQL
-> Purchase order lines stored
-> SKU mapping requests generated where required
-> Production workflow

## Production Flow

Purchase order
-> Production planning
-> Production stages
-> Department updates
-> Production progress
-> Batch recording
-> QC
-> Dispatch

## Inventory and BOM Flow

SKU
-> SKU BOM
-> Material requirements
-> Inventory comparison
-> Procurement requirements
-> Supplier purchasing
-> Material receipt
-> Inventory update

## Wage Flow

Worker production
-> Production entry
-> Piece-rate calculation
-> Worker allocation
-> Compensation entry
-> Advance adjustment
-> Wage payment
-> Wage balance
-> Wage report

## AI Read Architecture

The AI uses separate tools for different read operations:

- Factory Read Gateway
- PostgreSQL SQL Read Tool
- Google Sheets Read Tool

The SQL Read Tool is restricted to read operations.

## AI Mutation Architecture

The AI does not directly execute normal natural-language write requests.

Instead:

1. The AI identifies the requested mutation.
2. Current database state is inspected.
3. The AI prepares the exact proposed change.
4. A pending action is stored.
5. A confirmation token is returned.
6. The user must explicitly confirm.
7. The stored action is executed.
8. The result is audited.

## Undo Architecture

Undo follows the same confirmation model.

The Copilot:

1. Finds the previous executed action.
2. Reads the current database state.
3. Constructs the inverse operation.
4. Generates a new confirmation token.
5. Executes only after confirmation.
6. Preserves the original audit record.

## SQL Schema Discovery

The AI is instructed not to guess unknown table or column names.

When necessary, it queries:

- information_schema
- pg_catalog

If a read query fails because of an incorrect schema assumption, the error is returned to the AI as recoverable context.

The AI can then inspect the real schema, correct the query, and retry.

## Design Principles

1. PostgreSQL is the authoritative operational database.
2. n8n handles orchestration, not long-term business truth.
3. Read operations are separated from write operations.
4. AI reasoning determines intent and selects tools.
5. Database mutations require explicit confirmation.
6. Execution after confirmation is deterministic.
7. Executed actions remain auditable.
8. Undo operations are also confirmation-gated.
9. Runtime workflows avoid database DDL.
10. Schema discovery is preferred over guessed SQL identifiers.
11. Production credentials are excluded from public workflow exports.
