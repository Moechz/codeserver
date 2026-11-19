# Changelog

All notable user-visible changes to the Code Server TOS application package.

## 4.145.2-2 — 2026-11-19

### Fixed — App Center listing review remediation
- **C3** Added `PRIVACY.md` and linked it from the package Help field.
- **C2** Added `NOTICE.md` with licence and trademark attributions.
- **C10** Documented the repository layout in `README.md`.
- **V7** Rebuilt and re-uploaded the installer under a new tag, so the published
  SHA-256 matches the submitted file.
- **F1** Extended the healthcheck start period to 120 s; the previous failure was
  consistent with the checksum mismatch and a slow first start.
- **C01** Follow-up item addressed together with this version.

## 4.145.2-1 — 2026-11-09

### Added
- First release. Code Server (`codercom/code-server:4.145.2`, Docker Hub official image, unmodified)
  packaged as a TOS 7 Docker application, published on port 8035 and running
  as a non-root user (1000:1000).
- Persistent data is kept under `/Volume1/DockerAppData/codeserverdocker/`.
