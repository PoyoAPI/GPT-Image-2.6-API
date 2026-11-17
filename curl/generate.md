# GPT Image 2.6 API: cURL quickstart

```bash
export POYO_API_KEY="your-api-key"
export POYO_BASE_URL="https://api.poyo.ai"
```

## Fixed price, text-to-image

```bash
curl -X POST "$POYO_BASE_URL/api/generate/submit" \
  -H "Authorization: Bearer $POYO_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
  "model": "gpt-image-2.6-fixed",
  "input": {
    "prompt": "A paper-cut illustration of a harbor city at sunrise, layered paper in warm orange and teal, tiny boats and lighthouses, soft shadows between the layers",
    "version": "flare",
    "size": "16:9"
  }
}'
```

## Fixed price, edit with a reference image

```bash
curl -X POST "$POYO_BASE_URL/api/generate/submit" \
  -H "Authorization: Bearer $POYO_API_KEY" \
  -H "Content-Type: application/json" \
  -d @payloads/gpt-image-2.6-fixed-edit.json
```

Reference images do not change the fixed price.

## Flare, text-to-image

```bash
curl -X POST "$POYO_BASE_URL/api/generate/submit" \
  -H "Authorization: Bearer $POYO_API_KEY" \
  -H "Content-Type: application/json" \
  -d @payloads/gpt-image-2.6-flare-text-to-image.json
```

Flare and Sunburst are billed by actual tokens.

## Sunburst, transparent cutout

```bash
curl -X POST "$POYO_BASE_URL/api/generate/submit" \
  -H "Authorization: Bearer $POYO_API_KEY" \
  -H "Content-Type: application/json" \
  -d @payloads/gpt-image-2.6-sunburst-transparent.json
```

`background: transparent` requires `png` or `webp` output.

## Check status

Save `data.task_id` from the submit response, then poll:

```bash
curl "$POYO_BASE_URL/api/generate/status/<task_id>" \
  -H "Authorization: Bearer $POYO_API_KEY"
```

For production, add a public HTTPS `callback_url` to the top-level request and make the receiver idempotent.

See the [full README](../README.md) and [production notes](../docs/production-notes.md).
