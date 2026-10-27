<img width="1028" height="288" alt="workflow-overview" src="https://github.com/user-attachments/assets/f6813c44-a63a-4d9d-a084-e4b8255c9b6f" />

# WooCommerce Inventory Automation with n8n

A portfolio project demonstrating how an e-commerce team can receive order events, validate order data, classify stock risk, protect against duplicate processing, and prepare operational alerts with n8n.

> **Current status:** starter implementation / demo workflow. It uses inventory quantities included in the incoming webhook payload. It does **not** yet call a live WooCommerce store, send real email alerts, or provide persistent exactly-once processing. Those integrations require configuration and end-to-end testing.

## What it demonstrates

- Webhook-based order intake
- Input validation and normalization
- Order-level duplicate key generation
- Per-line-item inventory status classification
- Structured output for downstream logging and alerts
- Separate error-workflow template
- Example payloads for repeatable manual tests

## Architecture

```text
WooCommerce (or test client)
        |
        v
    Webhook
        |
        v
Validate + Normalize
        |
        v
Classify each line item
        |
        v
Return structured result
        |
        +--> Future integration: Google Sheets / database audit log
        +--> Future integration: email / Telegram alerts
```

## Repository contents

- `workflow.json` — importable n8n demo workflow
- `error-handler-workflow.json` — separate error-trigger workflow template
- `test-data/sample-order.json` — sample webhook payload
- `docs/SETUP.md` — import, run, and integration notes
- `docs/TEST-PLAN.md` — manual test checklist

## Quick start

1. In n8n, select **Workflows → Import from File** and import `workflow.json`.
2. Save the workflow and open the Webhook node.
3. Use the **Test URL** while the workflow is listening for a test event.
4. Send the JSON from `test-data/sample-order.json` using a REST client or cURL. See [Setup](docs/SETUP.md).
5. Inspect the execution output and compare it with the expected statuses in the test plan.

The sample payload uses `stock_quantity` values to make the demo deterministic. For a live deployment, replace this demo input with a WooCommerce REST API product lookup and configure credentials securely.

## Inventory status rules

- `OK`: available quantity is greater than the requested quantity and above the low-stock threshold.
- `LOW_STOCK`: enough stock exists for this line item, but remaining stock is at or below the threshold.
- `OUT_OF_STOCK`: available stock is less than the requested quantity, or the product is marked as not managing stock and no quantity was supplied (review this rule for your store).

This workflow is monitoring-only: it does not reduce inventory or modify WooCommerce orders.

## Duplicate handling

The workflow creates a stable key from the order ID. The current demo exposes that key but does not persist it. A production implementation should store processed order IDs in a database or n8n Data Table and use an atomic uniqueness constraint to handle concurrent duplicate webhooks safely.

## Security and production notes

- Do not commit WooCommerce consumer keys, email credentials, API tokens, customer personal data, or real order payloads.
- Verify WooCommerce webhook signatures before trusting external requests.
- Use HTTPS for webhook endpoints.
- Add persistent idempotency storage, retry/backoff, alert delivery, and a dead-letter/error process before production use.
- Test against a staging store before enabling real order webhooks.

## License

No license has been added yet. Until one is chosen, reuse and redistribution permissions are not granted by this repository.
