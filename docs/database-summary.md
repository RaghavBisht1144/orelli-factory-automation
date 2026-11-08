# Database Overview

The ORELLI factory automation backend uses PostgreSQL as the authoritative operational database.

## Current Database Scale

- 107 application tables
- 32 views
- 50 PostgreSQL functions
- 62 sequences

## Major Functional Areas

The schema supports:

- Purchase order intake and line-item management
- SKU master data and SKU mapping
- BOM management
- Inventory and material procurement
- Supplier purchasing and receipts
- Production planning and scheduling
- Department-level production tracking
- Worker production tracking
- Piece-rate wage calculation
- Worker advances and wage payments
- Factory expenses
- Quality control
- Packing and dispatch
- Grindery/material-cycle tracking
- Google Sheets synchronization
- Telegram workflow sessions
- AI factory actions
- AI Copilot conversation context
- Confirmation-gated AI mutations
- Factory analytics and reporting

## Analytics Views

The system contains operational and management views covering:

- Purchase-order analysis
- Department performance
- Inventory analysis
- Production schedules
- Worker wage balances
- Worker advances
- Wage payment balances
- Grindery consumption
- Factory summaries
- PO history

## Database Logic

Business logic is implemented using PostgreSQL functions for operations including:

- AI production updates
- AI production batches
- Material purchases
- QC
- Dispatch
- Wage entry preparation
- Wage payments
- Worker advances
- Factory batches
- Compensation
- Expenses
- Production planning
- Daily worker production

The complete schema definition is stored in:

`database/schema/footwear_factory_schema.sql`

The generated object inventory is stored in:

`docs/database-object-inventory.txt`
