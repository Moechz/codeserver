# Privacy Policy — Code Server for TerraMaster TOS 7

This policy describes how the **Code Server** Docker application package
(`codeserverdocker`) handles data when installed on your TerraMaster NAS.

- Package: `codeserverdocker` (TOS 7 App Center, Docker application)
- Upstream software: [code-server](https://github.com/coder/code-server) 4.145.2
- Publisher: Moechz

## 1. Scope

The application runs the official `codercom/code-server` Docker Hub image as a
non-root container (uid 1000). It is a browser-based IDE: you edit code that is
stored on your own NAS. This policy covers the package and its default
configuration; extensions you install have their own behaviour.

## 2. Data stored on your NAS

All application data stays in `/Volume1/DockerAppData/codeserverdocker/`:

| Data | Where | Why |
|---|---|---|
| Editor settings, keybindings, installed extensions | `data/` | Personalise the IDE |
| Workspace files you create or open | `data/` | Your projects |
| Access password hash, session tokens | `data/` | Authentication |
| Logs | `data/` | Troubleshooting |

No file, keystroke or session content is sent to the publisher or TerraMaster.

## 3. Data sent off your NAS

Outbound traffic happens only for features you use:

- **Extension marketplace / VSIX downloads** — when you search for or install extensions.
- **Git remotes and package registries** — when you run git, npm, pip or similar inside the terminal.
- **Update checks** — code-server may query GitHub for release information.

The web UI loads its own static assets from the container; no third-party CDN or
analytics script is included.

The publisher collects no telemetry, analytics or crash reports and operates no
server that receives data from this application.

## 4. Third parties

| Recipient | Data | Purpose |
|---|---|---|
| Extension marketplace (Open VSX / VSIX URL you configure) | Extension name, HTTP metadata | Install extensions |
| Git hosts / registries you configure | Repository URL, credentials you supply | Source control and packages |
| GitHub | HTTP metadata | Update checks |

No data is sold or shared for advertising.

## 5. Retention

Data is kept while the application is installed and you keep it. Deleting
individual files removes them immediately; deleting
`/Volume1/DockerAppData/codeserverdocker/` removes everything.

## 6. Your rights

- **Access / portability** — your files are ordinary files under the data
  directory and can be copied at any time.
- **Rectification** — edit files and settings in the IDE.
- **Erasure** — delete files, or the whole data directory.
- **Restriction** — disable extensions, update checks and network access to
  specific hosts from the IDE settings.

The publisher never receives your data, so requests are handled locally by you.

## 7. Security measures

- Container runs as uid 1000 (non-root), no privileged mode, no host network, no
  Docker socket access.
- Data directory is writable only by that user.
- The IDE is served over plain HTTP on your LAN and protected by a password; if
  exposed to the internet, place it behind HTTPS and a VPN or reverse proxy.

## 8. Deletion channel

App Center → uninstall the application **and** remove
`/Volume1/DockerAppData/codeserverdocker/`. There is no server-side copy to
delete.

## 9. Changes

Material changes are published in this file and noted in the package changelog
with the version that introduced them.

## 10. Contact

Open an issue at <https://github.com/Moechz/codeserver/issues>.
