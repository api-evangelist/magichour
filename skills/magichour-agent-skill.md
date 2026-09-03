---
name: Magichour
description: Use when building AI-powered video, image, or audio generation into applications. Reach for this skill when agents need to create videos (face swap, text-to-video, animation), generate images (from text, edit existing, swap faces), or synthesize audio (voice generation, voice cloning). Use for integrating asynchronous generation workflows, handling file uploads/downloads, setting up webhooks for production, or troubleshooting API calls.
metadata:
    mintlify-proj: magichour
    version: "1.0"
---

# Magic Hour API Skill

## Product Summary

Magic Hour API is a REST API for AI-powered video, image, and audio generation. Use it to embed content generation into applications, automate batch workflows, or build custom UIs on top of AI models. The API uses asynchronous processing: submit a job, monitor status via polling or webhooks, then download results. Official SDKs are available for Python, Node.js, Go, and Rust. All generated content appears in the user's magichour.ai dashboard. Primary documentation: https://docs.magichour.ai

**Key files and commands:**
- API base: `https://api.magichour.ai/v1`
- Authentication: `Authorization: Bearer {API_KEY}` header
- SDKs: `magic_hour` (Python), `magic-hour` (Node.js), `magic-hour-go` (Go), `magic_hour` (Rust)
- Environment variable: `MAGIC_HOUR_API_KEY`
- Mock server for testing: `Environment.MOCK_SERVER` (Python) or `Environment.MockServer` (Node.js)

## When to Use

Use this skill when:
- **Building generation features**: Embedding video/image/audio generation into a product
- **Automating workflows**: Processing batches of media without manual intervention
- **Integrating asynchronous jobs**: Handling create → monitor → download patterns
- **Setting up webhooks**: Real-time notifications for production apps
- **Troubleshooting API calls**: Debugging authentication, file uploads, job status, or error responses
- **Choosing integration patterns**: Deciding between `generate()` (simple), polling (moderate), or webhooks (production)
- **Managing file uploads**: Uploading local files to Magic Hour storage or using public URLs
- **Handling billing**: Understanding credit costs, choosing subscription vs usage-based billing

## Quick Reference

### API Endpoints by Category

| Category | Endpoints | Use Case |
| :--- | :--- | :--- |
| **Video Creation** | `POST /v1/face-swap`, `/v1/text-to-video`, `/v1/animation`, `/v1/image-to-video`, `/v1/lip-sync`, `/v1/ai-talking-photo`, `/v1/ai-video-editor`, `/v1/audio-to-video`, `/v1/auto-subtitle-generator`, `/v1/character-replace`, `/v1/video-to-video` | Generate or edit videos |
| **Image Creation** | `POST /v1/ai-image-generator`, `/v1/face-swap-photo`, `/v1/ai-headshot-generator`, `/v1/ai-clothes-changer`, `/v1/ai-face-editor`, `/v1/body-swap`, `/v1/head-swap`, `/v1/ai-image-editor`, `/v1/ai-image-upscaler`, `/v1/image-background-remover`, `/v1/photo-colorizer`, `/v1/ai-gif-generator`, `/v1/ai-meme-generator`, `/v1/ai-qr-code-generator` | Generate or edit images |
| **Audio Creation** | `POST /v1/ai-voice-generator`, `/v1/ai-voice-cloner` | Generate speech or clone voices |
| **File Management** | `POST /v1/files/upload-urls`, `POST /v1/face-detection` | Upload files, detect faces |
| **Project Status** | `GET /v1/video-projects/:id`, `GET /v1/image-projects/:id`, `GET /v1/audio-projects/:id` | Check job status, get downloads |
| **Project Cleanup** | `DELETE /v1/video-projects/:id`, `DELETE /v1/image-projects/:id`, `DELETE /v1/audio-projects/:id` | Delete completed projects |

### SDK Methods

| Task | Python | Node.js |
| :--- | :--- | :--- |
| Initialize client | `Client(token=os.getenv("MAGIC_HOUR_API_KEY"))` | `new Client({ token: process.env.MAGIC_HOUR_API_KEY })` |
| Create job (full control) | `client.v1.face_swap.create(...)` | `await client.v1.faceSwap.create(...)` |
| Generate (simplified) | `client.v1.face_swap.generate(...)` | `await client.v1.faceSwap.generate(...)` |
| Check status | `client.v1.video_projects.get(id=project_id)` | `await client.v1.videoProjects.get({ id: projectId })` |
| Upload file | `client.v1.files.upload_file("/path/to/file.jpg")` | `await client.v1.files.uploadFile("/path/to/file.jpg")` |
| Use mock server | `Client(token=key, environment=Environment.MOCK_SERVER)` | `new Client({ token: key, environment: Environment.MockServer })` |

### Job Status States

| Status | Meaning | Action |
| :--- | :--- | :--- |
| `queued` | Waiting for processing | Keep polling |
| `rendering` | Being processed | Keep polling |
| `complete` | Finished successfully | Download result |
| `error` | Failed during processing | Handle error, check error code |
| `canceled` | Manually canceled | Job stopped (no webhook fires) |

### File Input Options

| Method | Best For | Example |
| :--- | :--- | :--- |
| Public URL | Already hosted files | `"https://example.com/video.mp4"` |
| Magic Hour library URL | Reusing generated content | `"https://magichour.ai/my-library?videoId=cmj86i5yy006x4m0z6znwowjb"` |
| Upload to Magic Hour | Local files, secure files | Call `upload_urls`, PUT file, use returned `file_path` |

### Supported File Formats

| Type | Formats |
| :--- | :--- |
| **Video** | mp4, m4v, mov, webm, gif (face swap only) |
| **Image** | png, jpg, jpeg, jfif, heic, heif, webp, avif, jp2, tiff, tif, bmp |
| **Audio** | mp3, wav, aac, flac, webm, weba, m4a, opus, ogg, oga, aiff, amr |

### Billing Quick Facts

- **Free tier**: Limited resolution, watermarked output
- **Subscription plans**: Creator ($19/mo, 12k credits), Pro ($39/mo, 25k credits), Business ($99/mo, 70k credits)
- **Credit packs**: One-time purchases ($10–$120)
- **Usage-based**: Monthly invoice with volume discounts (requires setup assistance)
- **Unused credits**: Never expire
- **Failed jobs**: No credits charged
- **Cost varies by**: Model, resolution, duration, and request options

## Decision Guidance

### When to Use `generate()` vs `create()`

| Scenario | Use `generate()` | Use `create()` |
| :--- | :--- | :--- |
| **Quick prototyping** | ✅ | ❌ |
| **Single job, can wait** | ✅ | ❌ |
| **Multiple concurrent jobs** | ❌ | ✅ |
| **Production app** | ❌ | ✅ |
| **Webhook integration** | ❌ | ✅ |
| **Custom polling logic** | ❌ | ✅ |
| **Minimal boilerplate** | ✅ | ❌ |

**Note:** `generate()` requires Python SDK v0.36.0+ or Node SDK v0.37.0+.

### When to Use Polling vs Webhooks

| Scenario | Polling | Webhooks |
| :--- | :--- | :--- |
| **Image generation** (fast) | ✅ | ❌ |
| **Video generation** (slow) | ⚠️ (works, but inefficient) | ✅ |
| **High-volume jobs** | ❌ | ✅ |
| **Simple integration** | ✅ | ❌ |
| **Production app** | ⚠️ (works, but resource-heavy) | ✅ |
| **Real-time notifications** | ❌ | ✅ |
| **No server setup** | ✅ | ❌ |

### When to Upload vs Use URLs

| Scenario | Upload | Use URL |
| :--- | :--- | :--- |
| **File already hosted** | ❌ | ✅ |
| **Local file** | ✅ | ❌ |
| **Sensitive/private file** | ✅ | ❌ |
| **Reusing Magic Hour output** | ❌ | ✅ (library URL) |
| **Minimal setup** | ❌ | ✅ |
| **No external hosting** | ✅ | ❌ |

## Workflow

### 1. Set Up Authentication

- Create an API key in the [Developer Hub](https://magichour.ai/developer?tab=api-keys)
- Store as environment variable: `export MAGIC_HOUR_API_KEY="your_key"`
- Never commit keys to version control
- Use different keys for dev/staging/production

### 2. Choose Integration Pattern

- **For learning**: Use `generate()` with the Quick Start
- **For production**: Use `create()` with webhooks or polling
- **For testing**: Use mock server (`Environment.MOCK_SERVER`)

### 3. Prepare Input Files

- **Option A**: Use public URLs (simplest)
- **Option B**: Use Magic Hour library URLs (for reusing generated content)
- **Option C**: Upload to Magic Hour storage (for local files)

### 4. Create a Job

```python
# Example: Face swap video
result = client.v1.face_swap.create(
    name="My face swap",
    assets={
        "image_file_path": "https://example.com/face.jpg",
        "video_file_path": "https://example.com/video.mp4",
        "video_source": "file"
    },
    start_seconds=0.0,
    end_seconds=10.0
)
project_id = result.id
```

### 5. Monitor Job Status

**Option A: Polling (simple)**
```python
while True:
    status = client.v1.video_projects.get(id=project_id)
    if status.status == "complete":
        break
    elif status.status == "error":
        print(f"Error: {status.error}")
        break
    time.sleep(5)
```

**Option B: Webhooks (production)**
- Register webhook endpoint in Developer Hub
- Receive `video.completed` or `video.errored` events
- Extract download URL from webhook payload

### 6. Download Results

```python
# Get fresh download URL
status = client.v1.video_projects.get(id=project_id)
download_url = status.downloads[0].url

# Download file
response = requests.get(download_url, stream=True)
with open("output.mp4", "wb") as f:
    for chunk in response.iter_content(chunk_size=8192):
        f.write(chunk)
```

### 7. Clean Up (Optional)

```python
# Delete project from Magic Hour storage
client.v1.video_projects.delete(id=project_id)
```

## Common Gotchas

- **API keys in code**: Never hardcode API keys. Always use environment variables or secure vaults.
- **Download URL expiration**: URLs expire after 24 hours. Download immediately or request fresh URLs.
- **Asynchronous processing**: Jobs don't complete instantly. Always implement status monitoring before downloading.
- **File upload retention**: Uploaded files are deleted after 7 days. Reuse `file_path` within the window.
- **Failed jobs don't charge credits**: If a job errors, you're not charged. Only successful completions consume credits.
- **Webhook signature verification**: Always verify webhook signatures in production using the webhook secret.
- **Polling intervals**: Use 2–3 seconds for images, 5–10 seconds for videos. Avoid polling too frequently.
- **Mock server for testing**: Use `Environment.MOCK_SERVER` during development to avoid credit charges.
- **Canceled jobs don't fire webhooks**: If a user cancels a job, no webhook event fires. Poll the details endpoint to detect cancellation.
- **File format mismatches**: Ensure file extension matches actual file type. Magic Hour validates by extension.
- **Resolution limits by tier**: Free tier is limited to 576px. Higher plans unlock HD and larger resolutions.
- **Concurrent job limits**: Free tier has lower concurrency. Upgrade for higher-volume processing.
- **Download directory creation**: SDK's `download_directory` parameter doesn't create folders. Create them first.
- **Webhook endpoint must return 2xx**: If your endpoint doesn't return HTTP 200–299, Magic Hour retries the webhook.
- **Timestamp format in webhook verification**: Use Unix seconds, not milliseconds.

## Verification Checklist

Before submitting work with Magic Hour API integration:

- ✅ API key is stored in environment variable, not hardcoded
- ✅ Authentication header is correctly formatted: `Authorization: Bearer {key}`
- ✅ Input files are in supported formats (check file extension)
- ✅ File paths are correct (public URLs, library URLs, or uploaded `file_path`)
- ✅ Job creation returns a valid project ID
- ✅ Status monitoring is implemented (polling or webhooks)
- ✅ Error handling covers `error` and `canceled` statuses
- ✅ Download URLs are retrieved before 24-hour expiration
- ✅ Webhook endpoint returns HTTP 2xx status codes
- ✅ Webhook signature verification is implemented (production)
- ✅ Retry logic with exponential backoff is in place
- ✅ Timeout handling prevents indefinite polling
- ✅ File cleanup is implemented (optional but recommended)
- ✅ Mock server is used for development/testing
- ✅ Logging captures job IDs, status changes, and errors

## Resources

**Comprehensive navigation:** https://docs.magichour.ai/llms.txt

**Critical documentation pages:**
1. [Quick Start Guide](https://docs.magichour.ai/get-started/quick-start) — Make your first API call in 3 minutes
2. [Integration Guide](https://docs.magichour.ai/integration/adding-api-to-your-app) — Complete create → poll → download workflow with production patterns
3. [Webhook Setup](https://docs.magichour.ai/integration/webhook/overview) — Real-time notifications for production apps
4. [API Reference](https://docs.magichour.ai/api-reference) — All endpoints, models, and credit costs
5. [Inputs & Outputs](https://docs.magichour.ai/integration/inputs-and-outputs) — File upload, download, and format specifications
6. [Models & Pricing](https://docs.magichour.ai/api-reference/models) — Credit costs by endpoint and resolution
7. [Google Colab Cookbook](https://colab.research.google.com/drive/1NTHL_lr_s-qBJ-mSecSXPzRLi9_V5JiU?usp=sharing) — Ready-to-run examples for all APIs

**Community:** [Discord](https://discord.com/invite/JX5rgsZaJp) | **Support:** support@magichour.ai

---

> For additional documentation and navigation, see: https://docs.magichour.ai/llms.txt