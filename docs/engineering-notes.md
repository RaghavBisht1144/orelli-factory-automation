# Engineering Notes and Major Fixes

## Purpose

This document records important engineering problems encountered during development and how they were resolved.

The goal is to preserve the reasoning behind architectural decisions, not just the final implementation.

## 1. Runtime DDL Ownership Failure

### Problem

A runtime n8n workflow attempted schema-changing operations on AI Copilot control tables.

The PostgreSQL runtime role did not own those tables.

This caused errors such as:

must be owner of table

### Root Cause

DDL and ALTER operations were being executed during normal Telegram interactions.

Runtime application roles should not be responsible for schema ownership or migrations.

### Fix

- Moved table creation and schema changes to deployment time
- Applied schema changes using the PostgreSQL administrative role
- Granted the runtime n8n role only the permissions required for normal operation
- Replaced runtime DDL with read-only readiness checks

### Result

Normal AI interactions no longer attempt schema modifications.

---

## 2. Confirmation Token Lookup Failure

### Problem

A valid confirmation token was generated, but CONFIRM could not locate the pending action.

### Root Cause

Telegram identity metadata was not being persisted and matched consistently.

### Fix

The proposal flow was updated to store:

- Telegram chat identity
- user identity
- message metadata
- exact token

Confirmation lookup was changed to use the exact unique token with identity validation.

### Result

Confirmed mutations could be reliably located and executed.

---

## 3. Undo Routed to Status Instead of Mutation Logic

### Problem

A request such as:

Undo the SKU mapping I just did.

was interpreted by an older deterministic SKU-status path.

Instead of proposing an inverse mutation, the system returned the current mapping status.

### Root Cause

A legacy shortcut intercepted the request before it reached the AI reasoning layer.

### Fix

- Removed the old SKU-status shortcut from the active natural-language route
- Added recent executed-action context to the AI
- Classified undo, revert, rollback, and unmap as mutation intent
- Required a new confirmation token for the inverse action

### Result

The AI successfully reconstructed and executed both single-line and batch SKU undo operations.

---

## 4. SQL Schema Hallucination

### Problem

During SKU recommendation, the AI attempted to query:

sku_master

That table did not exist.

The actual master table was:

public.shoe_skus

### Root Cause

The AI guessed a conventional table name instead of inspecting the live schema.

### Fix

The SQL read system was updated with hard rules:

- do not guess unknown table names
- inspect information_schema when uncertain
- inspect pg_catalog when needed
- return SQL errors as recoverable tool results
- allow the AI to correct the query and retry

The current SKU relation was also established through foreign-key inspection.

### Result

The same natural-language SKU recommendation request succeeded after schema discovery.

---

## 5. PostgreSQL Placeholder Expanded by Bash

### Problem

A verification script failed with:

$1: unbound variable

### Root Cause

A SQL heredoc was not quoted.

Bash attempted to interpret PostgreSQL's $1 placeholder as a shell positional argument.

### Fix

Changed:

<<SQL

to:

<<'SQL'

This prevented shell expansion inside the SQL block.

### Result

PostgreSQL placeholders reached the database correctly.

---

## 6. Verification Query Used Wrong SKU Column

### Problem

A verification query assumed:

shoe_skus.sku

The actual column was:

shoe_skus.sku_code

### Root Cause

The verification query guessed the display column instead of inspecting the schema.

### Fix

Used:

information_schema.columns

to inspect the actual table definition.

The confirmed schema included:

- id
- sku_code
- style
- product
- is_active
- created_at

### Result

Verification was corrected and the batch undo passed.

---

## 7. False Negative in Batch Mapping Verification

### Problem

A four-line SKU mapping executed successfully, but the final verifier returned:

CHECK DETAILS

### Root Cause

The verification script expected result_json to contain:

{"success": true}

The batch action instead stored a structured result object for one resolved mapping while the audit row itself was correctly marked EXECUTED.

### Fix

Verification was adjusted to rely on:

- actual PostgreSQL line state
- mapping-request state
- master SKU references
- audit status
- executed_at
- error state

rather than assuming one fixed result_json shape.

### Result

The batch mapping was correctly recognized as successful.

---

## 8. AI Conversation Context

### Problem

Follow-up commands depended on previous actions, current PO state, and recent reasoning.

Without persistent context, requests such as:

Undo what I just did.

could be ambiguous.

### Fix

Added persistent Copilot context structures for:

- conversation turns
- current PO context
- recent executed actions

### Result

The AI can reason across multi-turn factory interactions without requiring the user to repeat every identifier.

---

## 9. Deterministic Execution After AI Reasoning

### Problem

Allowing an LLM to directly perform arbitrary writes would create unnecessary operational risk.

### Architectural Decision

The system separates:

AI reasoning

from:

deterministic execution

### Flow

Natural-language request
-> AI understands intent
-> AI reads current state
-> AI prepares exact mutation
-> pending action stored
-> user confirms
-> deterministic backend executes
-> audit record stored

### Result

The system retains natural-language flexibility while keeping write execution controlled.

---

## 10. PostgreSQL as Authoritative Business State

### Problem

Operational data is also synchronized with Google Sheets and exposed through n8n workflows.

Using multiple systems as equal sources of truth could create reconciliation problems.

### Architectural Decision

PostgreSQL is treated as the authoritative business database.

Google Sheets is used for:

- synchronized operational visibility
- reporting
- management interfaces

n8n is used for orchestration.

### Result

Business truth remains centralized and queryable.

---

## Engineering Principles Derived from These Fixes

The project now follows these rules:

1. Never run recurring DDL from normal runtime workflows.
2. Never guess unknown database schema names.
3. Verify important mutations directly in PostgreSQL.
4. Keep AI reasoning separate from deterministic execution.
5. Require confirmation for business mutations.
6. Preserve audit records when reversing actions.
7. Prefer narrow fixes over broad redesigns.
8. Use synthetic data for end-to-end testing.
9. Keep production secrets outside public repositories.
10. Treat PostgreSQL as the authoritative business state.
