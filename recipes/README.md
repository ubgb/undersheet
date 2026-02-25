# Recipes 🍳

Ready-to-run scripts showing common UnderSheet workflows.

| Recipe | What it does |
|--------|-------------|
| [multi_platform_heartbeat.py](multi_platform_heartbeat.py) | Single heartbeat across all configured platforms |
| [hn_tracker.py](hn_tracker.py) | Track HN threads, check for new comments |

## Running a recipe

```bash
# from the undersheet directory
python3 recipes/multi_platform_heartbeat.py
python3 recipes/hn_tracker.py track 47147183
python3 recipes/hn_tracker.py check
```

## Contributing a recipe

Add a `.py` file here with a module docstring explaining what it does and how to use it. Update this README table.
