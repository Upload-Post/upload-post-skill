---
name: upload-post
description: "Upload content to social media platforms via Upload-Post API. Use when posting videos, photos, text, or documents to TikTok, Instagram, YouTube, LinkedIn, Facebook, X (Twitter), Threads, Pinterest, Reddit, Bluesky, Telegram, or Google Business. Supports scheduling, a queue that can fill itself with the best times to post, paid partnership labels, editing/reposting/deleting live posts, analytics, FFmpeg processing, upload history, comments and replies, Instagram and Bluesky DMs, account-wide Instagram AutoDM rules, webhooks with a delivery log, LinkedIn mentions, Telegram chat and bot management, Bluesky reads and drafts, Google Business posts, reviews, performance and location info, and YouTube channel management (playlists, captions, comment inbox and moderation, channel settings, search, live broadcasts and live chat)."
---

# Upload-Post API

Post content to multiple social media platforms with a single API call.

## Documentation

- Full API docs: https://docs.upload-post.com
- LLM-friendly: https://docs.upload-post.com/llm.txt

## Setup

1. Create account at [upload-post.com](https://upload-post.com)
2. Connect your social media accounts
3. Create a **Profile** (e.g., "mybrand") - this links your connected accounts
4. Generate an **API Key** from dashboard
5. Use the profile name as `user` parameter in API calls

## Authentication

```
Authorization: Apikey YOUR_API_KEY
```

Base URL: `https://api.upload-post.com/api`

The `user` parameter in all endpoints refers to your **profile name** (not username), which determines which connected social accounts receive the content.

## Endpoints Reference

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/upload` | POST | Upload videos |
| `/upload_photos` | POST | Upload photos/carousels |
| `/upload_text` | POST | Text-only posts |
| `/upload_document` | POST | Upload documents (LinkedIn) or any file (Telegram) |
| `/uploadposts/status?request_id=X` | GET | Check async upload status |
| `/uploadposts/history` | GET | Upload history |
| `/uploadposts/schedule` | GET | List scheduled posts |
| `/uploadposts/schedule/<job_id>` | DELETE | Cancel scheduled post |
| `/uploadposts/schedule/<job_id>` | PATCH | Edit scheduled post |
| `/uploadposts/me` | GET | Validate API key |
| `/analytics/<profile>` | GET | Get analytics |
| `/uploadposts/facebook/pages` | GET | List Facebook pages |
| `/uploadposts/linkedin/pages` | GET | List LinkedIn pages |
| `/uploadposts/linkedin/resolve-organization` | GET | Resolve any LinkedIn company Page to `{urn, name}` for mentions |
| `/uploadposts/pinterest/boards` | GET | List Pinterest boards |
| `/uploadposts/post-analytics/<request_id>` | GET | Per-post metrics of an upload |
| `/uploadposts/comments` | GET | List comments of a post, or the replies under one comment |
| `/uploadposts/dms/send` | POST | Send an Instagram or Bluesky DM (or a sender action) |
| `/uploadposts/dms/conversations` | GET | List Instagram or Bluesky DM conversations |
| `/uploadposts/autodms/rules` | GET / POST | List / create account-wide Instagram AutoDM rules |
| `/uploadposts/autodms/rules/pause` · `resume` · `delete` | POST | Manage a rule (`{"rule_id": ...}`) |
| `/uploadposts/users/notifications` | GET / POST | Webhook URL and events |
| `/uploadposts/webhooks/deliveries` | GET | Webhook delivery log (7 days) |
| `/uploadposts/webhooks/deliveries/<delivery_id>` | GET | One delivery, with its payload |
| `/uploadposts/webhooks/deliveries/<delivery_id>/redeliver` | POST | Send a delivery again |
| `/uploadposts/reddit/detailed-posts` | GET | Get Reddit posts with media |
| `/uploadposts/ffmpeg/jobs/upload` | POST | Start an FFmpeg job |
| `/uploadposts/ffmpeg/jobs/<job_id>` | GET | FFmpeg job status |
| `/uploadposts/ffmpeg/jobs/<job_id>/download` | GET | Download FFmpeg output |
| `/uploadposts/best-times?profile_username=&platform=` | GET | Best hours to post for a connected account |
| `/uploadposts/queue/settings` | GET / POST | Queue settings (`slots: "auto"` = best times) |
| `/uploadposts/queue/preview` · `/queue/next-slot` | GET | Upcoming slots / next free slot |
| `/uploadposts/posts/edit` | POST | Edit a live post (Facebook, LinkedIn, X, YouTube, Google Business, Telegram) |
| `/uploadposts/posts/repost` | POST | Repost (LinkedIn, X, Threads) or forward/copy (Telegram) |
| `/uploadposts/posts/unpublish` | POST | Delete a live post |
| `/uploadposts/telegram/*` | GET / POST / PATCH / DELETE | Telegram chat, members, invite links, topics, bot profile and commands |
| `/uploadposts/bluesky/*` | GET / POST | Bluesky profiles, search, notifications, profile/settings, drafts |
| `/uploadposts/google-business/*` | GET / PATCH | Google Business posts, reviews, gallery, performance, location info |
| `/uploadposts/youtube/*` | GET / POST / PATCH / PUT / DELETE | YouTube videos, quota, playlists, captions, comments, channel, search, live |

## Upload Videos

```bash
curl -X POST "https://api.upload-post.com/api/upload" \
  -H "Authorization: Apikey YOUR_KEY" \
  -F "user=profile_name" \
  -F "platform[]=instagram" \
  -F "platform[]=tiktok" \
  -F "video=@video.mp4" \
  -F "title=My caption"
```

Key parameters:
- `user`: Profile username (required)
- `platform[]`: Target platforms (required)
- `video`: Video file or URL (required)
- `title`: Caption/title (required)
- `description`: Extended description
- `scheduled_date`: ISO-8601 date for scheduling
- `timezone`: IANA timezone (e.g., "Europe/Madrid")
- `async_upload`: Set `true` for background processing
- `first_comment`: Auto-post first comment

## Upload Photos

```bash
curl -X POST "https://api.upload-post.com/api/upload_photos" \
  -H "Authorization: Apikey YOUR_KEY" \
  -F "user=profile_name" \
  -F "platform[]=instagram" \
  -F "photos[]=@photo1.jpg" \
  -F "photos[]=@photo2.jpg" \
  -F "title=My caption"
```

Instagram & Threads support mixed carousels (photos + videos in same post).

Bluesky takes 4 images per post (extra ones are dropped with a `warnings` entry). Send `bluesky_gallery=true` to publish up to **10** images as a gallery; with more than 10 the first 10 are published and `warnings` says so.

## Upload Text

```bash
curl -X POST "https://api.upload-post.com/api/upload_text" \
  -H "Authorization: Apikey YOUR_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "user": "profile_name",
    "platform": ["x", "threads", "bluesky"],
    "title": "My text post"
  }'
```

Supported: X, LinkedIn, Facebook, Threads, Reddit, Bluesky, Telegram, Google Business.

### Paid partnership

`paid_partnership=true` (on `/upload`, `/upload_photos`, `/upload_text`) marks a sponsored post with each network's native label: TikTok `brand_content_toggle`, YouTube `hasPaidProductPlacement`, X `paid_partnership`. Facebook also needs `facebook_sponsor_id` (sponsor's Page id; not on Reels/Stories) and Pinterest `pinterest_sponsor_id` (closed beta). Other networks publish without it and `warnings` says so; a network-specific field always wins. TikTok: with `privacy_level=SELF_ONLY` the request is refused (`400 commercial_disclosure_privacy_conflict`), and drafts do not carry it. Guide: https://docs.upload-post.com/guides/paid-partnership

### LinkedIn mentions

Write mentions in the LinkedIn post text (`linkedin_title`/`title` for text posts, `linkedin_description`/`description` for photos and videos):

- `@[Exact Page Name](urn:li:organization:ID)`: a company, showcase or school Page. The name must match the Page's name on LinkedIn **exactly, capitals included**, or LinkedIn publishes it as plain text.
- `@[Name](urn:li:person:ID)`: a person. Only person ids Upload-Post itself sees work (a member who connected their LinkedIn, or the `author` of a comment from `/uploadposts/comments`); LinkedIn does not let apps look people up.
- `@company-vanity`: Upload-Post looks the Page up and fills in its exact name.

Write plain text (no backslashes): reserved characters are escaped for you. To get the URN and exact name of any Page from its URL:

```bash
curl -G "https://api.upload-post.com/api/uploadposts/linkedin/resolve-organization" \
  -H "Authorization: Apikey YOUR_KEY" \
  --data-urlencode "user=profile_name" \
  --data-urlencode "url=https://www.linkedin.com/company/linkedin/"
# {"success": true, "urn": "urn:li:organization:1337", "id": "1337", "name": "LinkedIn",
#  "vanity_name": "linkedin", "mention": "@[LinkedIn](urn:li:organization:1337)", "cached": false}
```

`url` also takes the vanity (`linkedin` or `@linkedin`), the numeric id or `urn:li:organization:ID`. Paste `mention` into the post as is. Cached Pages are free; live lookups count against 60 per hour per account. Errors: `400 linkedin_person_not_resolvable` (a `/in/` profile), `403 linkedin_permission_missing` (reconnect LinkedIn approving the Page permissions), `404 organization_not_found`, `429 rate_limited`.

## Upload Document (LinkedIn, Telegram)

Upload PDFs, PPTs, DOCs as native LinkedIn document posts (carousel viewer). Telegram accepts **any file up to 50 MB** here; `telegram_send_as` = `document` (default), `audio`, `voice`, `animation`, `sticker`, `video_note`, `video` or `photo`.

```bash
curl -X POST "https://api.upload-post.com/api/upload_document" \
  -H "Authorization: Apikey YOUR_KEY" \
  -F "user=profile_name" \
  -F 'platform[]=linkedin' \
  -F "document=@presentation.pdf" \
  -F "title=Document Title" \
  -F "description=Post text above document"
```

Parameters:
- `document`: PDF, PPT, PPTX, DOC, DOCX (max 100MB, 300 pages)
- `title`: Document title (required)
- `description`: Post commentary
- `visibility`: PUBLIC, CONNECTIONS, LOGGED_IN, CONTAINER
- `target_linkedin_page_id`: Post to company page

## Supported Platforms

| Platform | Videos | Photos | Text | Documents |
|----------|--------|--------|------|-----------|
| TikTok | ✓ | ✓ | - | - |
| Instagram | ✓ | ✓ | - | - |
| YouTube | ✓ | - | - | - |
| LinkedIn | ✓ | ✓ | ✓ | ✓ |
| Facebook | ✓ | ✓ | ✓ | - |
| X (Twitter) | ✓ | ✓ | ✓ | - |
| Threads | ✓ | ✓ | ✓ | - |
| Pinterest | ✓ | ✓ | - | - |
| Reddit | - | ✓ | ✓ | - |
| Bluesky | ✓ | ✓ | ✓ | - |
| Telegram | ✓ | ✓ | ✓ | ✓ |
| Google Business | ✓ | ✓ | ✓ | - |

## Upload History

```bash
curl "https://api.upload-post.com/api/uploadposts/history?page=1&limit=20" \
  -H "Authorization: Apikey YOUR_KEY"
```

Parameters:
- `page`: Page number (default: 1)
- `limit`: 10, 20, 50, or 100 (default: 10)

Returns: upload timestamp, platform, success status, post URLs, errors.

## Scheduling

Add `scheduled_date` parameter (ISO-8601):

```json
{
  "scheduled_date": "2026-02-01T10:00:00Z",
  "timezone": "Europe/Madrid"
}
```

Response includes `job_id`. Manage with:
- `GET /uploadposts/schedule` - List all scheduled
- `DELETE /uploadposts/schedule/<job_id>` - Cancel
- `PATCH /uploadposts/schedule/<job_id>` - Edit (date, title, caption)

## Best Time to Post and the Automatic Queue

```bash
curl "https://api.upload-post.com/api/uploadposts/best-times?profile_username=profile_name&platform=instagram&timezone=Europe/Madrid" \
  -H "Authorization: Apikey YOUR_KEY"
```

- Platforms: TikTok, Instagram, Facebook, YouTube, LinkedIn, Threads, Pinterest, Bluesky. Optional `top` (1-24, default 5), `refresh=true` (only if the cached answer is 24 h old; YouTube 7 days), `page_urn` (LinkedIn Page), `facebook_page_id`.
- Answer: `best_times` (best first, in `timezone`), `by_hour`, `by_day`, `source` (`audience_activity`, `account_posts`, `upload_post_history`, `none`), `confidence`, `warnings`.
- Let the queue use them: `POST /api/uploadposts/queue/settings` with `{"profile_username": "profile_name", "timezone": "Europe/Madrid", "slots": "auto", "auto_slots_count": 3, "auto_platforms": ["instagram", "tiktok"]}`. Then upload with `add_to_queue=true`. Posts already scheduled do not move; a `slots` array switches back to fixed slots. `GET /queue/settings` shows `slots_mode`, `auto_slots`, `effective_slots`; `GET /queue/preview?count=` and `GET /queue/next-slot` show what is coming.

## Edit, Repost and Delete Live Posts

```bash
curl -X POST "https://api.upload-post.com/api/uploadposts/posts/edit" \
  -H "Authorization: Apikey YOUR_KEY" -H "Content-Type: application/json" \
  -d '{"platform": "telegram", "user": "profile_name", "post_id": "812", "text": "Fixed text"}'
```

- Edit: Facebook `message`, LinkedIn `commentary`, X `text` (Premium, edit window), YouTube `title`/`description`/`tags`/`privacyStatus`/`hasPaidProductPlacement`, Google Business `summary`/`cta_type`/`cta_url`/`event_*`/`offer_*` (`post_id` = `accounts/…/locations/…/localPosts/…`), Telegram `text`/`caption`, `telegram_reply_markup`, `action` = `pin`/`unpin`/`stop_poll`/`edit_live_location`/`stop_live_location`, `telegram_media` (replace the media; send `caption` or `telegram_remove_caption`).
- Repost: LinkedIn (`commentary`), X, Threads; Telegram forwards or copies with `mode` (`forward`/`copy`), `target_chat_id` or `target_user` (another profile), `post_ids` (1-100) or `all_messages`, and for a copy `caption`.
- Unpublish (`platform`, `user`, `post_id`): not Instagram, TikTok or Threads. Telegram: `delete_all_messages=true` deletes every message of the post (album, caption rest, first comment); bots can delete for 48 h.

## Check Upload Status

For async uploads or scheduled posts:

```bash
curl "https://api.upload-post.com/api/uploadposts/status?request_id=XXX" \
  -H "Authorization: Apikey YOUR_KEY"
```

Or use `job_id` for scheduled posts.

## Analytics

```bash
curl "https://api.upload-post.com/api/analytics/profile_name?platforms=instagram,tiktok" \
  -H "Authorization: Apikey YOUR_KEY"
```

Supported: Instagram, TikTok, LinkedIn, Facebook, X, YouTube, Threads, Pinterest, Reddit, Bluesky, Telegram, Google Business.

Telegram returns `followers` (subscribers or members), reactions as `engagement`/`likes` and `boosts_active` (the Bot API has no views). Google Business daily metrics for any range: `GET /api/uploadposts/google-business/performance?user=&location_id=&metrics=&start_date=&end_date=`, search keywords: `/google-business/performance/keywords?start_month=&end_month=`.

Returns: followers, impressions, reach, profile views, time-series data.

Per-post metrics of an upload: `GET /api/uploadposts/post-analytics/<request_id>?platform=tiktok`. A TikTok draft (`post_mode=MEDIA_UPLOAD`) has metrics only after the creator publishes it as **public** from the TikTok app; until then `post_metrics_error` says it is still in the inbox. After 30 days without a post Upload-Post stops asking TikTok; read a published one with `GET /api/uploadposts/post-analytics?platform_post_id=<id>&platform=tiktok&user=<profile>`. TikTok rows carry `original_publish_id`, and `post_url` is empty when the account's @username is unknown.

## Get Pages/Boards

```bash
# Facebook Pages
curl "https://api.upload-post.com/api/uploadposts/facebook/pages" \
  -H "Authorization: Apikey YOUR_KEY"

# LinkedIn Pages  
curl "https://api.upload-post.com/api/uploadposts/linkedin/pages" \
  -H "Authorization: Apikey YOUR_KEY"

# Pinterest Boards
curl "https://api.upload-post.com/api/uploadposts/pinterest/boards" \
  -H "Authorization: Apikey YOUR_KEY"
```

## Reddit Detailed Posts

Get posts with full media info (images, galleries, videos):

```bash
curl "https://api.upload-post.com/api/uploadposts/reddit/detailed-posts?profile_username=myprofile" \
  -H "Authorization: Apikey YOUR_KEY"
```

Returns up to 2000 posts with media URLs, dimensions, thumbnails.

## FFmpeg Editor

Process media with custom FFmpeg commands:

```bash
curl -X POST "https://api.upload-post.com/api/uploadposts/ffmpeg/jobs/upload" \
  -H "Authorization: Apikey YOUR_KEY" \
  -F "file=@input.mp4" \
  -F "full_command=ffmpeg -y -i {input} -c:v libx264 -crf 23 {output}" \
  -F "output_extension=mp4"
```

- Use `{input}` and `{output}` placeholders
- Poll `GET /api/uploadposts/ffmpeg/jobs/<job_id>` until `FINISHED` (or `ERROR`)
- Download result from `GET /api/uploadposts/ffmpeg/jobs/<job_id>/download`
- Supports multiple inputs: upload `file`, `file1`, `file2`... and reference them as `{input0}`, `{input1}`, etc.
- Rules (400 otherwise): every `-i` must be `{input}`/`{inputN}` or a `-f lavfi` source; no URLs or protocols (`http://`, `concat:`, `file:`...) inside the command; endless inputs (`-stream_loop -1`, `-loop 1`, lavfi without duration) need `-t`/`-shortest`/`-frames`.

Quotas: Free 30min/mo, Basic 300min, Pro 1000min, Advanced 3000min, Business 10000min.

## Comments and Replies

```bash
# Top-level comments of a post
curl "https://api.upload-post.com/api/uploadposts/comments?platform=instagram&user=profile_name&post_id=17890455123456789" \
  -H "Authorization: Apikey YOUR_KEY"

# Replies under one comment: same call plus comment_id
curl "https://api.upload-post.com/api/uploadposts/comments?platform=instagram&user=profile_name&post_id=17890455123456789&comment_id=17858893269123456&limit=25" \
  -H "Authorization: Apikey YOUR_KEY"
```

- `comment_id` (replies) works on Instagram, Facebook, LinkedIn (company Pages only), TikTok, Threads and YouTube. X and Bluesky answer `400 platform_not_supported`. `post_id` is always required.
- Instagram: numeric comment id. Facebook: the `{post}_{comment}` id; `post_id` in `{page_id}_{post_id}` form picks the Page. LinkedIn: pass the `comment_urn` from the listing (`urn:li:comment:(urn:li:activity:…,…)`); a bare id is only searched among the post's first 250 comments.
- Paginate with `after` = `pagination.next_cursor` (`cursor` is ignored here). `limit` 1-50; LinkedIn's top-level listing ignores it.
- Errors: `400 invalid_comment_id`, `404 comment_not_found` (Instagram/Facebook), `404 linkedin_comment_not_found`, `403 linkedin_personal_not_supported` (personal profile), `403 linkedin_page_access_denied` (no longer Page admin: reconnect). A comment without replies is `200` with `comments: []`.
- Reply with `POST /api/uploadposts/comments/create` (`platform`, `user`, `comment_id`, `message`, plus `post_id` where needed).

## Direct Messages (Instagram, Bluesky)

```bash
curl -X POST "https://api.upload-post.com/api/uploadposts/dms/send" \
  -H "Authorization: Apikey YOUR_KEY" \
  -H "Content-Type: application/json" \
  -d '{"platform": "bluesky", "user": "profile_name", "recipient_id": "alice.bsky.social", "message": "Thanks for the follow! https://example.com"}'

curl "https://api.upload-post.com/api/uploadposts/dms/conversations?platform=bluesky&user=profile_name&messages_limit=1" \
  -H "Authorization: Apikey YOUR_KEY"
```

- Instagram: `recipient_id` is the Instagram-scoped id (from conversations or the `dm_received` webhook); the person must have written in the last 24 hours. Optional `buttons` (3), `attachments`, `quick_replies`, `generic_template`, `reply_to`, `sender_action` (`typing_on`, `typing_off`, `mark_seen`, `react`, `unreact` with `message_id`).
- Bluesky: needs an app password created with **"Allow access to your direct messages"** (Bluesky > Settings > Privacy and security > App passwords), else `403 bluesky_dm_scope_missing`: reconnect Bluesky with a new one. `recipient_id` is a handle, DID or profile link, or pass `conversation_id` (also for groups). Text up to 1,000 characters; links, `#hashtags` and up to 10 `@mentions` become clickable. Share a post with `attachments: {"type": "post", "url": "https://bsky.app/profile/<handle>/post/<id>"}` (no images or files). `reply_to`, and `sender_action`: `mark_seen`/`mark_read`, `react`/`unreact` (`message_id` + a single emoji in `reaction`), `delete` (for you), `accept`, `mute`, `unmute`, `leave`. Sender actions spend no DM quota.
- Bluesky conversations: `participant_id` (handle/DID), `conversation_id` + `messages_cursor` (older messages), `status=request|accepted`, `read_state=unread`. At most 25 per page; slow pages mark the rest `messages_truncated: true`. Messages carry `direction` (`in`/`out`), `reactions`, `reply_to`.
- Bluesky errors: `403 bluesky_recipient_unavailable` (`reason`; no quota spent), `404 bluesky_recipient_not_found` / `bluesky_conversation_not_found`, `400 bluesky_message_too_long`, `401 bluesky_reauth_required`, `429 bluesky_rate_limited`, `503 bluesky_edge_rate_limited` (`Retry-After`). **`504 bluesky_send_unconfirmed` (`retryable: false`): the DM may have gone out, read the conversation before sending again and never retry it automatically.**

## AutoDM: Account-Wide Instagram Rules

One rule answers with a DM every comment on any post or reel of the Instagram account (present and future), plus story replies, story mentions and DMs with a keyword. Rules never expire, do not count towards the 2 per-post monitors per profile per day, and run from Meta webhooks only.

```bash
curl -X POST "https://api.upload-post.com/api/uploadposts/autodms/rules" \
  -H "Authorization: Apikey YOUR_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "profile_username": "profile_name",
    "triggers": ["comment", "story_reply", "story_mention"],
    "trigger_keywords": ["guide"],
    "reply_message": "Here is your free guide!",
    "buttons": [{"title": "Get the guide", "url": "https://example.com/guide"}],
    "per_user_cooldown_hours": 24
  }'
```

- `triggers`: `comment` (default), `story_reply`, `story_mention`, `dm_keyword` (needs `trigger_keywords`). Keywords: up to 20, case- and accent-insensitive, not applied to `story_mention`; blank keywords are a `400`.
- `reply_message`: max 1,000 characters (640 with `buttons`, up to 3). `per_user_cooldown_hours`: whole number 0-720 (default 24); message triggers never go below 24 h (`message_cooldown_hours`).
- `GET /api/uploadposts/autodms/rules[?profile_username=]`: `status` (`active`, `paused`, `reconnect_required`, `stopped` + `stop_reason`, `disabled`), `stats`, `pending`, `webhook` (`receiving_events`, `last_event_at`, `last_event_age_hours`, `hint`).
- `POST /api/uploadposts/autodms/rules/pause|resume|delete` with `{"rule_id": "rule_..."}`.
- Limits: 10 rules per Instagram account; a post with its own monitor is answered by that monitor; 25 DMs per account every 15 minutes plus the plan's daily DM allowance; overflow is queued up to 24 h. Uncertain sends are never repeated (`stats.unknown_outcome`).
- Errors: `502 webhook_subscription_failed` (reconnect Instagram), `409 duplicate_rule` / `rule_limit_reached` / `rule_creation_in_progress` / `rule_stopped`, `503 account_rules_disabled`.

## Telegram (your own bot)

Publishing options (all optional, Bot API names with a `telegram_` prefix, JSON options as the official object): `telegram_title`, `telegram_parse_mode`, `telegram_reply_markup` (URL buttons), `telegram_pin` / `telegram_pin_notify`, `telegram_reply_to_message_id`, `telegram_message_thread_id`, `telegram_disable_notification`, `telegram_protect_content`, `telegram_first_comment_as_reply` (default true). Text posts: one of `telegram_poll`, `telegram_location`, `telegram_venue`, `telegram_contact`, `telegram_dice`, or `telegram_rich_message`. Media: `telegram_send_as` (`animation`, `video_note`, `live_photo`, `document`), `telegram_media_urls` (album), `telegram_caption_overflow=split`, `telegram_paid_star_count` (paid media, Stars go to your channel/bot). An invalid option fails only the Telegram result (`telegram_invalid_parameter`). Reference: https://docs.upload-post.com/api/telegram

Management (`user` = profile; the bot needs the matching admin right): `GET|PATCH /uploadposts/telegram/chat-info`, `GET /telegram/boosts`, `GET|POST /telegram/members` (`action`: `ban`, `unban`, `restrict`, `promote`, `demote`, `set_title`), `POST|PATCH|DELETE /telegram/invite-links`, `POST /telegram/join-requests`, `POST|PATCH|DELETE /telegram/topics`, `GET|PATCH /telegram/bot-profile`, `GET|POST|DELETE /telegram/bot-commands`, `GET /telegram/stars`. Destructive calls need `"confirm": true`. DMs: `POST /uploadposts/dms/send` with `platform=telegram` and the person's numeric `recipient_id` (only people who started the bot).

## Bluesky Reads and Drafts

- Publishing extras: `bluesky_tags` (up to 8 hidden hashtags), `bluesky_created_at` (import older posts), `bluesky_thread_overflow=true` (long text continues as replies), `bluesky_gif_as_video`, video `bluesky_captions` (WebVTT) and `bluesky_video_presentation=gif`.
- Reads: `GET /uploadposts/bluesky/profile?actor=` (up to 25; empty = connected account), `/bluesky/search/posts?user=&q=` (needs `user`), `/bluesky/search/actors?q=`, `/bluesky/followers`, `/bluesky/follows`, `/uploadposts/post-engagement?platform=bluesky&post_id=&type=likes|reposts|quotes`, `/bluesky/notifications?user=&reasons=`, `/bluesky/notifications/unread-count`, read-only gateway `/bluesky/xrpc/<nsid>` (allow-listed public methods).
- Account: `GET|POST /bluesky/profile/self`, `/bluesky/settings`, `/bluesky/preferences`, post actions `POST /uploadposts/posts/action` (`bookmark`, `pin`, reply/quote settings). Drafts: `GET|POST /bluesky/drafts`, `PATCH|DELETE /bluesky/drafts/<id>` (text only; delete needs `confirm`).

## Google Business

- Publishing extras: `gbp_alert_type`, `gbp_upload_to_gallery`, `gbp_multi_media=true` (up to 10 photos), recurring EVENT/OFFER posts (`gbp_recurrence` + `_days`/`_day_of_month`/`_week_occurrence`/`_end`), `gbp_scheduled_time` (Google's own scheduling).
- Posts: `GET /uploadposts/google-business/posts?user=&location_id=`, `/posts/get?post_id=`; edit with `/posts/edit`, delete with `/posts/unpublish`.
- Reviews: `GET /google-business/reviews` (`order_by`), `/reviews/get?review_name=`, `/reviews/batch?location_ids=`; reply `PUT /reviews/reply`.
- Location: `GET /google-business/location-info`, `PATCH /location-info` with `edit_mode` `legacy` (default) or `strict` (title, address, categories, open status; `dry_run`, `confirm`), plus gallery (`/media`), attributes, Place Actions, menus/services, lodging. `location_id` is optional (default: the location selected for the profile).

## YouTube Management (after publishing)

Upload extras: `youtube_localizations` (translated title/description per language, JSON; needs `defaultLanguage`) and `youtube_brand_partner` (brand channel ID `UC…`). Everything below takes `user` (profile) and lives under `/api/uploadposts/youtube`.

- **Videos**: `GET /youtube/videos?ids=` (or `chart=mostPopular`, `my_rating=like`), `parts`, `include_owner_details`. Thumbnail: `POST /youtube/thumbnail` (`video_id` + file or `thumbnail_url`). Edit metadata with `POST /uploadposts/posts/edit` (`platform: youtube`).
- **Playlists**: `GET|POST /youtube/playlists`, `PATCH|DELETE /youtube/playlists/<id>`, items `GET|POST /playlists/<id>/items` (`video_id`, `position`, `note`), `PATCH|DELETE /playlists/<id>/items/<item_id>`, image `/playlists/<id>/image`.
- **Captions**: `GET /youtube/captions?video_id=`, `POST /youtube/captions` (file or `file_url`, `language`, `name`, `is_draft`), `PUT|DELETE /captions/<id>`, `GET /captions/<id>/download?tfmt=srt|vtt…&tlang=`.
- **Comments inbox**: `GET /youtube/comments?video_id=` or `scope=channel` (`moderation_status=heldForReview|likelySpam`, `order`, `search`), `GET /comments/replies?parent_id=`, `POST /youtube/comments` (`text` + `parent_id` to reply or `video_id`; optional `idempotency_key`), `PATCH|DELETE /comments/<id>`, `POST /comments/moderate` (`comment_ids` up to 50, `status` `published`/`heldForReview`/`rejected`, `ban_author`).
- **Channel**: `GET|PATCH /youtube/channel` (`description`, `keywords`, `country`, `default_language`, `unsubscribed_trailer`, `localizations`; settings and translations in separate calls; the channel name cannot be changed), `GET /channel/lookup?for_handle=@…`, banner, sections, watermark.
- **Search and catalogs**: `GET /youtube/search` (`q`, `for_mine`, `channel_id`, `type`, `order`…), `/youtube/categories`, `/languages`, `/regions`. Subscriptions, ratings, activities: `/youtube/subscriptions`, `/videos/<id>/rate`, `/youtube/activities`.
- **Live**: `GET|POST /youtube/live/broadcasts` (`title`, `scheduled_start_time`, `privacy_status`, `create_stream`), `PATCH|DELETE /live/broadcasts/<id>`, `/bind`, `/transition` (`testing`, `live`, `complete`), `/cuepoint`; streams `/live/streams` (the stream key is a secret: never print it). Chat: `GET|POST /youtube/live/chat` (`broadcast_id` or `live_chat_id`; messages and polls only in your own broadcasts), `/live/chat/polls/<id>/close`, `/live/moderators`, `/live/bans`, `/live/super-chats`.

**Daily allowance.** These calls share a daily budget per Upload-Post account in YouTube quota units, reset at midnight Pacific: **2,000 units on paid plans, 200 on free** (searches: 5 and 1 calls). Check it with `GET /api/uploadposts/youtube/quota` before a batch. Reads cost 1 unit (chat 5 per page); playlist and comment writes ~50; captions are the expensive part (list 50, upload 400, replace 450, download 200), so the free plan cannot upload or download captions (`403 youtube_plan_daily_limit_too_low`); moderators 50-200. Uploads have their own allowance and are never limited by this one.

**Rules.** Lists return `items`, `next_page_token`, `page_info`. On updates `""` means no change; remove with `null` or `*_remove`. Destructive or hard-to-undo calls (delete playlist/item/caption/comment/section/broadcast/stream, reject comments, ban, remove a moderator, replace a watermark or image, go `live` or `complete`) need `confirm: true`, otherwise `400 confirmation_required` and nothing reaches YouTube: ask the user first. `502 youtube_write_outcome_unknown` means the write may have happened: read it back before retrying. Captions, moderators and bans need the full YouTube permission (`403 youtube_insufficient_scope`: reconnect the channel). Reference: https://docs.upload-post.com/api/youtube-videos

## Webhooks

Configure with `POST /api/uploadposts/users/notifications`:

```json
{"channels": {"webhook": true}, "webhook_url": "https://example.com/hook",
 "webhook_events": {"upload_completed": true, "dm_received": true, "comment_received": true}}
```

Events: `upload_completed`, `social_account_connected`, `social_account_disconnected`, `social_account_reauth_required`, `dm_received` (Instagram; Bluesky checked every 5 minutes), `comment_received`. Signed with HMAC-SHA256 (`X-Upload-Post-Signature`).

- **At least once.** A timeout (15 s total), connection error, `5xx`, `408` or `429` is retried: 6 attempts over ~8.5 h (+1 min, +5 min, +30 min, +2 h, +6 h), same body and same `X-Upload-Post-Delivery` id (deduplicate by it); `X-Upload-Post-Attempt` is 1-6. Other `4xx` are final; `3xx` counts as delivered (redirects are not followed). No ordering guarantee.
- After 5 consecutive failures the webhook pauses 30 min; events during the pause are queued, not lost. Pending after 24 h = `failed`.
- `GET /api/uploadposts/webhooks/deliveries`: filters `event`, `status` (`pending`, `delivered`, `failed`, `cancelled`), `request_id`, `job_id`, `profile_username`; `limit` 1-100 (20), `page` 1-50, `include_payload=true`. Kept 7 days.
- `GET /api/uploadposts/webhooks/deliveries/<delivery_id>`: one delivery with `attempts` and `payload`.
- `POST /api/uploadposts/webhooks/deliveries/<delivery_id>/redeliver`: new delivery now, same body, current URL, header `X-Upload-Post-Redelivery-Of`. `404` unknown/expired, `409` (`webhook_removed`, `event_unsubscribed`, `blocked_url`).

## Platform-Specific Parameters

See [references/platforms.md](references/platforms.md) for detailed platform parameters.

## Media Requirements

See [references/requirements.md](references/requirements.md) for format specs per platform.

## Error Codes

| Code | Meaning |
|------|---------|
| 400 | Bad request / missing params |
| 401 | Invalid API key |
| 403 `TEAM_SEAT_SUSPENDED` | Team member acting with `X-Act-As-User` on an account whose plan no longer covers that seat. Access returns when the owner upgrades or frees a seat |
| 404 | Resource not found |
| 429 | Rate limit / quota exceeded |
| 500 | Server error |

## Notes

- Videos auto-switch to async if >59s processing time
- X long text creates threads unless `x_long_text_as_post=true`
- Facebook requires Page ID (personal profiles not supported by Meta)
- Instagram/Threads support mixed carousels (photos + videos)
- Instagram posts cannot be deleted through Upload-Post (Meta only allows it with Facebook Login; Upload-Post connects Instagram with Instagram Login). TikTok has no delete API either
- X edits: Premium only, inside X's edit window (`edit_controls.editable_until` is the real deadline); the post ID changes
- Instagram `media_url` is missing for videos with licensed audio: fall back to `permalink` or `thumbnail_url`
- Telegram bots can delete messages for 48 h; Telegram has no views or channel statistics in the Bot API
