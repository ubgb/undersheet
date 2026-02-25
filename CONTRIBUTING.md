# Contributing to UnderSheet

## The fastest way to contribute: add a platform adapter

An adapter is a single Python file in `platforms/`. Here's the minimum viable version:

```python
# platforms/myplatform.py
from undersheet import PlatformAdapter

class Adapter(PlatformAdapter):
    name = "myplatform"

    def get_threads(self, thread_ids: list) -> list:
        """
        Fetch threads/posts by ID. Must return:
        [{"id": str, "title": str, "url": str, "comment_count": int, "score": int}, ...]
        """
        ...

    def get_feed(self, limit: int = 25, **kwargs) -> list:
        """
        Fetch recent posts. Must return:
        [{"id": str, "title": str, "url": str, "score": int, "created_at": str}, ...]
        """
        ...

    def post_comment(self, thread_id: str, content: str, **kwargs) -> dict:
        """
        Post a reply. Must return {"success": True} or {"error": "reason"}
        """
        ...
```

That's it. Drop the file in `platforms/`, run `python3 undersheet.py platforms` to confirm it shows up, then `python3 undersheet.py heartbeat --platform myplatform` to test.

## Testing your adapter

```bash
# Run the verify script against your platform
python3 verify.py --platform myplatform

# Or run the full suite
python3 verify.py
```

## Credentials convention

Store credentials at `~/.config/undersheet/<platform>.json`. Document the exact format in your adapter's module docstring. Never hardcode secrets.

## PR checklist

- [ ] Adapter file in `platforms/<platform>.py`
- [ ] Module docstring explains credentials format + required permissions
- [ ] `python3 verify.py --platform <platform>` passes at minimum the feed test
- [ ] Added platform to the README table

## Bugs / edge cases

Open a GitHub issue with:
- Platform name
- What you expected vs. what happened
- The raw API response if relevant (redact tokens)

## Ideas for new adapters

- Twitter / X
- Mastodon
- Slack
- Telegram
- GitHub Discussions
- Lobsters
- Dev.to

If you're building one, open an issue first so we don't duplicate effort.
