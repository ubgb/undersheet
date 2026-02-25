# UnderSheet 🗂️

**Persistent thread memory for OpenClaw agents. Works everywhere.**

Zero dependencies. Pure Python stdlib. One file to rule them all.

---

## The Problem

Your agent wakes up every 30 minutes. It sees 40 posts. It has no idea which ones it already read, which threads it was part of, or what changed since it last looked. So it either re-reads everything or ignores everything.

This is the session amnesia problem. Every agent hits it.

## The Solution

UnderSheet tracks your threads across heartbeats — on any platform.

```
$ python3 undersheet.py heartbeat --platform hackernews

[undersheet:hackernews] heartbeat @ 09:01 UTC

💬 2 thread(s) with new replies:
  +4 — Ask HN: Share your productive usage of OpenClaw
       https://news.ycombinator.com/item?id=47147183
  +1 — Show HN: UnderSheet – thread memory for AI agents
       https://news.ycombinator.com/item?id=47149006

📰 5 new post(s) in feed:
  [451↑] I pitched a roller coaster to Disneyland at age 10 in 1978
  [397↑] Amazon accused of widespread scheme to inflate prices
  [316↑] Nearby Glasses
  [245↑] Hacking an old Kindle to display bus arrival times
  [207↑] Steel Bank Common Lisp

[undersheet] State saved.

$ python3 undersheet.py status --platform hackernews

[undersheet:hackernews] status
  Last heartbeat : 2026-02-25T09:01:44+00:00
  Tracked threads: 2
  Seen post IDs  : 47

  Threads:
    [24💬] Ask HN: Share your productive usage of OpenClaw  (last seen 2026-02-25)
           https://news.ycombinator.com/item?id=47147183
    [1💬]  Show HN: UnderSheet – thread memory for AI agents (last seen 2026-02-25)
           https://news.ycombinator.com/item?id=47149006
```

Your agent picks up exactly where it left off. Every platform. Every heartbeat.

---

## Supported Platforms

| Platform | Read | Post | Auth |
|----------|------|------|------|
| Moltbook | ✅ | ✅ (CAPTCHA solver included) | API key |
| Hacker News | ✅ | ✅ | Username/password |
| Reddit | ✅ | ✅ | OAuth (client ID/secret) |
| Twitter / X | ✅ | ✅ | Bearer token (read) + OAuth 1.0a (write) |
| Discord | ✅ | ✅ | Bot token |
| _Your platform_ | [add adapter →](#adding-a-platform) | | |

---

## Install

```bash
# Core engine
curl -fsSL https://raw.githubusercontent.com/ubgb/undersheet/main/undersheet.py \
  -o ~/.openclaw/skills/undersheet/undersheet.py

# Platform adapters (grab what you need)
mkdir -p ~/.openclaw/skills/undersheet/platforms
curl -fsSL https://raw.githubusercontent.com/ubgb/undersheet/main/platforms/moltbook.py \
  -o ~/.openclaw/skills/undersheet/platforms/moltbook.py
curl -fsSL https://raw.githubusercontent.com/ubgb/undersheet/main/platforms/hackernews.py \
  -o ~/.openclaw/skills/undersheet/platforms/hackernews.py
curl -fsSL https://raw.githubusercontent.com/ubgb/undersheet/main/platforms/reddit.py \
  -o ~/.openclaw/skills/undersheet/platforms/reddit.py
```

Or via ClawHub:
```bash
clawhub install undersheet
```

> **Twitter/X adapter:** also grab `platforms/twitter.py`
> ```bash
> curl -fsSL https://raw.githubusercontent.com/ubgb/undersheet/main/platforms/twitter.py \
>   -o ~/.openclaw/skills/undersheet/platforms/twitter.py
> ```

---

## Quick Start

**1. Configure a platform:**
```bash
# Hacker News (no auth needed for read-only)
echo '{"username": "myuser", "password": "mypass"}' \
  > ~/.config/undersheet/hackernews.json
```

**2. Run a heartbeat:**
```bash
python3 ~/.openclaw/skills/undersheet/undersheet.py heartbeat --platform hackernews
```

**3. Track a specific thread:**
```bash
python3 ~/.openclaw/skills/undersheet/undersheet.py track \
  --platform hackernews --thread-id 47147183
```

**4. Add to HEARTBEAT.md:**
```markdown
## UnderSheet (every 30 minutes)
Run: python3 ~/.openclaw/skills/undersheet/undersheet.py heartbeat --platform hackernews
```

---

## Commands

```
heartbeat   Check tracked threads + new feed posts
feed-new    Show only unseen posts from the feed
track       Start tracking a thread by ID
unread      List threads with new replies (no feed)
platforms   List installed platform adapters
```

---

## Adding a Platform

Drop a file in `platforms/` with a class named `Adapter`:

```python
from undersheet import PlatformAdapter

class Adapter(PlatformAdapter):
    name = "myplatform"

    def get_threads(self, thread_ids: list) -> list:
        # Return: [{"id", "title", "url", "comment_count", "score"}, ...]
        ...

    def get_feed(self, limit=25, **kwargs) -> list:
        # Return: [{"id", "title", "url", "score", "created_at"}, ...]
        ...

    def post_comment(self, thread_id: str, content: str, **kwargs) -> dict:
        # Return: {"success": True} or {"error": "..."}
        ...
```

Run `undersheet.py platforms` to confirm it's detected.

---

## State

State is stored per-platform at `~/.config/undersheet/<platform>_state.json`. Safe to inspect, edit, or back up.

---

## Relationship to MoltMemory

UnderSheet is the generalized successor to [MoltMemory](https://github.com/ubgb/moltmemory). MoltMemory is Moltbook-specific and stays maintained. UnderSheet brings the same architecture to every platform.

---

## License

MIT
