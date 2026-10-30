# Sideline Number Checker API — Bulk Verification | NumberChecker

The **Sideline Number Checker API** accepts a text file of phone numbers, creates an asynchronous bulk task, and exports a result file. This repository contains a runnable integration in seven languages.

- **Product page:** https://numberchecker.ai/products/sideline-checker
- **API documentation:** [local OpenAPI contract](openapi.yaml) (hosted product guide unavailable)
- **API base URL:** `https://api.numberchecker.ai`
- **Authentication:** `X-API-Key`
- **Get an API key:** https://platform.numberchecker.ai?utm_source=github&utm_medium=referral&utm_campaign=numberchecker-ai_sideline-checker-api

> [!NOTE]
> This repository covers the standalone product code `sideline` only. Other product codes are outside its scope.

## Product facts

| API fact | Value |
|---|---|
| Product code | `sideline` |
| Input | Text file, one phone per line |
| Processing mode | Asynchronous bulk task |
| Maximum batch | 10,000,000 identifiers |
| Export fields | `number`, `activated` |

## Workflow

1. Submit the input file to `POST /v1/tasks` with `task_type=sideline`.
2. Poll `POST /v1/gettasks` with the returned `task_id`.
3. When `status=exported`, download `result_url`.

```bash
export NUMBERCHECKER_API_KEY="YOUR_API_KEY"
curl -X POST 'https://api.numberchecker.ai/v1/tasks' \
  -H "X-API-Key: $NUMBERCHECKER_API_KEY" \
  -F 'file=@examples/numbers.txt' \
  -F 'task_type=sideline'
```

Task states are `pending`, `processing`, `exported`, and `failed`. Download the result only after export and preserve the returned column names exactly. `GET /v1/balance` reads the current account balance.

## Input and output

Provide one phone number per line in international E.164 format, including the country code. The exported fields are: `number`, `activated`.

## Errors and safe integration

Typical responses include `400` invalid input, `401` invalid API key, `402` insufficient balance, `404` unknown task, `413` oversized upload, and `500` temporary server error. Retry idempotent status reads with capped backoff when appropriate. Do not blindly resubmit task creation after a timeout or server error: the original task may already exist. Correct invalid input or credentials before retrying. Read keys from `NUMBERCHECKER_API_KEY` and never expose them in browser code or source control. Process only identifiers you are authorized to verify.

## Runnable examples

Complete submit → poll → download clients are available in the server-side examples:

- [`examples/python`](examples/python)
- [`examples/nodejs`](examples/nodejs)
- [`examples/go`](examples/go)
- [`examples/java`](examples/java)
- [`examples/csharp`](examples/csharp)
- [`examples/php`](examples/php)
- [`examples/shell`](examples/shell)

Run the server-side examples from the repository root using the exact commands listed in each language README; each command passes `examples/numbers.txt`. All server-side examples read `NUMBERCHECKER_API_KEY` from the environment and fail fast if it is missing.

## FAQ

### Is this a realtime single-identifier API?

No. It is an asynchronous bulk API for uploaded lists.

### Which product does this repository support?

Only `sideline`. The task type is intentionally fixed to that product code.

### Where can I find current pricing?

See the [NumberChecker pricing page](https://numberchecker.ai/pricing). This repository does not hard-code a price.

## Official resources

- **Product:** https://numberchecker.ai/products/sideline-checker
- **Documentation:** [local OpenAPI contract](openapi.yaml) (hosted product guide unavailable)
- **Dashboard and API keys:** https://platform.numberchecker.ai
- **Pricing:** https://numberchecker.ai/pricing
- **OpenAPI contract:** [`openapi.yaml`](openapi.yaml)
- **License:** [MIT](LICENSE)

NumberChecker is not affiliated with the third-party service named by this product family. Use this API only for authorized processing and comply with applicable privacy laws and platform terms.

*Last reviewed: 2026-10-30 · Maintained by NumberChecker.*

## Export header verified 2026-10-30

Current sample CSV columns, in order: `number`, `activated`. Preserve case, order, spaces and duplicate column names. Use positional CSV parsing when duplicate headers are present; a dictionary keyed only by header name would lose a column.

## Upload and billing limits verified 2026-10-30

- `sideline`: upload 1–10,000,000 entries; minimum billable quantity 500.

Upload minimum and minimum billable quantity are different. A smaller accepted upload can still be billed at the stated minimum; a wholly failed task with zero processed entries is not charged a minimum. A billable quantity of 0 means no billing floor. Check current pricing before submitting.

## Hosted product guide

The previous product-specific hosted guide is unavailable. The local [OpenAPI contract](openapi.yaml) and this repository’s request/response facts are the integration reference for this product. An unrelated product page is not a substitute.
