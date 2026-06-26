---
name: twitter
description: "Fetch a SPECIFIC Twitter/X tweet, article, thread, profile, or timeline by URL/id via the SocialData API. Use when: a Twitter link, read a known tweet, check a profile, read a thread. For SEARCH / discovery / 'what's trending in a niche', use the `x-research` skill (Grok Live Search) instead — this skill is fetch-only."
user-invocable: true
argument-hint: "<twitter-url>"
---

# Twitter / X Reader (SocialData)

Fetch a SPECIFIC tweet, article, thread, profile, or timeline by URL/id.

> **Search / discovery / radar / "what's viral"** → use the `x-research` skill (Grok Live Search), NOT this one. This skill is fetch-by-url/id only.

## What handles what

| Task | Source |
|------|--------|
| Single tweet, profile | SocialData |
| Article, thread, timeline, comments | SocialData |
| **Search / discovery / radar** | **`x-research` skill (Grok)** — not this skill |

## Setup

```bash
# SocialData API key (register at https://socialdata.tools)
echo 'your-key' > ~/.claude-lab/shared/secrets/socialdata-api-key
SOCIALDATA_API_KEY=$(cat ~/.claude-lab/shared/secrets/socialdata-api-key)
```

## Single Tweet

```bash
# https://x.com/elonmusk/status/1234567890  -> tweet_id = 1234567890
curl -sS "https://api.socialdata.tools/twitter/tweets/1234567890" \
  -H "Authorization: Bearer $SOCIALDATA_API_KEY"
```

## User Profile

```bash
curl -sS "https://api.socialdata.tools/twitter/user/elonmusk" \
  -H "Authorization: Bearer $SOCIALDATA_API_KEY"
```

## Thread

```bash
curl -sS "https://api.socialdata.tools/twitter/thread/TWEET_ID" \
  -H "Authorization: Bearer $SOCIALDATA_API_KEY"
```

## X Article (long-form)

```bash
# 1. Get the tweet, find article_id in entities.urls (x.com/i/article/{article_id})
# 2. Fetch the article:
curl -sS "https://api.socialdata.tools/twitter/article/ARTICLE_ID" \
  -H "Authorization: Bearer $SOCIALDATA_API_KEY"
```

## User Timeline (needs user_id, not username)

```bash
USER_ID=$(curl -sS "https://api.socialdata.tools/twitter/user/elonmusk" \
  -H "Authorization: Bearer $SOCIALDATA_API_KEY" | python3 -c "import json,sys;print(json.load(sys.stdin)['id'])")
curl -sS "https://api.socialdata.tools/twitter/user/$USER_ID/tweets" \
  -H "Authorization: Bearer $SOCIALDATA_API_KEY"
```

## Search (prefer x-research)

For search/discovery use the `x-research` skill (Grok). SocialData search is kept only for precise Advanced-Search-operator queries that need a raw tweet list:

```bash
curl -sS "https://api.socialdata.tools/twitter/search" \
  -H "Authorization: Bearer $SOCIALDATA_API_KEY" \
  -G -d "query=from:elonmusk AI agents" -d "type=Latest"
```

## Comments on a tweet

```bash
curl -sS "https://api.socialdata.tools/twitter/tweets/TWEET_ID/comments" \
  -H "Authorization: Bearer $SOCIALDATA_API_KEY"
```

## Error Handling

| Code | Meaning | Action |
|------|---------|--------|
| 404 | Tweet deleted or private | Inform user |
| 401 | Bad API key | Check SocialData key |
| 429 | Rate limited | Wait and retry |
