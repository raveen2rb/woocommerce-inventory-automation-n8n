# Setup and configuration

## Requirements

- A working n8n instance (self-hosted or cloud)
- A REST client such as curl or Postman for the demo
- For live integrations: a WooCommerce staging store and appropriately scoped API credentials

## Import the demo

1. Download `workflow.json` from this repository.
2. In n8n, use **Workflows → Import from File**.
3. Save the workflow. It is inactive by default.
4. Open **Order Webhook** and click **Listen for test event**.
5. Send the sample payload to the node's Test URL.

Example (replace the URL with the Test URL shown by your n8n instance):

```bash
curl -X POST "YOUR_N8N_TEST_WEBHOOK_URL" \
  -H "Content-Type: application/json" \
  --data @test-data/sample-order.json
```

If your shell cannot read the relative file path, paste the JSON body directly into Postman instead.

## Expected demo results

The workflow emits one item per line item:

- Wireless Keyboard → `OK`
- USB-C Hub → `LOW_STOCK` (6 available, 3 requested, 3 would remain)
- Portable SSD → `OUT_OF_STOCK`
- Mystery Product → `STOCK_UNKNOWN`

The classification is a monitoring estimate. It does not reserve stock, decrement stock, or change the order.

## Configure an error workflow

1. Import `error-handler-workflow.json`.
2. Save it.
3. Open the main workflow's settings and select the imported workflow as its Error Workflow.
4. Connect the normalized error output to a notification node only after choosing an approved destination and reviewing what information may be sent.

The included error workflow only normalizes the event; it does not send notifications.

## Next step: live WooCommerce product lookup

For a real integration, replace payload-supplied `stock_quantity` with an HTTP Request node that queries the WooCommerce REST API for each `product_id`. Use n8n credentials or another secure credential mechanism; never hardcode consumer keys or secrets in the workflow JSON. Map WooCommerce's `stock_quantity`, `manage_stock`, and product status fields into the classifier.

Also add:
- WooCommerce webhook signature verification
- Persistent order ID storage with atomic duplicate protection
- Google Sheets/database audit logging
- Gmail or Telegram alert delivery
- Retry/backoff for transient API failures
- Tests against a staging store before production use

## Important

The files in this repository are a portfolio starter and have not been validated against a live WooCommerce store. Verify node compatibility with your installed n8n version and run the test plan before describing the workflow as production-ready.