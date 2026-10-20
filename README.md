# Komari maintenance fork

Based on upstream Komari **1.5.1**, commit `f0cc0fba38ce161935372633cae5248c934e2b23`.
Maintained by [yinpengfei](https://github.com/yinpengfei). Upstream: [komari-monitor/komari](https://github.com/komari-monitor/komari).
Original MIT license and copyright notices are retained.

## Repositories

- [Server](https://github.com/yinpengfei/komari/tree/maint/1.5.1)
- [Frontend](https://github.com/yinpengfei/komari-web/tree/maint/1.5.1), based on the upstream server release's `1.5.0-fix1` frontend.
- [Agent](https://github.com/yinpengfei/komari-agent/tree/maint/1.5.1)
- [Server releases](https://github.com/yinpengfei/komari/releases)
- [Privacy review](PRIVACY_REVIEW.md)

## Installation / 安装

This fork must publish its own release assets before installation works. Do not use upstream binaries expecting these changes.
本仓库需先发布自己的二进制版本；上游二进制不包含本分支的隐私改动。

```sh
curl -fsSL https://raw.githubusercontent.com/yinpengfei/komari/refs/heads/maint/1.5.1/install-komari.sh -o install-komari.sh
# Review the downloaded script, then run it / 检查脚本后运行
sudo bash install-komari.sh
```

The Standard edition downloads from `yinpengfei/komari`. The installer also offers an explicitly selected third-party Lite edition, which is outside this review.
标准版从本仓库下载。安装器中主动选择的第三方 Lite 版不在此次审查范围内。
Docker images, once published: `ghcr.io/yinpengfei/komari:<release-tag>`.

## Privacy defaults / 隐私默认设置

New installations disable online GeoIP and automatic third-party market catalog requests. Existing saved settings are retained: disable GeoIP manually or choose local MMDB, and review market sources.
新安装默认关闭在线 GeoIP，主题和插件市场的上游源默认不启用。已有数据库的设置不会自动覆盖，请手动关闭 GeoIP 或选择本地 MMDB，并检查市场源。
Office Online preview is disabled because it shares a temporary download URL and file content with Microsoft. Download Office files to view them locally.
Office 在线预览已关闭，请下载后本地查看。
