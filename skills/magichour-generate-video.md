---
name: magichour-generate-video
description: Create an AI video (text-to-video, face swap, lip sync…) with Magic Hour, upload input assets, and retrieve the render.
api: magichour:magichour-video-projects-api
operations:
  - videoAssets.generatePresignedUrl
  - textToVideo.createVideo
  - videoProjects.getDetails
generated: 2026-09-03
method: generated
source: openapi/_original/magichour-openapi.json + https://docs.magichour.ai/integration/inputs-and-outputs.md
---

# Create an AI video with Magic Hour

1. **Upload inputs (when using local files)** — `POST /v1/files/upload-urls`
   (`videoAssets.generatePresignedUrl`) with the asset types you need (`video`, `audio`,
   `image`); PUT each file to its presigned `upload_url`, then pass the returned key in the
   create call. Publicly reachable HTTPS URLs can be passed directly instead.
2. **Create the job** — e.g. `POST /v1/text-to-video` (`textToVideo.createVideo`) with
   `orientation`, `style.prompt`, and `end_seconds`. Other video generators
   (`faceSwap.createVideo`, `lipSync.createVideo`, `imageToVideo.createVideo`, …) follow the
   same shape. The response returns `{ id, credits_charged, estimated_frame_cost }`.
3. **Poll or subscribe** — `GET /v1/video-projects/{id}` (`videoProjects.getDetails`) until
   `status` is `complete`, or receive `video.completed` / `video.errored` webhooks.
4. **Download** — `downloads[].url` on the completed project; URLs expire, use verbatim.

Rules that matter:
- Video renders are the most credit-expensive calls; `estimated_frame_cost` is charged based on
  frames and resolution, and higher resolutions require a paid plan (`403` otherwise).
- `DELETE /v1/video-projects/{id}` (`videoProjects.delete`) is permanent and NOT reversible —
  the docs say so explicitly. Never delete a project you may still need.
- No idempotency key exists; only retry a create that failed before returning an `id`.
