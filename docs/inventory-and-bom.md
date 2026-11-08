# Inventory and BOM

## Purpose

The inventory and BOM subsystem connects finished-product SKUs to their material requirements and provides the operational foundation for procurement, purchasing, material receipts, and inventory analysis.

It allows the factory to answer questions such as:

- What materials are required for a SKU?
- What materials are required for an active PO?
- What is currently available in inventory?
- What needs to be purchased?
- Which suppliers can provide a material?
- Which material receipts have been recorded?
- Which inventory shortages may affect production?

## Main Database Objects

Important inventory, BOM, and procurement objects include:

- materials
- inventory
- sku_bom
- procurement_requirements
- suppliers
- supplier_materials
- supplier_purchase_orders
- supplier_purchase_order_lines
- supplier_material_receipts

Additional synchronized source structures include:

- client_sheet_bom_source
- client_sheet_inventory_source
- client_sheet_procurement_source

## SKU BOM

The SKU BOM defines the relationship between an internal SKU and the materials required to manufacture it.

Conceptually:

SKU
-> required materials
-> required quantity per unit
-> total requirement based on production quantity

This allows purchase-order quantities to be translated into material requirements.

## Material Master

The materials table acts as the structured material reference used by inventory and procurement workflows.

Material records can then be referenced by:

- BOM definitions
- inventory
- procurement requirements
- suppliers
- purchases
- receipts

## Inventory

The inventory table represents material availability used by the operational system.

Inventory information can be compared against calculated requirements to identify shortages.

Conceptually:

Required material quantity
- Available inventory
= Procurement requirement

## Procurement Requirements

The procurement_requirements structure stores or represents materials that need to be sourced for production.

This allows material planning to move from:

PO demand
-> BOM demand
-> current inventory
-> procurement requirement

## Supplier Management

The supplier subsystem includes:

- suppliers
- supplier_materials

This allows the system to associate suppliers with the materials they can provide.

The structure supports future or current logic for:

- supplier selection
- sourcing analysis
- purchase planning
- supplier-specific material records

## Supplier Purchase Orders

Supplier purchasing is represented using:

- supplier_purchase_orders
- supplier_purchase_order_lines

This provides structured records for purchases placed with suppliers.

A supplier purchase order can contain multiple material lines.

## Material Receipts

Material received from suppliers is tracked using:

- supplier_material_receipts

This enables the system to record incoming materials and connect purchasing activity with operational inventory.

## AI Material Purchase Recording

The database includes:

- fn_ai_material_purchase_record

This function supports structured material-purchase recording through the AI/backend automation layer.

A natural-language instruction can therefore be converted into structured purchase information before controlled execution.

Example intent:

Record a material purchase made today from a supplier.

The AI layer can extract details such as:

- material
- supplier
- quantity
- total amount
- unit cost
- date

The backend then handles structured validation and recording.

## AI Inventory Analysis

Inventory information is exposed to the Factory Copilot through:

- vw_ai_factory_analysis_inventory
- PostgreSQL read tools
- factory read tools

The Copilot can use this data for questions such as:

- Which materials are currently at risk?
- What materials need to be purchased?
- Which POs may face material shortages?
- What inventory is available for a particular SKU?
- Which requirements are not covered by current stock?

## BOM and PO Integration

The intended material-planning flow is:

Purchase order
-> Internal SKU
-> SKU BOM
-> Material requirements
-> Available inventory
-> Procurement requirement
-> Supplier purchase
-> Material receipt
-> Updated inventory state

This connects PO intake directly with material planning.

## Google Sheets Integration

BOM, inventory, and procurement data may also be synchronized with Google Sheets for operational visibility.

Relevant synchronized structures include:

- client_sheet_bom_source
- client_sheet_inventory_source
- client_sheet_procurement_source

The n8n Master Data Sync workflow handles synchronization between PostgreSQL-backed operational data and configured Google Sheets views.

## AI Safety Model

Inventory reads and analysis can happen immediately.

Inventory-changing actions should follow controlled execution.

For AI-driven mutations:

1. User provides a natural-language instruction.
2. AI identifies the intended operation.
3. Current material/inventory state is inspected where required.
4. Structured parameters are prepared.
5. Exact proposed changes are generated.
6. Confirmation is required for mutations.
7. Backend logic performs the update.
8. The resulting state remains queryable and auditable.

## Data Quality

Accurate inventory planning depends on maintaining:

- correct SKU mappings
- correct BOM definitions
- valid material records
- reliable inventory quantities
- supplier-material relationships
- accurate purchase and receipt records

Missing or incomplete BOM information can prevent reliable procurement calculations.

## Related n8n Workflows

Relevant sanitized workflow exports include:

- n8n/main/ai-factory-copilot.json
- n8n/helpers/factory-tool-gateway.json
- n8n/helpers/factory-read-gateway.json
- n8n/helpers/sql-read-tool.json
- n8n/sync/master-data-sync.json

## Design Goal

The inventory and BOM subsystem is intended to turn production demand into actionable material planning.

The long-term operational chain is:

Customer demand
-> PO
-> SKU
-> BOM
-> Material requirement
-> Inventory check
-> Procurement
-> Supplier purchase
-> Receipt
-> Production availability
