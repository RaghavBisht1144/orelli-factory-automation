# Development History

## Phase 1 — Infrastructure

- Linux VPS environment established
- Docker Compose deployment created
- n8n deployed
- PostgreSQL configured
- Factory database created

## Phase 2 — Core Factory Database

- Purchase order tables
- Purchase order line tables
- SKU master
- BOM structures
- Inventory structures
- Procurement structures
- Production tracking
- Quality control
- Dispatch
- Worker and wage systems
- Factory audit structures

## Phase 3 — Purchase Order Automation

- PDF purchase-order intake
- PDF extraction
- AI-assisted document parsing
- Validation
- Purchase-order creation
- Purchase-order line storage
- PO state tracking

## Phase 4 — SKU Mapping

- SKU master integration
- Mapping request generation
- Existing SKU resolution
- Mapping status tracking
- AI-assisted SKU recommendations
- Single-line mapping
- Batch SKU mapping

## Phase 5 — Production Automation

- Production progress updates
- Department-level tracking
- Production scheduling
- Production batch recording
- Production planning
- Rescheduling workflows
- Historical production state tracking

## Phase 6 — BOM and Inventory

- SKU BOM management
- Material requirements
- Procurement requirements
- Inventory tracking
- Supplier purchase orders
- Supplier purchase-order lines
- Material receipts
- Material purchase recording

## Phase 7 — Worker Production and Wages

- Worker production entries
- Piece-rate wage calculations
- Worker allocations
- Compensation entries
- Worker advances
- Wage payments
- Wage balances
- Payment grouping
- Wage audit controls

## Phase 8 — Factory Reporting

- Worker wage reports
- Weekly wage summaries
- Factory summaries
- PDF report generation
- Google Sheets integration
- Google Drive integration
- Google Slides integration

## Phase 9 — Grindery Operations

- Grindery production tracking
- Material cycles
- Material purchases
- Grindery expenses
- Material consumption
- Payment sessions
- Payment transactions
- Grindery reporting views

## Phase 10 — Master Data Synchronization

- PostgreSQL and Google Sheets synchronization
- Master-data views
- Sheet synchronization queue
- Sheet synchronization audit
- Factory management reporting tables

## Phase 11 — Factory Analytics

- Purchase-order analytics
- Department analytics
- Inventory analytics
- Factory analysis snapshot
- Management summaries
- Cross-domain operational analysis

## Phase 12 — AI Factory Copilot

- Unified Telegram interface
- LLM-based reasoning
- Factory read gateway
- PostgreSQL read tool
- Google Sheets read tool
- Natural-language factory analysis
- Multi-domain operational queries

## Phase 13 — Confirmation-Gated AI Mutations

- Change proposal workflow
- Pending-action storage
- Confirmation tokens
- Cancellation tokens
- Deterministic execution after confirmation
- Mutation audit trail

## Phase 14 — Conversation Context

- Persistent conversation history
- Current PO context
- Recent executed-action context
- Follow-up question handling
- Context-aware mutation reasoning

## Phase 15 — Natural-Language Undo

- Previous action lookup
- Current-state verification
- Inverse mutation generation
- New confirmation token for undo
- Confirmation-gated rollback
- Audit preservation after reversal

## Phase 16 — SQL Schema Discovery and Self-Healing

- information_schema discovery
- pg_catalog inspection
- Recoverable SQL read errors
- AI retry after schema mismatch
- Prevention of guessed table names
- Live schema-aware SQL generation

## Phase 17 — End-to-End Verification

Verified flows include:

- Purchase-order ingestion
- Single SKU mapping
- Single SKU undo
- Four-line batch SKU mapping
- Four-line batch undo
- Confirmation-token execution
- Mapping-request state changes
- Master SKU preservation
- PostgreSQL state verification
- Audit-record verification
