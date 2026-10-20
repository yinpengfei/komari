# 安装与维护：服务端 1.5.2 / Agent 1.5.3

更新日期：2026-10-21。本文对应 **yinpengfei 服务端 1.5.2 / Agent 1.5.3**，基于上游服务端 1.5.1 加入隐私与维护链接调整。

## 版本与下载入口

| 组件 | 正式版本 | 下载 |
|---|---|---|
| 服务端（含前端） | 1.5.2 | [8 个平台安装包](https://github.com/yinpengfei/komari/releases/tag/1.5.2) |
| Agent | 1.5.3 | [14 个平台安装包](https://github.com/yinpengfei/komari-agent/releases/tag/1.5.3) |
| 服务端镜像 | 1.5.2 | `ghcr.io/yinpengfei/komari:1.5.2` |
| Agent 镜像 | 1.5.3 | `ghcr.io/yinpengfei/komari-agent:1.5.3` |

两种镜像均提供 amd64、arm64，并已发布 `latest`。固定版本部署分别使用服务端 `:1.5.2`、Agent `:1.5.3`，`latest` 会随将来的正式发版变化。

源代码维护分支仍为 `maint/1.5.1`，名称表示开发基线，不代表当前发行版本停留在 1.5.1。服务端与 Agent 的默认分支保留 `main`，前端默认分支保留 `radix`；查看最新维护文档请使用本文的维护分支链接。已发布的 `1.5.2` 标签和安装包不随文档更新而改写。

## 服务端：Docker Compose

新建部署目录，将下面内容保存为 `compose.yaml`：

```yaml
services:
  komari:
    image: ghcr.io/yinpengfei/komari:1.5.2
    container_name: komari
    restart: unless-stopped
    ports:
      - "25774:25774"
    volumes:
      - ./data:/app/data
    environment:
      TZ: Asia/Shanghai
```

在同一目录启动：

```sh
mkdir -p data
docker compose pull
docker compose up -d
docker compose logs --tail=100 komari
```

访问 `http://服务器地址:25774`，按首次安装页面创建管理员。已有数据目录会沿用原账号与设置。通过域名使用时，配置反向代理和 HTTPS，并支持 WebSocket。

数据保存在部署目录的 `./data`，应长期保留。重建容器时继续挂载相同目录。

## 服务端：Linux systemd 安装脚本

```sh
curl -fsSL https://raw.githubusercontent.com/yinpengfei/komari/1.5.2/install-komari.sh -o install-komari.sh
sudo bash install-komari.sh
```

菜单中选择 **标准版 / Standard → 稳定版 / Stable**。标准版从 `yinpengfei/komari` 的最新正式 Release 下载，目前为 1.5.2；脚本链接固定到 1.5.2 不等于二进制下载永远锁定 1.5.2。需严格固定版本时使用上面的 Compose 配置或下面的手动下载。

本 Fork 未发布 `Snapshot-*` 版时不要选择快照通道。菜单中的 Lite 版属于其他维护者，不包含本 Fork 的改动。

默认安装位置：

| 项目 | 默认位置 |
|---|---|
| 二进制 | `/opt/komari/komari` |
| 工作目录 | `/opt/komari` |
| 持久化数据 | `/opt/komari/data` |
| systemd 服务 | `komari.service` |
| 默认端口 | `25774`（安装时可选择） |

常用操作：

```sh
sudo systemctl status komari --no-pager -l
sudo journalctl -u komari -n 100 --no-pager
sudo systemctl restart komari
```

## 服务端：手动固定版本下载

以下为 Linux amd64 示例；ARM64 等平台请在 Release 中选择匹配的附件：

```sh
curl -fL https://github.com/yinpengfei/komari/releases/download/1.5.2/komari-linux-amd64 -o komari
chmod 755 komari
./komari server -l 0.0.0.0:25774
```

程序在当前工作目录使用 `./data`。正式运行时应选择固定工作目录，避免每次启动产生另一份数据。

## Agent 安装

先在后台新增节点，取得该节点的 Token，将下面的地址和 Token 替换为自己的值。不要把真实 Token 写进公开文档。

### Linux / macOS 脚本

```sh
curl -fsSL https://raw.githubusercontent.com/yinpengfei/komari-agent/1.5.3/install.sh -o install-komari-agent.sh
sudo bash install-komari-agent.sh \
  --install-version 1.5.3 \
  --endpoint 'https://你的面板域名' \
  --token '替换为后台生成的TOKEN'
```

Linux systemd 默认服务名是 `komari-agent`。自定义安装位置或服务名时，后续维护命令也要使用对应值。普通用户安装可能使用 user service；macOS 使用 launchd。

### Windows

以管理员身份打开 PowerShell，下载并运行：

```powershell
Invoke-WebRequest 'https://raw.githubusercontent.com/yinpengfei/komari-agent/1.5.3/install.ps1' -OutFile .\install-komari-agent.ps1
& .\install-komari-agent.ps1 --install-version 1.5.3 --endpoint 'https://你的面板域名' --token '替换为后台生成的TOKEN'
Get-Service komari-agent
```

默认目录为 `%ProgramFiles%\Komari`，服务由 NSSM 管理；缺少 NSSM 时安装器会从 `nssm.cc` 下载。若本机策略禁止执行脚本，按自己的管理策略允许已检查的脚本，或从 Release 下载二进制后自行配置服务。

### Agent 隐私选项

| 需求 | 参数 / 行为 |
|---|---|
| 使用指定 IP | `--custom-ipv4 'IP地址'` / `--custom-ipv6 'IP地址'` |
| 默认自动获取 | IPv4 查询 HTTPS ipify；IPv6 优先读取网卡公网候选，缺失才查询 IPv6 ipify |
| 显式网卡模式 | `--get-ip-addr-from-nic`，保留原行为：可能读到私网地址，找到任意一种地址就返回 |
| 关闭第三方公网 IP 查询 | `--allow-external-ip-lookup=false`；仍可读取本地网卡公网 IPv6 |
| 固定版本并关闭自动更新 | 添加 `--disable-auto-update` |
| 不需要网页终端、远程命令和文件控制 | 添加 `--disable-web-ssh` |

Agent 1.5.3 默认允许缺失地址通过 ipify 补齐（1.5.2 默认关闭）。手动 IP 优先；自动 IPv6 只选择已启用、非回环网卡上的公网候选，过滤本地及特殊用途地址。多个候选按网卡名、地址数值稳定选择，遵守现有网卡筛选规则（include 显式匹配优先于 exclude）。网卡地址不保证互联网连通性或与实际出口一致。IPv6 查询失败时留空，下一次基础信息更新重试，默认每 5 分钟。

关闭外部查询也可使用环境变量 `AGENT_ALLOW_EXTERNAL_IP_LOOKUP=false` 或 JSON `"allow_external_ip_lookup": false`，升级后已有 false 仍生效；需要恢复查询时修改实际生效的配置。配置优先级为默认值 < 命令行 < 环境变量 < JSON。仅升级 Agent 即可修复默认 IP 显示，无需升级服务端。完整选址规则见 [Agent 参数说明](https://github.com/yinpengfei/komari-agent/blob/maint/1.5.1/readme.md#153-的-ip-获取规则)。

面板仍可观察 Agent 的连接源 IP。安装版本固定为 1.5.3 后，若不关闭自动更新，Agent 仍会在启动和每 6 小时检查本 Fork 的同一 `1.5.*` 版本线。容器内不执行二进制自更新，应拉取和重建镜像。

## 从已有版本升级

先备份，再按需更新服务端与 Agent。本次 Agent 1.5.3 的 IP 修复无需更新服务端。首次迁移到本 Fork 时，必须使用本 Fork 的镜像或安装包；已运行的上游 Agent 不会因为仓库链接改动就自动切换更新源。

### Docker Compose

在原部署目录，将 `compose.yaml` 的镜像改为 `ghcr.io/yinpengfei/komari:1.5.2`，保留原数据挂载、端口和配置，然后执行：

```sh
docker compose pull
docker compose up -d
docker compose logs --tail=100 komari
```

### systemd 安装

使用本维护版脚本选择“升级”，并选标准版、稳定版。**脚本内的升级备份仅针对二进制，不是完整数据备份；还会清理历史二进制备份。** 请先按下一节将数据独立备份到安装目录之外。

从上游迁移时，先用 `systemctl cat komari` 确认旧服务的程序路径、工作目录和运行参数。路径与默认值不一致时，按原路径升级，不要另外启动一份空数据的服务。

Agent 可重新运行本 Fork 的安装脚本，并显式传入原面板地址、Token、服务名和其他原有选项。安装器可能重新生成服务配置；先保存旧参数，再执行重装。

## 备份与恢复

完整备份应包含：数据目录、服务配置/Compose 文件，以及对应版本的二进制或镜像标签。备份中含账号和访问凭证，应保存到受控位置。

默认 systemd 安装的示例（以 root 运行；在维护窗口停服）：

```sh
backup_dir="/opt/komari-backups/$(date +%Y%m%d-%H%M%S)"
mkdir -p "$backup_dir"
chmod 700 "$backup_dir"
systemctl cat komari > "$backup_dir/komari.service.txt"
systemctl stop komari
tar -czf "$backup_dir/data.tar.gz" -C /opt/komari data
cp /opt/komari/komari "$backup_dir/komari"
systemctl start komari
```

确认归档可读取、文件大小正常，再继续升级。任何备份步骤失败时先处理错误，并检查服务是否已重新启动。

Compose 的默认 `./data` 挂载可在部署目录执行 `docker compose stop komari` 后，用 `tar -czf 数据备份文件.tar.gz data compose.yaml` 备份，再执行 `docker compose start komari`。其他卷布局请备份实际挂载位置。

恢复或回退应先停服，保留当前数据到另一个目录，再把备份恢复到原挂载/工作目录，配合备份对应的旧程序启动。不要只降级二进制后让旧程序直接读取升级后的数据库；数据库迁移可能不向后兼容。本文不自动执行清理或回退。

## 升级后的检查

```sh
curl -fsS http://127.0.0.1:25774/ping
curl -fsS http://127.0.0.1:25774/api/version
```

按实际监听端口调整。随后确认后台版本为 1.5.2、账号能登录、原节点和历史数据存在、Agent 在线、日志无持续错误。

已有数据库不会被新默认值覆盖。检查并按需关闭在线 GeoIP，或改用本地 MMDB；检查主题/插件市场源、已安装扩展、自定义 Head/Body、通知和 OAuth。Office 在线预览已禁用，可下载文件后本地查看。

浏览器仍显示旧页面时，先确认加载的是默认维护版前端，而非此前安装的其他主题；再刷新页面并按需清理该站点的缓存/Service Worker。详细外部请求说明见 [隐私审查](PRIVACY_REVIEW.md)。

## 后续维护与发版

1. 在三个仓库各自的 `maint/1.5.1` 分支开发，保留原 `main`，不要直接把上游 main 同步到维护分支。
2. 前端变更提交并验证构建后，更新服务端 `.github/actions/build-frontend/action.yml` 的默认 ref 和 `.github/workflows/release.yml` 的 `frontend_ref`，都固定到审核过的前端提交。
3. 服务端与 Agent 分别创建新的正式 Release，目标选择对应维护分支。Agent 不勾选预发布。先确认仓库 Actions 已启用。
4. 发布会触发安装包和镜像构建。服务端包含固定前端，无需先独立发布前端主题包。检查 Actions 全部成功、Release 附件齐全、镜像版本标签与 `latest`，再部署。
5. 本次 Agent 的历史发布发生在 Actions 启用前，已通过现有 `snapshot.yml` 手动入口补打包成功。维护分支上的该手动入口**只重打包 1.5.2**，不创建 Snapshot 版本；不要把它当成其他版本的发布入口。今后的正常正式发版使用第 3 步。

历史 1.5.2 构建记录：[服务端](https://github.com/yinpengfei/komari/actions/runs/39454034246)、[Agent](https://github.com/yinpengfei/komari-agent/actions/runs/39455281125)。两次均成功；这不代表已经更新任何线上部署。
