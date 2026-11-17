# GPT Image 2.6 API production notes

## Confirmed contract

- Endpoint: `POST /api/generate/submit`
- Model IDs: `gpt-image-2.6-fixed`, `gpt-image-2.6-flare`, `gpt-image-2.6-sunburst`
- Authentication: `Authorization: Bearer <POYO_API_KEY>`
- Status: `GET /api/generate/status/{task_id}`

## Input limits

| Field | Requirement | Constraint |
| --- | --- | --- |
| prompt | required | Up to 32,000 characters; must contain non-whitespace text. |
| image_urls | optional | Up to 16 public HTTP(S) URLs; array even for one image. |
| version (fixed) | optional | flare (default) or sunburst. |
| size (fixed) | optional | auto, 1:1 (default), 16:9, 9:16, 4:3, 3:4, 3:2, 2:3, 5:4, 4:5, 21:9; no custom pixels. |
| size (flare/sunburst) | optional | auto, aspect ratio, or WIDTHxHEIGHT (sides divisible by 16, longest edge ≤ 3840, ratio ≤ 3:1, 655,360–8,294,400 pixels). |
| resolution (flare/sunburst) | optional | 1K (default), 2K, 4K. |
| quality (flare/sunburst) | optional | low, medium, high (default), xhigh, max. |
| background (flare/sunburst) | optional | auto (default), transparent (png or webp), opaque. |
| output_format (flare/sunburst) | optional | png (default), jpeg, webp. |
| output_compression (flare/sunburst) | optional | 0–100; jpeg or webp only, otherwise 400. |
| mask_url (flare/sunburst) | optional | Requires non-empty image_urls. |
| moderation (flare/sunburst) | optional | auto (default) or low. |
| complexity (flare/sunburst) | optional | low, medium, high (default); no price change. |

Each request returns one image; `n` and `num_images` are not supported on the fixed model.

## Operational checklist

- Keep API keys in a server-side secret manager.
- Validate user input before calling the API.
- Persist `data.task_id` immediately after submission.
- Use five-second polling only for development; prefer `callback_url` in production.
- Make webhook processing idempotent and reconcile status before fulfilling an order.
- Copy output files to durable storage after the task finishes.
- Recheck the live model page before hard-coding prices.

## Failure handling

Treat non-2xx HTTP responses and non-success API `code` values as errors. Preserve the HTTP status, API code, and a redacted response body for debugging.

Schema: [PoYo API documentation](https://docs.poyo.ai/api-manual/image-series/gpt-image-2-6-fixed?utm_source=github&utm_medium=readme&utm_campaign=gpt-image-2.6-api)
