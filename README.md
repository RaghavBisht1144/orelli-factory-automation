# ORELLI Factory Automation

AI-powered end-to-end factory operations automation platform built for footwear manufacturing.

The system combines PostgreSQL, n8n, Docker, Telegram, Google Workspace integrations, PDF processing, and an LLM-powered Factory Copilot to automate and analyze core manufacturing operations.

## What It Automates

- Purchase-order PDF ingestion
- Structured PO extraction and validation
- SKU mapping
- BOM management
- Inventory tracking
- Procurement requirements
- Supplier purchasing and receipts
- Production progress
- Production scheduling
- Department-level tracking
- Worker production
- Piece-rate wages
- Worker advances
- Wage payments
- Factory expenses
- Quality control
- Dispatch
- Grindery operations
- Factory reporting
- Management analytics

## AI Factory Copilot

A Telegram-based AI Copilot provides a natural-language interface to factory operations.

The Copilot can:

- analyze purchase orders
- inspect production progress
- identify inventory risks
- inspect wages and worker balances
- compare operational data across factory domains
- recommend SKU mappings
- query PostgreSQL
- query synchronized Google Sheets data
- prepare controlled business actions
- reverse previous actions using natural language

## Confirmation-Gated AI Actions

AI-generated business mutations do not execute immediately.

The system follows this model:

1. User provides a natural-language request.
2. AI understands the intended operation.
3. Current factory state is inspected.
4. AI generates the exact proposed change.
5. A unique confirmation token is created.
6. User explicitly confirms or cancels.
7. Only the stored confirmed operation executes.
8. Execution remains auditable.

Undo operations follow the same confirmation process.

## Engineering Scale

Current PostgreSQL implementation:

- 107 application tables
- 32 database views
- 50 PostgreSQL functions
- 62 sequences

Automation layer:

- 13 major n8n workflows
- AI reasoning tools
- PostgreSQL read tooling
- Google Sheets tooling
- confirmation-gated mutation system
- persistent conversation context
- executed-action context
- natural-language rollback

## Technology Stack

- PostgreSQL
- n8n
- Docker
- Docker Compose
- Linux
- JavaScript
- SQL
- Bash
- Telegram Bot API
- Google Sheets API
- Google Drive API
- Google Slides API
- LLM APIs
- PDF processing
- Git / GitHub

## Example Verified Workflow

A synthetic four-line purchase order was used for end-to-end SKU mapping verification.

The AI identified four existing SKU matches, proposed them as one batch operation, waited for confirmation, executed the mappings, and the resulting PostgreSQL state was independently verified.

The user then requested the batch to be undone using natural language.

The AI identified the previous executed action, reconstructed the inverse operation, generated a new confirmation token, executed the rollback after approval, and PostgreSQL verification confirmed that all four lines returned to their original state while master SKU records remained intact.

## Architecture

PostgreSQL acts as the authoritative operational database.

n8n handles workflow orchestration.

Telegram provides the conversational interface.

Google Sheets provides synchronized management and operational views.

The AI layer performs reasoning and tool selection, while deterministic backend logic performs confirmed business mutations.

See:

- docs/system-architecture.md
- docs/ai-copilot.md
- docs/confirmation-and-safety.md

## Engineering Challenges Solved

Development included work on:

- production-safe workflow orchestration
- database ownership and runtime permission issues
- confirmation-token persistence
- multi-turn AI context
- natural-language undo
- SQL schema discovery
- recoverable AI SQL errors
- batch mutation verification
- PostgreSQL-first business state
- synchronization between database and spreadsheet interfaces

See:

docs/engineering-notes.md

## Testing

Important business mutations are verified directly against PostgreSQL rather than relying only on successful workflow messages.

Testing includes:

- single SKU mapping
- single SKU undo
- four-line batch mapping
- four-line batch undo
- audit verification
- master-record preservation
- schema-error recovery

See:

docs/testing-and-validation.md

## Security and Privacy

This repository is a portfolio showcase.

The live production implementation, credentials, client data, full workflow exports, complete database schema, and production infrastructure are intentionally not published.

This repository does not provide access to the running factory automation system.

## Repository Contents

- README.md — project overview
- DEVELOPMENT-HISTORY.md — development phases
- docs/ — architecture and engineering documentation
- examples/ — sanitized demonstrations
- screenshots/ — selected sanitized project screenshots

## Production Implementation

The full production-derived technical repository is maintained privately.

This public repository intentionally focuses on architecture, engineering decisions, verified behavior, and project outcomes rather than publishing the complete deployable implementation.
