### v1.20.8 — debug (server-dev) | Client: v1.15.11 | Admin: v2.13.15
- Added raw response logging to TranscriptAPI call to diagnose segments structure

### v1.20.7 — bug fix (server-dev) | Client: v1.15.11 | Admin: v2.13.15
- Fixed TranscriptAPI endpoint URL (/api/v2/youtube/transcript) and response parsing (segments[] field)

### v1.20.6 — bug fix (server-dev) | Client: v1.15.11 | Admin: v2.13.15
- Switched to TranscriptAPI.com for YouTube transcript fetching — handles cloud IP blocking that was causing all previous attempts to fail
- Requires transcriptapi_API_KEY environment variable on Render
- Clean REST API, 100 free credits/month, no proxy setup needed

### v1.20.5 — bug fix (server-dev) | Client: v1.15.11 | Admin: v2.13.15
- Switched YouTube transcript fetching to page HTML approach — fetches video page, extracts caption baseUrl from ytInitialPlayerResponse, then fetches caption XML directly. More reliable than timedtext or innertube API.

### v1.20.4 — bug fix (server-dev) | Client: v1.15.11 | Admin: v2.13.15
- Switched to YouTube innertube API for transcript fetching — same internal API YouTube's frontend uses, works for ASR captions without OAuth

### v1.20.3 — bug fix (server-dev) | Client: v1.15.11 | Admin: v2.13.15
- Rewrote YouTube transcript fetcher: now uses timedtext fmt=srv3 (XML format) across multiple languages, with fallback to captions API list. Previous json3 format caused "Unexpected end of JSON input" on many videos.

### v1.20.2 (server-dev) | Client: v1.15.11 | Admin: v2.13.15
- YouTube: videos longer than 10 minutes are rejected with a clear user-facing message
- YouTube: live/streaming videos are rejected with a clear user-facing message
- Both checks use YouTube Data API v3 video metadata before attempting transcript fetch

### v1.20.1 — bug fix (server-dev) | Client: v1.15.11 | Admin: v2.13.15
- Switched YouTube transcript fetching from youtube-transcript package (blocked by YouTube CAPTCHA on cloud IPs) to YouTube Data API v3 + timedtext endpoint
- Requires YOUTUBE_API_KEY environment variable on Render
- Removed youtube-transcript from package.json

### v1.20.0 (server-dev) | Client: v1.15.11 | Admin: v2.13.15
- YouTube transcript support: direct YouTube URLs now fetch the video transcript via youtube-transcript package and analyze it. Falls back to manual text entry if no transcript available.
- News articles with embedded YouTube videos: transcripts fetched and appended to article text automatically. If no transcript available, analysis proceeds on article text only with a note shown to the user.
- YouTube added as a supported platform in detectPlatform()

### v1.19.4 — bug fix (server-dev) | Admin: v2.13.14 | Client: v1.15.9
- Fixed posts column missing from clusters/list SELECT query — posts were being saved correctly to DB but never returned to the client, causing posts: [] on every cluster fetch

### v1.19.3 (server-dev) | Admin: v2.13.10 | Client: v1.15.5
- clusters now store a posts JSONB array (scanId, url, topMatches, overallScore, ts) so any device can reconstruct the cluster visualization without needing the original device's localStorage

### v1.19.2 (server-dev) | Client: v1.15.2
- clusters/list: accepts optional device_id query param — client passes its own ID to see only its clusters; admin omits it to see all

### v1.19.1 (server-dev) | Admin: v2.13.1
- Seeded 32 default FAQs on first deploy across 4 groups: Terminology, Scanning logic, Technical, Privacy

### v1.19.0 (server-dev) | Admin: v2.13.0
- clusters table: added connections JSONB column for history reconstruction
- clusters/save: now stores connections array; postCount now includes isolated posts
- clusters/list: returns connections field
- New faq table: id, question, answer, faq_group, sort_order, active
- New GET /faq/list — returns active FAQs ordered by group and sort_order
- New POST /faq/save — add or update a FAQ item
- New DELETE /faq/:id — soft-deletes a FAQ item (sets active=false)

### v1.18.3 (server-dev) | Admin: v2.12.4 | Client: v1.14.4
- New PATCH /clusters/rename — updates cluster_name for a given cluster ID

### v1.18.2 (server-dev) | Admin: v2.12.3 | Client: v1.14.3
- clusters table now stores isolated_post_ids so cluster history can reconstruct omitted posts
- clusters/save and clusters/list updated accordingly

### v1.18.1 (server-dev) | Admin: v2.12.1 | Client: v1.14.1
- synthesize now returns postSummaries — one-liner narrative summary per post in cluster
- post_summaries column added to clusters DB table
- clusters/save and clusters/list updated to store and return postSummaries

### v1.18.0 (server-dev) | Admin: v2.12.0 | Client: v1.14.0
- New clusters DB table — stores investigation results persistently
- New POST /clusters/save — saves a cluster with name, synopsis, entity, connection type, post IDs
- New GET /clusters/list — returns all clusters ordered by date desc

### v1.17.5 — bug fix (server-dev) | Admin: v2.11.1 | Client: v1.13.0
- Fixed investigation 400 error on Hebrew/Arabic/emoji posts — replaced regex-based Unicode sanitization with Buffer-based approach that reliably strips unpaired surrogates at byte level

### v1.17.4 — bug fix (server-dev) | Admin: v2.11.1 | Client: v1.13.0
- Fixed investigation failing with 400 error on Hebrew/Arabic/emoji posts — unpaired Unicode surrogates and control characters are now stripped from post text before sending to the API

### v1.17.3 — bug fix (server-dev) | Admin: v2.11.1 | Client: v1.13.0
- Added detailed error logging to investigate/detect endpoint — surfaces exact API error message and raw response in Render logs for debugging

### v1.17.2 — bug fix (server-dev) | Admin: v2.11.1 | Client: v1.13.0
- Fixed investigate/detect returning 500 — extractJSON now handles JSON arrays (Stage 1 returns an array of pair results, not a single object)

### v1.17.1 — bug fix (server) | Admin: v2.11.0 | Client: v1.13.0
- Stage 1 connection detection: switched from Sonnet to Haiku (~75% cheaper per token)
- Stage 1: batched 4 pairs per call instead of 1 (~75% fewer API calls)
- Stage 1: trimmed prompt from ~500 to ~180 input tokens per pair
- Stage 2: trimmed synthesis prompt (~50% fewer input tokens)
- Combined saving: ~85-90% token reduction for investigation vs v1.17.0

### v1.17.0 (server) | Admin: v2.11.0 | Client: v1.13.0
- New POST /investigate/detect — Stage 1: evaluates every post pair for meaningful connection, returns connection graph and clusters using union-find
- New POST /investigate/synthesize — Stage 2: generates cluster name and synopsis for a connected cluster
