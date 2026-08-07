# Example handoff

[`example-handoff.carry.json`](example-handoff.carry.json) is a synthetic, secret-free export showing the kind of context Codex Carry carries between authorized environments.

It intentionally contains no source code, patch, credential, customer data, real remote URL, or real commit ID. A real export may still contain sensitive project metadata, so inspect every field before sharing it.

To try the receiving-side checks from the repository root:

```text
python skills/codex-carry/scripts/carry.py validate examples/example-handoff.carry.json
python skills/codex-carry/scripts/carry.py render examples/example-handoff.carry.json
```

Use your platform's equivalent Python 3.11+ launcher when `python` is not on `PATH`.

The example uses synthetic Git metadata and is not expected to match this repository when checked with `status`.
