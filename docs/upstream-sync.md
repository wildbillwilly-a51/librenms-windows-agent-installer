# Upstream Sync Model

This document defines the durable relationship between the private development
project and this public installer product.

## Roles

- Private development project: feature development, lab rollout, parser testing,
  and validation against real Windows and LibreNMS behavior.
- Public installer repo: generic release packaging, one-command install path,
  public documentation, checksum manifest, and distribution through GitHub.

The public repo should receive only sanitized generic artifacts.

## Current Sync Method

The current package was generated from a validated private overlay package and
converted to generic identifiers:

- section prefix: `windows_agent`
- section family: `windows_agent_*`
- LibreNMS app type: `windows-agent`
- UI name: `Windows Agent`
- overlay package: `librenms-windows-agent-overlay-0.6.0.tar.gz`

When syncing a new version, record:

- upstream source commit or tag
- public package version
- package SHA256
- validation performed in the development project
- local validation performed in this repo
- known compatibility requirements for Windows agent output

## Recommended Durable Model

The most reliable long-term approach is to invert the current flow:

1. Store the generic overlay source in this public repository.
2. Treat this repo as the product source for `windows_agent_*` and
   `windows-agent`.
3. Let the private development project consume, package, or test the generic
   overlay source during lab validation.
4. Add any lab-specific behavior only as private deployment configuration, not
   as product source.

This avoids recurring risk from text-converting a site-specific overlay into a
generic product. It also makes public releases reproducible from public source.

## Acceptable Interim Model

Until the generic overlay source is moved here, use an explicit promotion step:

1. Build and validate the private development overlay.
2. Generate a generic public overlay package in a temporary staging folder.
3. Scan the generated package for private/site-specific content.
4. Copy only the generic tarball into `artifacts/`.
5. Regenerate `SHA256SUMS`.
6. Update `CURRENT-STATE.md`, `CHANGELOG.md`, and this file with the promoted
   version and source commit.
7. Commit locally.
8. Push only after the full committed snapshot is public-safe.

Do not automate a blind copy from the private project into this public repo.
Every sync must include a public-safety scan.

## Promotion Record

### 0.6.0

- Public package: `artifacts/librenms-windows-agent-overlay-0.6.0.tar.gz`
- SHA256:
  `604d947221ff7dec81a8b57f166aa73f87073e9cd76791015b79370e9c262454`
- Compatibility: requires Windows agent output using `windows_agent` and
  `windows_agent_*` section names.
- Local validation: shell syntax check, tarball listing, checksum generation,
  raw GitHub URL checks, and public-safety scans.
- PHP lint: skipped locally because PHP was not installed.
