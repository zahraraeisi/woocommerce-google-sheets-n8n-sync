# WooCommerce ↔ Google Sheets Sync with n8n

A practical n8n workflow for syncing WooCommerce products with Google Sheets and safely applying price and stock updates.

The goal of this project is simple:

> Turn a repetitive WooCommerce management task into a small operational system.

## What this project does

This repository contains two n8n workflows.

### 1. WooCommerce → Google Sheets

This workflow:

- Gets Simple Products from WooCommerce
- Syncs product data to Google Sheets
- Uses `product_id` as the unique match key
- Updates existing rows instead of creating duplicates

Synced fields:

```text
product_id
sku
product_name
regular_price
stock_quantity
````

### 2. Google Sheets → WooCommerce

This workflow:

* Reads rows with `update_status = pending`
* Validates product data
* Updates price and stock in WooCommerce
* Records the result back in Google Sheets

Possible statuses:

```text
pending
Updated
Failed
Invalid Data
```

## Google Sheet Structure

Create these columns:

```text
product_id | sku | product_name | regular_price | stock_quantity | update_status | updated_at | error_message
```

`product_id` is used as the main product identifier.

## Validation

Before sending an update to WooCommerce, the workflow checks:

* `product_id` must be greater than 0
* `regular_price` must be numeric and not negative
* `stock_quantity` must be numeric and not negative

Invalid rows are not sent to WooCommerce.

They are marked as:

```text
Invalid Data
```

## WooCommerce API Errors

If WooCommerce rejects an update:

```text
update_status = Failed
```

The WooCommerce API error message is also saved in:

```text
error_message
```

## Recommended Workflow

For this MVP, use the system in this order:

1. Run **WooCommerce Product Sync**
2. Edit price and stock in Google Sheets
3. Set `update_status` to `pending`
4. Run **WooCommerce Apply Price & Stock Changes**
5. Check the result in WooCommerce and Google Sheets

### Important MVP Limitation

Do not run the WooCommerce → Google Sheets sync after editing a product in Google Sheets but before applying the change.

Otherwise, the current WooCommerce values may overwrite the unsent changes in the sheet.

## Installation

1. Download the JSON workflows from this repository.
2. Import them into n8n.
3. Connect your own Google Sheets credentials.
4. Connect your own WooCommerce credentials.
5. Replace:

```text
YOUR_STORE_URL
YOUR_GOOGLE_SHEET_ID
YOUR_SHEET_NAME
```

6. Create the Google Sheet columns listed above.
7. Test the workflow with one product first.

## Security

The public workflows are sanitized.

They do not contain the original:

* WooCommerce credentials
* Google OAuth credentials
* credential IDs
* Google Sheet ID
* Google Sheet URL
* WooCommerce store URL
* n8n instance ID

You must configure your own credentials after importing the workflows.

## MVP Scope

Included:

* WooCommerce Simple Products
* price updates
* stock updates
* Google Sheets management
* validation
* error handling
* multiple pending rows
* duplicate-safe product synchronization

Not included:

* Variable Products
* Variations
* ERP integration
* supplier APIs
* approval workflows
* notifications
* dashboards
* multi-store management
* real-time two-way synchronization

## Future Development

This workflow can be extended much further with a custom WooCommerce plugin.

A plugin could add:

* dedicated WordPress admin interface
* user permissions
* bulk updates
* audit logs
* scheduled operations
* advanced validation
* variation support
* safer configuration
* more complex inventory workflows

This is intentionally outside the scope of the current MVP.

The current version focuses on keeping the system simple, practical, and easy to understand before adding more complexity.

## Built With

* WooCommerce
* n8n
* Google Sheets
* WooCommerce REST API


