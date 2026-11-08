# Known Limitations and Future Improvements

## Current Limitations

### Public Workflow Exports

The n8n workflows in this repository are sanitized reference exports.

Credentials and production-specific identifiers have been removed.

They require configuration before they can run in another environment.

### Google Sheets Configuration

The public workflow definitions contain placeholder Google file identifiers.

A deployment must provide its own Google configuration and credentials.

### AI Mutation Audit Results

Some historical mutation executions returned different result_json structures depending on the backend operation.

A future improvement is to standardize mutation result payloads.

### Generic SQL Mutations

Broad generic SQL mutation capability is powerful but should remain tightly confirmation-gated.

Where practical, important business operations should use dedicated deterministic backend functions.

### Batch Atomicity

Multi-row business changes should be executed as a single atomic database operation whenever possible.

Future development should continue replacing multi-step mutation patterns with atomic SQL or dedicated backend functions.

### Child Workflow User Interface

Some historical child workflows contained their own Telegram response nodes.

The long-term architecture should keep user-facing interaction centralized in the unified Copilot.

### Automated Regression Testing

The project currently uses direct verification scripts and controlled end-to-end testing.

A future improvement is a larger automated regression suite covering:

- PO intake
- SKU mapping
- production
- inventory
- wages
- QC
- dispatch
- reporting
- Google synchronization

## Future Improvements

Potential future work includes:

- deeper inventory forecasting
- supplier performance analysis
- automatic procurement recommendations
- production capacity forecasting
- anomaly detection
- cost and margin analysis
- standardized AI mutation result envelopes
- broader automated regression testing
- dashboard interfaces
- improved observability and alerting
