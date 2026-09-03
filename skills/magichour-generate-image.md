---
name: magichour-generate-image
description: Generate an AI image with Magic Hour and download the result — the async create → poll → download flow.
api: magichour:magichour-image-projects-api
operations:
  - aiImageGenerator.createImage
  - imageProjects.getDetails
generated: 2026-09-03
method: generated
source: openapi/_original/magichour-openapi.json + https://docs.magichour.ai/integration/overview.md
---

# Generate an AI image with Magic Hour

Magic Hour generation is asynchronous: every create call returns a project `id` and
`credits_charged` immediately, then renders in the background.

1. **Create the job** — `POST /v1/ai-image-generator` (`aiImageGenerator.createImage`) with
   `Authorization: Bearer <api_key>`. Body takes `image_count`, `orientation`/`aspect_ratio`,
   `resolution`, and `style.prompt`. The response is `{ id, credits_charged, frame_cost }`.
2. **Poll for completion** — `GET /v1/image-projects/{id}` (`imageProjects.getDetails`).
   Statuses: `queued` → `rendering` → `complete` | `error` | `canceled`. Poll every few seconds;
   observed median end-to-end times are published at
   https://docs.magichour.ai/api-reference/processing-times
3. **Download** — on `complete`, `downloads[].url` holds expiring result URLs. Use each URL
   exactly as returned.

Rules that matter:
- Every render consumes credits (cost is stated per endpoint in the docs); a `402` means the
  account is out of credits and a `403` means the model/resolution is not on the current plan.
- Errors come back as `{ "message": "..." }` JSON — see errors/magichour-problem-types.yml.
- There is no idempotency key: a retried create is a second bill. Only retry when the first
  request failed before returning an `id` (e.g. a 502).
- For production, prefer webhooks (`image.completed` / `image.errored`) over polling — see
  asyncapi/magichour-webhooks.yml.
