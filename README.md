# Komari maintenance fork

Current stable release: **1.5.2**, published and built on 2026-10-21. Based on upstream Komari 1.5.1 with privacy and maintenance-link changes. Original MIT license and copyright notices are retained.

- **[Installation, upgrades, backup/restore, and release maintenance (Chinese)](INSTALL_MAINTENANCE.md)**
- [Chinese README](README_zh-cn.md)
- [Server 1.5.2 downloads](https://github.com/yinpengfei/komari/releases/tag/1.5.2): 8 platform binaries
- [Agent 1.5.2 downloads](https://github.com/yinpengfei/komari-agent/releases/tag/1.5.2): 14 platform binaries
- [Privacy review](PRIVACY_REVIEW.md)

Docker images (amd64/arm64): `ghcr.io/yinpengfei/komari:1.5.2` and `ghcr.io/yinpengfei/komari-agent:1.5.2`. The `latest` tags follow future stable releases; pin versions for controlled upgrades.

## Installation

For systemd Linux, download the installer from this fork:

```sh
curl -fsSL https://raw.githubusercontent.com/yinpengfei/komari/1.5.2/install-komari.sh -o install-komari.sh
sudo bash install-komari.sh
```

Select **Standard / Stable**. This downloads this fork's latest stable binary, currently 1.5.2; a pinned script URL does not pin its latest-release download. For exact-version installation, use the pinned Compose image or release asset described in the guide. The optional Lite edition is maintained by another project and is outside this review.

New installations disable online GeoIP and automatic third-party market catalog requests. Existing saved settings are retained: review GeoIP, market sources, extensions, notifications, and OAuth after upgrading. Third-party Office previews and default Agent public-IP lookups are disabled. Agent auto-update remains enabled unless explicitly disabled.

Back up the full data directory and deployment configuration before upgrading. An installer binary backup alone is not a data backup. Existing upstream programs must be replaced with this fork's binaries/images to receive these changes.

## Repositories

- [Server](https://github.com/yinpengfei/komari/tree/maint/1.5.1)
- [Frontend](https://github.com/yinpengfei/komari-web/tree/maint/1.5.1), based on the upstream server release's 1.5.0-fix1 frontend
- [Agent](https://github.com/yinpengfei/komari-agent/tree/maint/1.5.1)
- [Upstream server](https://github.com/komari-monitor/komari)

The maintenance branch remains `maint/1.5.1` (the development baseline); stable release version is 1.5.2. Server and Agent default branches remain `main`; the frontend default remains `radix`. Read current maintenance docs via the maintenance-branch links. Published tags and binaries are unchanged by documentation updates.
