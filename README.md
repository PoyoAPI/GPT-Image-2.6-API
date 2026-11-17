# GPT Image 2.6 API: cURL, Python, and Node.js Examples

[![GPT Image 2.6 on PoYo](https://img.shields.io/badge/Try%20on%20PoYo-gpt--image--2.6-84cc16)](https://poyo.ai/models/gpt-image-2-6?utm_source=github&utm_medium=readme&utm_campaign=gpt-image-2.6-api) [![API docs](https://img.shields.io/badge/API-docs-22d3ee)](https://docs.poyo.ai/api-manual/image-series/gpt-image-2-6-fixed?utm_source=github&utm_medium=readme&utm_campaign=gpt-image-2.6-api) [![Check](https://github.com/PoyoAPI/GPT-Image-2.6-API/actions/workflows/check.yml/badge.svg)](https://github.com/PoyoAPI/GPT-Image-2.6-API/actions/workflows/check.yml)

Runnable examples for OpenAI **GPT Image 2.6** through PoYo's async image API: text-to-image and reference-image editing with a fixed per-image price (`gpt-image-2.6-fixed`) or token billing with the full option set (`gpt-image-2.6-flare`, `gpt-image-2.6-sunburst`).

**Answer:** To use the GPT Image 2.6 API, send a server-side request to `POST https://api.poyo.ai/api/generate/submit` with model `gpt-image-2.6-fixed`, `gpt-image-2.6-flare`, or `gpt-image-2.6-sunburst` and a PoYo API key, then poll `GET https://api.poyo.ai/api/generate/status/{task_id}`. The examples in this repository run with Python 3.9+, Node.js 22+, or cURL.

Last verified against the live PoYo schema and pricing on **November 17, 2026**.

## GPT Image 2.6 at a glance

- **Released:** November 4, 2026 by OpenAI
- **Model IDs:** `gpt-image-2.6-fixed` (per image), `gpt-image-2.6-flare` and `gpt-image-2.6-sunburst` (per token)
- **Variants:** Flare is tuned for speed; Sunburst for more detailed visuals and more precise, consistent edits
- **Inputs:** a prompt of up to 32,000 characters and up to 16 reference images; one image per request
- **Price:** `gpt-image-2.6-fixed` from 1.7 credits ($0.0085) per image with free reference images; Flare and Sunburst at $4 per 1M text input tokens, $6.40 per 1M image input tokens, and $24 per 1M output tokens (80% of OpenAI's price)

## Which model ID should I use?

| | `gpt-image-2.6-fixed` | `gpt-image-2.6-flare` / `gpt-image-2.6-sunburst` |
| --- | --- | --- |
| Billing | Fixed per image | Actual tokens |
| Variant | `version`: `flare` (default) or `sunburst` | Fixed by the model ID |
| Fields | `prompt`, `version`, `size`, `image_urls` | Adds `resolution`, `quality`, `background`, `output_format`, `output_compression`, `mask_url`, `moderation`, `complexity` |
| Sizes | 11 aspect ratios incl. `auto`; no custom pixels | Aspect ratios, `auto`, or custom `WIDTHxHEIGHT` |
| Masks and transparency | No | Yes |

Pick `gpt-image-2.6-fixed` when you need to know the cost before the call. Pick Flare or Sunburst for masks, transparent backgrounds, quality up to `max`, 2K/4K tiers, or JPEG/WebP output.

## Quickstart

1. Create a key in the [PoYo dashboard](https://poyo.ai/dashboard/api-key?utm_source=github&utm_medium=readme&utm_campaign=gpt-image-2.6-api).
2. Copy `.env.example` to `.env` and replace the placeholder.
3. Run one of the examples. Each submits the task and polls until `finished` or `failed`.

```dotenv
POYO_API_KEY=YOUR_POYO_API_KEY_HERE
POYO_BASE_URL=https://api.poyo.ai
```

### Python

```bash
python python/generate.py
python python/generate.py payloads/gpt-image-2.6-sunburst-transparent.json
```

[`python/generate.py`](python/generate.py) uses only the Python standard library.

### Node.js

```bash
node node/index.mjs
node node/index.mjs payloads/gpt-image-2.6-fixed-edit.json
```

### cURL

```bash
curl -X POST "https://api.poyo.ai/api/generate/submit" \
  -H "Authorization: Bearer $POYO_API_KEY" \
  -H "Content-Type: application/json" \
  -d @payloads/gpt-image-2.6-fixed-text-to-image.json
```

Status polling and the other modes are in [curl/generate.md](curl/generate.md).

## Example requests

Every file in [`payloads/`](payloads) is a complete request body with public input media, so it runs as-is.

| File | Mode | Settings | Cost |
| --- | --- | --- | --- |
| [`gpt-image-2.6-fixed-text-to-image.json`](payloads/gpt-image-2.6-fixed-text-to-image.json) | Fixed, text-to-image | Flare, 16:9 | 1.7 credits ($0.0085) |
| [`gpt-image-2.6-fixed-edit.json`](payloads/gpt-image-2.6-fixed-edit.json) | Fixed, edit with 1 reference | Sunburst | 1.7 credits ($0.0085); references free |
| [`gpt-image-2.6-flare-text-to-image.json`](payloads/gpt-image-2.6-flare-text-to-image.json) | Flare, text-to-image | 16:9, 2K, `quality: medium` | Actual tokens |
| [`gpt-image-2.6-sunburst-transparent.json`](payloads/gpt-image-2.6-sunburst-transparent.json) | Sunburst, transparent cutout | 1:1, 1K, WebP, `quality: high` | Actual tokens |

## Request fields

### `gpt-image-2.6-fixed`

| Field | Requirement | Meaning |
| --- | --- | --- |
| prompt | required | 1–32,000 characters. |
| version | optional | `flare` (default) or `sunburst`. |
| size | optional | `auto`, `1:1` (default), `16:9`, `9:16`, `4:3`, `3:4`, `3:2`, `2:3`, `5:4`, `4:5`, or `21:9`. Custom pixel sizes are not accepted. |
| image_urls | optional | Up to 16 public HTTP(S) reference images. Always an array. |

### `gpt-image-2.6-flare` and `gpt-image-2.6-sunburst`

| Field | Requirement | Meaning |
| --- | --- | --- |
| prompt | required | Up to 32,000 characters. |
| image_urls | optional | Up to 16 reference images; a non-empty array selects editing. |
| size | optional | `auto`, an aspect ratio (`1:1` default, `2:3`, `3:2`, `4:3`, `3:4`, `4:5`, `5:4`, `16:9`, `9:16`, `21:9`), or custom `WIDTHxHEIGHT`. |
| resolution | optional | `1K` (default), `2K`, or `4K`. |
| quality | optional | `low`, `medium`, `high` (default), `xhigh`, or `max`. |
| background | optional | `auto` (default), `transparent` (PNG or WebP), or `opaque`. |
| output_format | optional | `png` (default), `jpeg`, or `webp`. |
| output_compression | optional | 0–100; only with `jpeg` or `webp`. |
| mask_url | optional | Mask for the area to edit; requires `image_urls`. |
| moderation | optional | `auto` (default) or `low`. |
| complexity | optional | `low`, `medium`, or `high` (default) planning effort; does not change the price. |

Custom sizes need both sides divisible by 16, a longest edge of at most 3840, an aspect ratio of at most 3:1, and 655,360–8,294,400 total pixels. `size: auto` is billed at the 1K tier.

## Pricing

1 credit = $0.005.

| Model | Price |
| --- | --- |
| `gpt-image-2.6-fixed` | 1.7 credits ($0.0085) per image at 1K; reference images free |
| `gpt-image-2.6-flare` / `gpt-image-2.6-sunburst`, text input | 800 credits ($4) per 1M tokens |
| `gpt-image-2.6-flare` / `gpt-image-2.6-sunburst`, image input | 1,280 credits ($6.40) per 1M tokens |
| `gpt-image-2.6-flare` / `gpt-image-2.6-sunburst`, image output | 4,800 credits ($24) per 1M tokens |

The model page also lists `gpt-image-2.6-fixed` at 6 credits ($0.03) for 2K and 8 credits ($0.04) for 4K. The current API reference does not expose a `resolution` field for the fixed model, so requests run at the default 1K tier. Token-billed requests reserve a maximum based on resolution and quality, then charge actual tokens. Failed tasks are not charged. Confirm current prices on the [model page](https://poyo.ai/models/gpt-image-2-6?utm_source=github&utm_medium=readme&utm_campaign=gpt-image-2.6-api).

## Writing GPT Image 2.6 prompts

- **Edits:** say what to keep as well as what to change, for example "keep the product and replace the background".
- **Masks:** with Flare or Sunburst, send `mask_url` and describe only the change inside the masked area.
- **Cutouts:** describe an isolated subject and set `background` to `transparent` with `png` or `webp` output.
- **Exact pixels:** use a custom `WIDTHxHEIGHT` on Flare or Sunburst; aspect-ratio sizes can vary slightly between generations.

## Async production flow

```text
submit -> persist data.task_id -> poll during development -> receive callback_url webhook in production
```

Treat `finished` and `failed` as terminal states. A finished task returns the image in `files` with `file_type: image`. Copy outputs to your own storage after the task finishes.

## FAQ

**How much does the GPT Image 2.6 API cost?** `gpt-image-2.6-fixed` costs 1.7 credits ($0.0085) per image with free reference images. Flare and Sunburst bill actual tokens at $4 (text input), $6.40 (image input), and $24 (output) per 1M tokens.

**What is the difference between Flare and Sunburst?** Flare is tuned for speed; Sunburst for more detailed visuals and more precise, consistent edits.

**Can I edit images?** Yes. Add up to 16 HTTP(S) URLs in `image_urls` and describe the change. Flare and Sunburst also accept a `mask_url`.

**Does the fixed model support transparent backgrounds or masks?** No. The fixed endpoint returns raster images and accepts `prompt`, `version`, `size`, and `image_urls`. Use Flare or Sunburst for masks and transparency.

**How do I migrate from GPT Image 2.5?** Change the model ID to the matching 2.6 ID; see [GPT-Image-2-API](https://github.com/PoyoAPI/GPT-Image-2-API) for earlier GPT Image examples.

## Repository map

- [`payloads/`](payloads) — complete request bodies for each model ID
- [`python/generate.py`](python/generate.py) — standard-library Python example
- [`node/index.mjs`](node/index.mjs) — dependency-free Node.js example
- [`curl/generate.md`](curl/generate.md) — copy-paste cURL requests
- [`docs/production-notes.md`](docs/production-notes.md) — limits and rollout checklist

## Related repositories

- [GPT-Image-2-API](https://github.com/PoyoAPI/GPT-Image-2-API) — GPT Image 2 examples
- [Seedream-5.5-API](https://github.com/PoyoAPI/Seedream-5.5-API) — Seedream 5.5 with transparent PNGs and layers
- [awesome-poyo-api](https://github.com/PoyoAPI/awesome-poyo-api) — every PoYo model repository in one list
- [poyo-devtools](https://github.com/PoyoAPI/poyo-devtools) — PoYo CLI, MCP bridge, and Agent Skill

## Links

- [GPT Image 2.6 API on PoYo](https://poyo.ai/models/gpt-image-2-6?utm_source=github&utm_medium=readme&utm_campaign=gpt-image-2.6-api)
- [GPT Image 2.6 Fixed API reference](https://docs.poyo.ai/api-manual/image-series/gpt-image-2-6-fixed?utm_source=github&utm_medium=readme&utm_campaign=gpt-image-2.6-api)
- [GPT Image 2.6 Flare API reference](https://docs.poyo.ai/api-manual/image-series/gpt-image-2-6-flare?utm_source=github&utm_medium=readme&utm_campaign=gpt-image-2.6-api)
- [GPT Image 2.6 Sunburst API reference](https://docs.poyo.ai/api-manual/image-series/gpt-image-2-6-sunburst?utm_source=github&utm_medium=readme&utm_campaign=gpt-image-2.6-api)
- [PoYo model catalog](https://poyo.ai/models?utm_source=github&utm_medium=readme&utm_campaign=gpt-image-2.6-api)

## License

MIT
