# Komari 维护版

当前正式版本：**1.5.2**（2026-10-21 已完成 GitHub Actions 打包）。基于上游 Komari 1.5.1，包含本 Fork 的隐私和维护链接调整；原始 MIT 许可证与版权声明保留。

- **[安装、升级、备份恢复与后续发版指南](INSTALL_MAINTENANCE.md)**
- [服务端 1.5.2 下载](https://github.com/yinpengfei/komari/releases/tag/1.5.2)（8 个平台）
- [Agent 1.5.2 下载](https://github.com/yinpengfei/komari-agent/releases/tag/1.5.2)（14 个平台）
- [隐私审查与升级设置](PRIVACY_REVIEW.md)

服务端镜像：`ghcr.io/yinpengfei/komari:1.5.2`。Agent 镜像：`ghcr.io/yinpengfei/komari-agent:1.5.2`。两者均提供 amd64、arm64，以及随正式版本更新的 `latest` 标签。

## 快速安装

推荐按[指南](INSTALL_MAINTENANCE.md)使用固定版本的 Docker Compose，或在支持 systemd 的 Linux 上运行安装脚本：

```sh
curl -fsSL https://raw.githubusercontent.com/yinpengfei/komari/1.5.2/install-komari.sh -o install-komari.sh
sudo bash install-komari.sh
```

选择标准版（Standard）、稳定版（Stable）。该模式下载本 Fork 的最新正式版，目前为 1.5.2；如需严格固定版本，请使用指南中的 Compose 或指定附件下载。Lite 版属于第三方维护，不在本 Fork 的审查范围内。

## 隐私和迁移

新安装默认关闭在线 GeoIP；上游主题/插件市场源默认不启用。已有数据的设置不会自动覆盖，请按指南检查 GeoIP、市场源、扩展、通知及 OAuth。Office 在线文件预览已禁用，Agent 第三方公网 IP 探测默认关闭。

迁移到本维护版需要实际更换程序/镜像，旧的上游程序不会因为仓库链接变化而自动迁移。升级前独立备份数据、程序版本及启动配置。脚本自带的二进制备份不是完整数据备份。

## 源码

- [服务端维护分支](https://github.com/yinpengfei/komari/tree/maint/1.5.1)
- [前端维护分支](https://github.com/yinpengfei/komari-web/tree/maint/1.5.1)
- [Agent 维护分支](https://github.com/yinpengfei/komari-agent/tree/maint/1.5.1)
- [上游服务端](https://github.com/komari-monitor/komari)

`maint/1.5.1` 是开发基线分支名，当前正式发行版为 1.5.2。默认分支继续保留 `main`；最新维护文档在以上维护分支中。已发布标签和安装包保持不变。
