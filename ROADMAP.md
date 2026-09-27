# Roadmap

## Shipped in v9.7.7

Opt-in isolated NVIDIA board-power reads, minimized redraw/playback suppression, source-separated recordings and asynchronous updater launch preflight. Windows trust blocks remain subject to publisher signing and local policy.

## Shipped in v9.7.6

Every listed live Top 5 event has a bounded in-memory replay, at any spread.
Replacement releases the old replay and closes it if it is being viewed.
Saved-history retention remains separate.

## Shipped in v9.7.5

Manual GitHub update checks, in-app release notes, download progress, SHA-256
verification, deferred installation with Restart now / Close, a recoverable
previous-EXE backup and the post-update changelog/release link are implemented.
See [installation](docs/INSTALLATION.md) and [data storage](docs/DATA_AND_UPDATES.md).

## Future work

- Authenticode publisher signing and publisher-signature verification.
- Native Windows/hardware validation across both supported MSI PSU models.
- Optional release reminders or per-version skip controls, if requested.

No release date is promised for these items. Signing and GitHub distribution
are separate concerns; the updater retains Windows protections.
