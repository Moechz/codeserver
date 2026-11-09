# codeserver-for-tos

> TerraMaster TOS 7 App Center — **Docker application** package for
> [Code Server](https://github.com/coder/code-server).

## What this is

A four-file TOS Docker application archive (`codeserverdocker.tar.gz`) that installs
Code Server on TOS 7 through the App Center:

- `config.ini` — TOS application metadata (`application_type: docker`, id `codeserverdocker`)
- `codeserverdocker.lang` — 23-language superset (TOS requires 14)
- `codeserverdocker.svg` — application icon (642 bytes, 2 SVG elements)
- `docker-compose.yml` — one service, image `codercom/code-server:4.145.2`

## How it works

| | |
|---|---|
| Container image | `codercom/code-server:4.145.2` (Docker Hub, used unmodified) |
| Open | `http://<NAS-IP>:8035/` (container port 8080) |
| Runs as | non-root, `user: "1000:1000"` |
| Persistent data | `/home/coder` → `/Volume1/DockerAppData/codeserverdocker/data` |
| Logs | `docker logs codeserverdocker` (stdout/stderr) |

`/home/coder` (mounted from `DockerAppData/codeserverdocker/data`) holds everything: `.config/code-server/config.yaml` (password/auth), `.local/share/code-server` (extensions and user settings) and whatever project folders you create.

## Design highlights

- **Non-root.** `user: "1000:1000"`; verified on real hardware (the container reports uid 1000).
- **Fixed image tag, never `:latest`**, Docker Hub only, no `privileged`, no `network_mode: host`, and no Docker socket, no `cap_add`.
- **Health check** probing the service on container port 8080.
- **Deterministic build**: GNU tar with uid/gid 0 and zeroed timestamps, gzip without a timestamp, plus the review-standards self-check above.
- **Already non-root.** The upstream image runs as uid 1000 (`coder`); the package pins the same `user: "1000:1000"` so file ownership matches the volume.
- **One volume, one port.** `/home/coder` is the only persistent path and 8080 the only published port.
- **Password from the config file.** No secret is baked into the package: the image generates one on first start and keeps it in the data folder.

## Runtime file manifest

Everything the application writes on the NAS lives inside its own data
directory; the container-internal `/tmp` is discarded with the container. The
table lists what is created and how it behaves. Nothing else is written.

| Path | Purpose | Created when | Growth bound | Lifecycle |
|---|---|---|---|---|
| `/Volume1/DockerAppData/codeserverdocker/data` | Code Server application data (everything the container writes outside /tmp) | on first start / on demand | bounded by your own data | persistent; kept on uninstall unless you ask to delete it |

## Build

```bash
scripts/build.sh                  # -> out/codeserverdocker.tar.gz + .sha256
scripts/build.sh 4.145.2-2   # packaging iteration bump
scripts/build.sh 4.145.2-1 aarch64
```

The build stages the four files, substitutes the version/platform placeholders,
removes CRLF/BOM/AppleDouble, then runs
`tools/verify.py`, a review-standards self-check (archive layout, JSON validity,
required fields, 14+ language coverage, version consistency, Docker-Hub-only
images, fixed tag, reserved/recommended host ports, non-root user, per-service
healthcheck/TZ/restart, data path below the application data root, never the
`/Volume*` wildcard, comment-free sequence blocks, `x-app-meta` position, no
literal secrets and the SVG size/element limits).

## Local verification

```bash
./TOSAppSelfTestingTool -c out/codeserverdocker.tar.gz   # official spec check
bash test/install-and-verify.sh                  # on-device E2E (needs root)
```

> Docker applications cannot be side-loaded through the App Center's manual
> install page (it accepts `.deb` only); the official self-testing tool's `-i`
> mode or `docker compose` are the only ways to install one for testing.

## Store submission checklist

1. Public GitHub repo (code + README only).
2. Release **tag `v4.145.2-1`**, assets `codeserverdocker.tar.gz` +
   `codeserverdocker.tar.gz.sha256` (Docker asset naming: `<app_id>.tar.gz`, **no
   platform suffix**).
3. Developer platform → Add Application: ID `codeserverdocker`, package type Docker,
   repository URL, architecture `x86_64`.
4. Version Management → Add Version `4.145.2-1` → automated validation → review.

## License / branding

Code Server is distributed by its upstream authors under **MIT**. The container image is the official image from Docker Hub (`codercom/code-server:4.145.2`) and is used unmodified; no upstream source code is redistributed here. This packaging is an independent community submission by Moechz and is not affiliated with the upstream project or TerraMaster.

## Limitations

- The built-in terminal runs commands as uid 1000 on the NAS; treat access to this app like SSH access.
- The web UI is plain HTTP; use a TLS reverse proxy and a strong password before exposing it.
- Extensions are installed from the internet, so the NAS needs outbound access to the VS Code marketplace (or you install VSIX files manually).

---

Packaged for TOS 7 by Moechz. Upstream image used unmodified from Docker Hub.
