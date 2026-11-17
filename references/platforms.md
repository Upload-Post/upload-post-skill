# Platform-Specific Parameters

## TikTok

| Parameter | Type | Description |
|-----------|------|-------------|
| `tiktok_title` | String | Override title for TikTok |
| `post_mode` | String | `DIRECT_POST` (default) or `MEDIA_UPLOAD` (draft) |
| `privacy_level` | String | `PUBLIC_TO_EVERYONE`, `MUTUAL_FOLLOW_FRIENDS`, `FOLLOWER_OF_CREATOR`, `SELF_ONLY` |
| `auto_add_music` | Boolean | Add background music to photos |
| `disable_comment` | Boolean | Disable comments |
| `brand_content_toggle` | Boolean | Paid partnership content (also set by the cross-network `paid_partnership`) |
| `photo_cover_index` | Integer | Cover photo index (0-based) |

## Instagram

| Parameter | Type | Description |
|-----------|------|-------------|
| `instagram_title` | String | Override title |
| `media_type` | String | `IMAGE`, `STORIES`, `REELS` |
| `collaborators` | String | Comma-separated usernames |
| `user_tags` | String | Comma-separated users to tag |
| `location_id` | String | Instagram location ID |

## YouTube

| Parameter | Type | Description |
|-----------|------|-------------|
| `youtube_title` | String | Video title |
| `youtube_description` | String | Video description |
| `privacy_status` | String | `public`, `private`, `unlisted` |
| `category_id` | String | YouTube category ID |
| `tags` | Array | Video tags |
| `made_for_kids` | Boolean | COPPA compliance |
| `playlist_id` | String | Add to playlist |
| `youtube_localizations` | JSON | Translated title/description per language, e.g. `{"es": {"title": "…", "description": "…"}}`; needs `defaultLanguage` |
| `youtube_brand_partner` | String | Brand partner channel ID (`UC…`) for creator-initiated brand partner access |

## LinkedIn

| Parameter | Type | Description |
|-----------|------|-------------|
| `linkedin_title` | String | Override title |
| `visibility` | String | `PUBLIC`, `CONNECTIONS`, `LOGGED_IN` |
| `target_linkedin_page_id` | String | Organization/page URN |

Get page IDs: `GET /api/uploadposts/linkedin/pages`

Mentions in the post text: `@[Exact Page Name](urn:li:organization:ID)`, `@[Name](urn:li:person:ID)` or `@company-vanity`. A company name must match LinkedIn exactly, capitals included. Resolve any Page with `GET /api/uploadposts/linkedin/resolve-organization?user=<profile>&url=<Page URL|vanity|id>`.

## Facebook

| Parameter | Type | Description |
|-----------|------|-------------|
| `facebook_title` | String | Override title |
| `facebook_page_id` | String | Page ID (required for pages) |
| `facebook_media_type` | String | `POSTS` or `STORIES` |
| `facebook_sponsor_id` | String | Sponsor's Page id for the branded content tag (not on Reels/Stories) |

Get page IDs: `GET /api/uploadposts/facebook/pages`

Note: Personal profiles not supported by Meta API - only Pages.

## X (Twitter)

| Parameter | Type | Description |
|-----------|------|-------------|
| `x_title` | String | Override title |
| `x_long_text_as_post` | Boolean | Post long text as single post (default: thread) |
| `reply_settings` | String | `following`, `mentionedUsers`, `subscribers`, `verified` |
| `reply_to_id` | String | Tweet ID to reply to |
| `community_id` | String | Post to community |
| `tagged_user_ids` | Array | Users to tag in photos (max 10) |

Thread behavior: Long text auto-splits at 280 chars. Media attaches to first tweet only.

## Threads

| Parameter | Type | Description |
|-----------|------|-------------|
| `threads_title` | String | Override title |

Supports mixed carousels (photos + videos).

## Pinterest

| Parameter | Type | Description |
|-----------|------|-------------|
| `pinterest_title` | String | Pin title |
| `pinterest_board_id` | String | Target board ID (required) |
| `pinterest_link` | String | Destination link |
| `pinterest_alt_text` | String | Alt text for image |
| `pinterest_sponsor_id` | String | Sponsor account id for the paid partnership label (Pinterest closed beta) |

Get board IDs: `GET /api/uploadposts/pinterest/boards`

## Reddit

| Parameter | Type | Description |
|-----------|------|-------------|
| `reddit_title` | String | Post title |
| `subreddit` | String | Target subreddit (required) |
| `reddit_flair_id` | String | Flair ID |
| `nsfw` | Boolean | Mark as NSFW |
| `spoiler` | Boolean | Mark as spoiler |

## Bluesky

| Parameter | Type | Description |
|-----------|------|-------------|
| `bluesky_title` | String | Override title |
| `alt_text` | String | Alt text for images |
| `bluesky_gallery` | Boolean | Publish up to 10 images as a gallery (`app.bsky.embed.gallery`) |
| `bluesky_tags` | String / JSON array | Up to 8 hashtags stored on the post, not shown in the text |
| `bluesky_created_at` | String | ISO 8601 with time zone, not in the future: date Bluesky shows (imports) |
| `bluesky_thread_overflow` | Boolean | Text over 300 characters continues as replies instead of being cut |
| `bluesky_gif_as_video` | Boolean | Single animated GIF as a looping video (photos) |
| `bluesky_captions` | JSON array | Video: up to 20 WebVTT tracks `[{"lang": "es", "url": "https://…/es.vtt"}]` |
| `bluesky_video_presentation` | String | Video: `default` or `gif` |

An invalid value fails only the Bluesky result (`bluesky_invalid_parameter`).

Limits: 4 images per post (10 with `bluesky_gallery=true`; extra images are dropped with a warning), 1MB per image, 50 uploads/day.

## Telegram

| Parameter | Type | Description |
|-----------|------|-------------|
| `telegram_title` | String | Text / caption for Telegram |
| `telegram_parse_mode` | String | `MarkdownV2` or `HTML` |
| `telegram_reply_markup` | JSON | Inline keyboard with URL buttons |
| `telegram_pin` / `telegram_pin_notify` | Boolean | Pin after publishing (silent by default) |
| `telegram_reply_to_message_id` / `telegram_reply_quote` | Integer / String | Publish as a reply |
| `telegram_message_thread_id` | Integer | Forum topic |
| `telegram_disable_notification` / `telegram_protect_content` | Boolean | Silent / no forwarding or saving |
| `telegram_first_comment_as_reply` | Boolean | First comment as a reply (default true) |
| `telegram_poll` / `telegram_location` / `telegram_venue` / `telegram_contact` / `telegram_dice` | JSON / emoji | Text posts: one interactive message |
| `telegram_rich_message` | `true` / `markdown` / `html` / JSON | Text posts: rich message (up to 32,768 characters) |
| `telegram_send_as` | String | `animation`, `video_note`, `live_photo`, `document` (video); any type on `/upload_document` |
| `telegram_media_urls` | String | Up to 9 extra URLs: one album |
| `telegram_caption_overflow` | String | `split` sends a caption over 1,024 characters in two parts |
| `telegram_paid_star_count` | Integer | Paid media (1-25000 Stars) |
| `telegram_allow_paid_broadcast` | Boolean | Go above 30 messages/s for 0.1 Stars per message from the bot's balance. Only bots with 100,000 Stars and 100,000 monthly active users; otherwise the result fails with `telegram_paid_broadcast_not_allowed` |

Limits: text 4,096, captions 1,024; files 10 MB photos / 50 MB others. Full list: https://docs.upload-post.com/api/telegram

## Google Business

| Parameter | Type | Description |
|-----------|------|-------------|
| `gbp_location_id` | String | Location to post to |
| `gbp_topic_type` | String | `STANDARD`, `EVENT`, `OFFER` or `ALERT` |
| `gbp_alert_type` | String | `ALERT`: `COVID_19` |
| `gbp_cta_type` / `gbp_cta_url` | String | Call-to-action button |
| `gbp_upload_to_gallery` | Boolean | Photo to the Media/Gallery tab |
| `gbp_multi_media` | Boolean | Send every photo (up to 10) |
| `gbp_recurrence` | String | EVENT/OFFER: `DAILY`, `WEEKLY`, `MONTHLY` (+ `gbp_recurrence_days`, `_day_of_month`, `_week_occurrence`, `_end`) |
| `gbp_scheduled_time` | String | Google's own scheduling (RFC 3339 with time zone) |

## Common Parameters (All Platforms)

| Parameter | Type | Description |
|-----------|------|-------------|
| `first_comment` | String | Auto-post first comment (Instagram, Facebook, Threads, Bluesky, Reddit, X, YouTube, Telegram) |
| `paid_partnership` | Boolean | Sponsored post: native label on TikTok, YouTube and X (Facebook/Pinterest also need their sponsor id) |
| `add_to_queue` | Boolean | Publish at the next queue slot (the queue can use the best times: `slots: "auto"`) |
| `async_upload` | Boolean | Background processing |
| `scheduled_date` | String | ISO-8601 schedule time |
| `timezone` | String | IANA timezone for schedule |
