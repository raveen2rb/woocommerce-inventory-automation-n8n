# Manual test plan

Run the workflow with the sample payload and inspect the Code node output.

| Case | Input | Expected result |
|---|---|---|
| Normal stock | requested 2, stock 20, threshold 5 | `OK` |
| Low stock after order | requested 3, stock 6, threshold 5 | `LOW_STOCK` |
| Insufficient stock | requested 2, stock 1 | `OUT_OF_STOCK` |
| Unknown stock | stock_quantity is null | `STOCK_UNKNOWN` |
| Missing order ID | omit both `id` and `order_id` | Execution errors with validation message |
| Empty order | omit line items or provide an empty array | Execution errors with validation message |
| Invalid quantity | quantity is zero, negative, or not numeric | Execution errors with validation message |
| Negative stock | stock_quantity is negative | Execution errors with validation message |

## Acceptance checklist

- [ ] Workflow imports without errors in the target n8n version.
- [ ] The sample payload returns four line-item outputs.
- [ ] Each inventory status matches the expected result.
- [ ] Invalid order payloads fail with a clear error.
- [ ] Error workflow is selected in main workflow settings and tested.
- [ ] Live API lookup, persistent deduplication, logging, and notifications are tested separately before production use.

This checklist describes tests to run; it does not claim they have already passed.