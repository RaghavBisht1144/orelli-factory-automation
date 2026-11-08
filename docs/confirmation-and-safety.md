# Confirmation and Safety Model

## Purpose

The AI layer is designed to provide flexible natural-language reasoning without allowing unrestricted direct modification of factory data.

The core principle is:

AI decides what should be done.

Deterministic backend logic performs the actual change.

## Read vs Write Separation

### Reads

Read operations can execute immediately.

Examples:

- PO status
- SKU mapping status
- Inventory analysis
- Wage summaries
- Production analysis
- Factory reports

### Writes

Business mutations require explicit confirmation.

Examples:

- SKU mapping
- Production updates
- Material purchases
- Wage entries
- Wage payments
- Worker advances
- QC updates
- Dispatch
- Factory expenses

## Mutation Lifecycle

A mutation follows this process:

1. User gives a natural-language instruction.
2. AI determines the intended operation.
3. AI reads the current state.
4. AI constructs the exact proposed change.
5. The proposed action is stored.
6. A unique confirmation token is generated.
7. User confirms or cancels.
8. Only the stored action is executed.
9. The result is audited.

## Confirmation Commands

The user can reply:

CONFIRM <token>

or:

CANCEL <token>

The token identifies the exact stored proposal.

## Why the Action Is Stored

The system does not regenerate the mutation after confirmation.

Instead, the previously reviewed proposal is stored before confirmation.

This helps ensure that:

- the confirmed action matches the displayed proposal
- AI reasoning does not silently change between proposal and execution
- confirmation applies to one exact operation

## Identity Guard

Confirmation handling can use Telegram identity information such as:

- chat identity
- user identity
- message context

This helps ensure that the pending action is executed in the intended conversation context.

## Pending Actions

Pending AI mutations are stored in:

ai_copilot_pending_actions

A pending action can contain:

- token
- target system
- action summary
- execution payload
- status
- timestamps
- result data
- error information

## Undo Safety

Undo operations are not automatically executed.

Undo follows the same safety model:

Previous executed action
-> current state inspection
-> inverse proposal
-> new confirmation token
-> explicit confirmation
-> inverse execution
-> audit

## Audit Preservation

Undo does not delete the original audit record.

Both the original action and the undo remain recorded.

This preserves the history of what happened.

## Runtime Database Safety

Runtime n8n workflows should avoid schema-changing operations.

Database schema changes should happen during deployment using the appropriate database administrative role.

Normal runtime access should be limited to required:

- SELECT
- INSERT
- UPDATE
- DELETE
- sequence usage

## SQL Safety

The generic SQL read tool is restricted to read operations.

The AI should not use the read tool for arbitrary mutations.

Unknown schema names should be discovered rather than guessed.

## Public Repository Safety

Public workflow exports are sanitized.

The repository intentionally excludes:

- API keys
- bot tokens
- passwords
- credential IDs
- private Google file IDs
- service-account private keys
- customer data
- worker data
- supplier-sensitive data
- production database dumps

## Design Goal

The system is designed to combine:

- natural-language flexibility
- deterministic execution
- explicit human approval
- auditability
- reversibility
- minimal operational risk
