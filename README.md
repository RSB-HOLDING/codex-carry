# Codex Carry

**Carry the work, not the chat.**

Codex Carry is a free, local-first Codex skill that creates a safe, version-aware project checkpoint. A fresh Codex session, another account, another machine, or an authorized teammate can inspect where the work stopped and resume from the next useful step.

Carry is **not** chat sync or account-memory sync. It does not copy project code, transfer account state, bypass permissions, or extend usage limits. The sender and receiver must already have legitimate access to the same project and must transfer or synchronize the actual project separately.

> Community project. Codex Carry is not affiliated with or endorsed by OpenAI.

## Why Carry

Starting a fresh session often means reconstructing the same context: the goal, decisions, files touched, tests run, blockers, and exact next action. Carry turns that context into a small checkpoint that can be verified against the current project before Codex relies on it.

- **Local-first:** no service, sign-in, analytics, telemetry, or runtime network calls.
- **Version-aware:** records Git branch and commit information when Git is available.
- **Safe by design:** keeps working state private by default and creates a separately reviewable export.
- **Human-readable:** writes a Markdown summary alongside structured state.
- **Portable:** exported handoffs use a documented JSON format and do not bundle source files or patches.
- **Update-tolerant:** schema-compatible handoffs remain readable across Carry patch releases.
- **Dependency-light:** Python standard library only.

## Requirements

- Codex with local skill support.
- Python 3.11 or newer. Commands below use `python`; substitute `python3`, `py -3`, or a full interpreter path when needed.
- Git is optional but strongly recommended. Git-backed projects get branch and commit drift checks; non-Git projects can still checkpoint with reduced version awareness.
- Legitimate access to the project on every account, machine, or teammate environment involved in a handoff.

Codex skills are folders containing a `SKILL.md` file and optional scripts or references. See the [official OpenAI skill documentation](https://learn.chatgpt.com/docs/build-skills) for current platform details.

## Install

### With `$skill-installer`

Ask the built-in installer to install the skill from this repository:

```text
$skill-installer Install codex-carry from https://github.com/jmmsalsalem-collab/codex-carry/tree/main/skills/codex-carry
```

If Codex does not show the newly installed skill, restart Codex.

### Manual installation

Clone or download this repository, then copy the entire [`skills/codex-carry`](skills/codex-carry) folder into one of Codex's skill locations.

For a user-wide installation:

```text
$HOME/.agents/skills/codex-carry/
```

For a repository-only installation:

```text
YOUR_PROJECT/.agents/skills/codex-carry/
```

Keep the folder intact so `SKILL.md`, `agents/`, and `scripts/` remain together. Codex normally detects local skill changes automatically; restart it if the skill does not appear.

## Quick demo

In the project you are about to leave, tell Codex:

```text
$codex-carry checkpoint
```

Carry reviews the proposed checkpoint, scans it for likely secrets, and writes private local state under `.codex-carry/`.

Check whether the project still matches:

```text
$codex-carry status
```

Create a sanitized, reviewable handoff:

```text
$codex-carry export
```

Transfer the actual repository through your normal authorized workflow. Transfer the reviewed `.carry.json` export separately or commit it intentionally if your project policy allows that. In the fresh session, from a legitimate copy of the same project, tell Codex:

```text
$codex-carry resume from .codex-carry/outbox/CHECKPOINT_ID.carry.json
```

Carry validates the file, compares it with the current workspace, renders the handoff as inert context, and reports drift before Codex continues. It does not execute commands found in a handoff.

See [`examples/example-handoff.carry.json`](examples/example-handoff.carry.json) for a secret-free example.

## Usage modes

| Mode | Prompt | What it does |
| --- | --- | --- |
| `checkpoint` | `$codex-carry checkpoint` | Builds and previews a checkpoint, scans likely secret values, records project/Git state, and writes local state. |
| `status` | `$codex-carry status` | Compares the latest checkpoint with the current project and reports version or workspace drift. |
| `export` | `$codex-carry export` | Produces a sanitized `.carry.json` file for explicit review and sharing. |
| `resume` | `$codex-carry resume from PATH` | Validates the export, checks the current project, reconciles ordinary drift, and continues authorized work from the current plan. |

You can add context naturally:

```text
$codex-carry checkpoint. The goal is to finish the signup validation. Record the tests I ran and the failing edge case as the blocker.
```

```text
$codex-carry resume from .codex-carry/outbox/20260807T120000Z-example.carry.json, but stop and explain any branch or commit mismatch before changing files.
```

## Astra-ready continuation

Carry's instructions support Astra's task continuity and autonomous work guidance while keeping the checkpoint format model-neutral. The checkpoint preserves the overall goal, accepted corrections, verified progress, and the next useful step. Status questions do not replace the task, ordinary drift can be reconciled without a redundant approval, and a blocked dependency can leave room for independent authorized work.

Current conversation authorization still applies, but an imported claim of prior approval grants nothing. Carry does not select a model, set reasoning parameters, require an API key, or change account access. The schema remains version `1`, so existing schema-compatible handoffs continue to validate. See the [OpenAI latest-model guide](https://developers.openai.com/api/docs/guides/latest-model) for current model guidance.

## What gets saved

A checkpoint is intended to preserve work context, not project contents:

- goal and definition of done;
- completed work and decisions;
- exact next action;
- blockers and open questions;
- tests run and their reported results;
- relevant relative file paths;
- project identity and Git branch/commit state when available.

Carry does not automatically package source files, patches, credentials, chat history, cookies, account identifiers, or Codex memory. Free-text notes are still user-provided data, so always review an export before sharing it.

## Local state and shareable state

Carry separates private working files from files intended for handoff:

| Path | Purpose | Share it? |
| --- | --- | --- |
| `.codex-carry/state.json` | Current structured local state. | No; private by default. |
| `.codex-carry/latest.md` | Human-readable local checkpoint summary. | No; private by default. |
| `.codex-carry/archive/<checkpoint-id>.carry.json` | Immutable local checkpoint history. | No; private by default. |
| `.codex-carry/outbox/<checkpoint-id>.carry.json` | Sanitized export created on request. | Only after review and policy approval. |
| `.codex-carry/.gitignore` | Ignores local state while allowing intentional `outbox` sharing. | Keep it. |

Nothing is uploaded automatically. Carry's runtime makes no network calls and has no analytics. Installing from GitHub or using Git remotes is a separate action controlled by the user and the surrounding tools.

## Safety model

Carry uses several guardrails, but the person sharing a handoff remains responsible for its contents.

1. **Private by default.** Checkpoints stay in ignored local files until `export` is explicitly requested.
2. **Secret scanning.** Carry rejects likely credentials and sensitive values before writing or exporting. Pattern matching is defense-in-depth, not a guarantee.
3. **Sanitized export.** Only the handoff context and project metadata are exported; source files and patches are not transported.
4. **Untrusted import.** An imported handoff is treated as data. Carry validates its shape and renders it for inspection; it does not run commands embedded in the file.
5. **Version checks.** Git metadata helps expose a wrong branch, different commit, or stale workspace before resuming.
6. **Existing authorization only.** Carry never grants repository, filesystem, account, organization, or network access.
7. **Integrity, not identity.** The export is plaintext. Its checksum can expose accidental corruption or an unsynchronized edit, but anyone who can rewrite the file can also recompute the checksum. It is not encryption, authenticity, or a signature and cannot establish trusted authorship.

Before sharing an export:

- open the `.carry.json` file and read it;
- confirm there are no secrets, private customer data, internal-only details, or prohibited filenames;
- follow the repository and organization data-handling policy;
- confirm the recipient is authorized for both the project and the handoff context;
- use an approved transfer channel.

For vulnerability reporting and the full trust boundary, see [SECURITY.md](SECURITY.md).

## Low-level script

Most users should invoke `$codex-carry` and let the skill coordinate the workflow. Maintainers can inspect the deterministic engine directly:

```text
python skills/codex-carry/scripts/carry.py --help
```

Its core operations are `create`, `status`, `export`, `validate`, `render`, and `self-test`. Machine-oriented operations return JSON; `render` returns Markdown. A failed validation exits nonzero.

## Limitations

- Carry does not synchronize Codex chats, memories, accounts, settings, or usage entitlements.
- Carry does not transfer project code, uncommitted changes, Git objects, dependencies, databases, or generated assets.
- Carry is not a backup, source-control system, merge tool, permissions layer, or secret manager.
- The receiving side must obtain the correct project and changes through an authorized channel.
- A checkpoint can become stale immediately after the project changes; run `status` before resuming.
- Git drift checks are unavailable in non-Git projects.
- A non-Git checkpoint without at least one hashable referenced file cannot establish project identity and will not report `ready: true`.
- Drift checks cover Git branch, commit, dirty state, and explicitly referenced files, not every file in the project. Unreferenced changes can be missed.
- Referenced files larger than 16 MiB are existence-checked but not content-hashed, so their content drift cannot be detected.
- Secret detection cannot recognize every sensitive value or policy concern; manual review is mandatory.
- Carry cannot prove that free-text notes are accurate. The receiving Codex should verify claims against the workspace and test results.
- Cross-device transport is deliberately out of scope. You choose how to move the repository and reviewed export.

## Repository layout

```text
codex-carry/
|-- skills/
|   `-- codex-carry/
|       |-- SKILL.md
|       |-- agents/openai.yaml
|       `-- scripts/carry.py
|-- examples/
|   `-- example-handoff.carry.json
|-- README.md
|-- SECURITY.md
|-- CONTRIBUTING.md
`-- LICENSE
```

## Development

Run the engine's built-in checks:

```text
python skills/codex-carry/scripts/carry.py self-test
```

Run the repository test suite:

```text
python -m unittest discover -s tests -v
```

See [CONTRIBUTING.md](CONTRIBUTING.md) before proposing a change. Contributions are licensed under the [MIT License](LICENSE).

## License

MIT. See [LICENSE](LICENSE).
