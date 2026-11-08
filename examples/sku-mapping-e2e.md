# AI SKU Mapping — End-to-End Example

This example uses synthetic test data.

## Initial State

Four purchase-order lines required internal SKU mappings:

- Article 092 — Brown
- Article 092 — Black
- Article 255 — Brown
- Article 255 — Black

All four mapping requests were OPEN.

## AI Recommendation

The Factory Copilot inspected the existing SKU master and identified:

- 092 Brown -> TF092 BROWN
- 092 Black -> TF092 BLACK
- 255 Brown -> LP255 BROWN
- 255 Black -> LP255 BLACK

This recommendation was read-only.

## Mutation Proposal

The user then requested all four recommended mappings.

The AI generated one confirmation-gated batch proposal.

No database changes occurred until the user explicitly confirmed the generated token.

## Execution Verification

After confirmation, PostgreSQL verification confirmed:

- 4 product lines correctly mapped
- 4 mapping requests changed to RESOLVED
- all resolved SKU references pointed to existing master SKU records
- unrelated records remained unchanged

## Natural-Language Undo

The user then requested:

"Undo the batch SKU mapping I just did."

The AI:

1. identified the previous executed batch
2. inspected the current database state
3. reconstructed all four inverse changes
4. generated a new confirmation token
5. waited for approval
6. executed the rollback

## Final Verification

PostgreSQL confirmed:

- all 4 lines returned to an unmapped state
- all 4 requests returned to OPEN
- resolved SKU references were cleared
- resolution timestamps were cleared
- all master SKUs remained present
- the undo action was separately audited

Final verification result:

PASS
