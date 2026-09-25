# Roadmap

These features are planned, not included in v9.7.4. No release date is promised.

## In-app updates

The intended screen shows the installed version, available version, download
size and release changelog, with **Update now**, **Skip this version**,
**Remind me later** and **View release on GitHub** actions.

After a successful update, show the installed version and changelog again, with
a button linking to that exact release page.

Implementation requirements:

- Read published releases from GitHub's public API; never embed a private
  GitHub token in the application. Cache checks and respect rate limits.
- Compare version components numerically and match the Windows x64 asset.
  Do not silently install a prerelease or downgrade.
- Fetch only expected HTTPS release assets. Use timeouts, download limits and
  a temporary staging directory. Show release notes as inert text.
- Verify a SHA-256 digest before installation. A digest from the same GitHub
  release detects corruption, but does not protect against a compromised
  publishing account. Add publisher-signature verification when signing is available.
- Install only on the user's request. Stop polling and flush captures before
  shutdown, preserve settings and logs, and use a helper to replace the closed
  executable with a recoverable backup. Restore the old version if replacement fails.
- Confirm the new version actually started before displaying success. Keep a
  manual-download route available when Windows policy or file access prevents updating.

GitHub distribution, code signing and Windows application trust are separate
concerns. The updater must not disable Windows protections.

References: [Releases API](https://docs.github.com/en/rest/releases/releases),
[API rate limits](https://docs.github.com/en/rest/using-the-rest-api/rate-limits-for-the-rest-api).
