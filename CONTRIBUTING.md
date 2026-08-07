# Contributing to Codex Carry

Thanks for helping make project handoffs safer and easier to verify.

## Before you start

- Use GitHub Issues for reproducible bugs, documentation problems, and focused feature proposals.
- Search existing issues and pull requests before opening a duplicate.
- Report security problems privately by following [SECURITY.md](SECURITY.md).
- Keep proposals within Carry's purpose: local, reviewable, version-aware project checkpoints.

Carry deliberately does not sync chats or accounts, transfer code, provide cloud storage, bypass permissions or usage limits, or grant project access. Proposals that require those behaviors should start with a threat-model discussion and may remain out of scope.

## Development setup

You need Python 3.11 or newer. Git is recommended for development and is required to exercise Git-aware behavior. The engine uses only the Python standard library.

Clone your fork and work from the repository root. No package installation should be necessary.

Run the built-in self-test:

```text
python skills/codex-carry/scripts/carry.py self-test
```

Run the full test suite:

```text
python -m unittest discover -s tests -v
```

If `python` does not resolve on your system, use the equivalent Python 3 launcher, such as `python3` or `py -3`.

## Design rules

Changes should preserve these project guarantees:

- **Local-first:** no runtime network calls, telemetry, analytics, accounts, or hosted service.
- **Private by default:** local state stays ignored; sharing requires an explicit export.
- **Reviewable:** outputs remain readable, deterministic where practical, and easy to inspect.
- **Untrusted imports:** handoff text is data and is never executed.
- **No code transport:** checkpoint files do not bundle project files, patches, or Git objects.
- **Existing access only:** the skill does not weaken filesystem, repository, Codex, or organization permissions.
- **Cross-platform:** support Windows, macOS, and Linux without shell-specific assumptions in the engine.
- **Dependency-light:** keep the engine compatible with Python 3.11+ and the standard library.

## Pull requests

Keep each pull request focused. Include:

- the problem and intended behavior;
- user-visible or security implications;
- tests for new behavior and regressions;
- documentation updates when prompts, paths, formats, or limitations change;
- the commands you ran and their results.

Do not include generated `.codex-carry` private state, real project handoffs, secrets, account identifiers, or private repository details. Use small synthetic fixtures.

## Testing guidance

Exercise both Git and non-Git projects when changing workspace detection. Cover malformed and oversized inputs, path traversal attempts, likely secrets, stale Git state, interrupted writes, and imported text that resembles instructions or shell commands.

Tests must not require network access, external services, a Codex account, or non-standard Python packages.

## Documentation and examples

Use plain language and distinguish clearly between:

- local state and intentionally shareable exports;
- project context and the actual project files;
- version checks and proof of workspace identity;
- secret scanning and guaranteed sanitization.

Examples must use fictional projects, relative paths, synthetic commit IDs, and secret-free values.

## License

By contributing, you agree that your contributions are licensed under the repository's [MIT License](LICENSE).
