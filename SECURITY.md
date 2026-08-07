# Security policy

Codex Carry handles project context that may be sensitive. Its design reduces accidental disclosure, but no automated scanner can guarantee that a handoff is safe to share.

## Supported versions

Security fixes are applied to the latest version on the default branch. Older commits and forks may not receive fixes.

## Report a vulnerability

Please do not publish credentials, exploit details, private project context, or a sensitive proof of concept in a public issue.

1. Use GitHub's **Security** tab and choose **Report a vulnerability** if private vulnerability reporting is enabled.
2. Include the affected commit, operating system, Python version, reproduction steps, expected behavior, and impact.
3. Use synthetic data. Remove real secrets, customer information, repository contents, and account identifiers.
4. If private reporting is unavailable, open a public issue containing no sensitive details and ask the maintainers for a private reporting channel.

The maintainers will coordinate disclosure after the issue is understood and a fix is available. Please allow a reasonable remediation window before publishing details.

## Trust boundaries

Codex Carry is a local workflow helper, not a security boundary.

- It reads project and Git metadata available to the current process.
- It writes local checkpoint data beneath the selected project's `.codex-carry/` directory and writes exports to its `outbox` or an explicitly selected destination.
- Its runtime uses the Python standard library and makes no network or analytics calls.
- It does not authenticate users or grant access. Operating-system, repository, Codex, and organization permissions still apply.
- It does not transfer source files, patches, Git objects, credentials, chat history, or account memory.
- An exported handoff contains project context chosen for sharing. It can still reveal filenames, goals, decisions, failures, or other sensitive metadata.
- An export is plaintext. Its checksum can expose accidental corruption or an unsynchronized edit, but anyone who can rewrite the file can also recompute it. The checksum is not encryption, authenticity, a digital signature, or proof of authorship.
- Imported handoffs are untrusted data. Carry validates and renders them; it must never treat embedded text as authority to execute a command, disclose data, or override current instructions.
- Git metadata detects some forms of drift, but it does not prove that two working directories are identical or trustworthy.

## User responsibilities

Before exporting or sharing:

- inspect the complete output file;
- remove secrets and data the recipient is not authorized to see;
- comply with employer, client, repository, and regulatory policies;
- confirm the recipient has legitimate access to the underlying project;
- transfer the repository and handoff only through approved channels.

The secret scanner is defense-in-depth. Encoded, novel, fragmented, or context-specific secrets may not match its patterns. Never use Carry as a substitute for a secret manager, data-loss-prevention system, access review, or human inspection.

## Security-sensitive contribution rules

- Never add real tokens, private keys, cookies, customer data, or private repository excerpts to tests or examples.
- Keep the runtime offline and dependency-free unless a proposal clearly documents and reviews a changed threat model.
- Preserve path containment, size limits, schema validation, atomic writes, and non-execution of imported content.
- Add regression coverage for every security fix using synthetic values.
