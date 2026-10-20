# 隐私与维护链接审查

审查日期：2026-10-20。范围为本分支源码及发布工作流；不是对所有依赖、未来主题/插件、已部署程序的安全保证，也未对生产环境抓包。

## 审查基线

- 服务端：上游 1.5.1，`f0cc0fba38ce161935372633cae5248c934e2b23`。
- 前端：服务端 1.5.1 发布工作流指定的 `1.5.0-fix1`，`3324844cfa347f18c83435f1ccf5634df7e5b768`。
- Agent：上游 `828afafe7fb5b1b58c1232094e1e46320719016e`，使用 v2 协议；Agent 的版本号独立于服务端，并非名为 1.5.1 的 Agent 发布版。
- 三个维护仓库均在 `yinpengfei` 下，维护分支均为 `maint/1.5.1`。原始许可证与版权声明保留，Go module/import 路径保留（它们不是运行时上报地址）。

## 结果与已采取的措施

在检查的自有源码中未发现独立的隐藏遥测、广告统计 SDK 或把监控数据固定上传给原作者的逻辑；但存在以下明确外发路径：

| 行为 | 触发与发送的数据 | 本次处理 |
|---|---|---|
| 在线 GeoIP | 原默认启用 IPinfo；服务器 IP，以及登录通知触发时的登录者 IP，会作为查询参数发往第三方。可选 ip-api 使用 HTTP，geojs 使用 HTTPS。见 `utils/geoip/`、`web/api/client/uploadBasicInfo.go`、`database/accounts/sessions.go`。 | 新安装默认关闭。已有设置不覆盖。需地理位置时建议本地 MMDB；MMDB 首次下载/更新仍访问 GitHub，但不会发送待查询 IP。 |
| Agent 公网 IP 探测 | 原默认访问 Visa、Qualcomm、头条、html.zone、IP.sb、ipify 等服务；请求本身暴露源 IP。见 `monitoring/unit/ip.go`。 | 默认不请求外部 IP 服务；可显式配置 IP 或从网卡读取。主动开启 `--allow-external-ip-lookup` 后仅保留 HTTPS ipify。未指定 IP 时服务端仍可记录连接源 IP。 |
| Office 在线预览 | 前端会创建临时下载令牌，把文件下载 URL 交给微软 Office Online；微软能够读取文件内容。见前端 `src/pages/terminal/fileManagerApi.ts`。 | 禁用第三方预览，不再创建该令牌或发送 URL；下载后本地查看。 |
| 版本检测和更新 | 前端浏览器访问 GitHub Releases；Agent 启动和每 6 小时检查更新，必要时下载并替换二进制。GitHub 可看到请求源 IP、User-Agent；检查请求不包含面板监控数据。若配置 `GITHUB_TOKEN`，Agent 会向 GitHub API 发送该令牌。 | 全部改为本人的服务端/Agent Releases；浏览器明确省略 cookie 和 Referrer。Agent 可用 `--disable-auto-update` 关闭。 |
| README 外链资源 | 后台关于页面原远程加载 README，内含 hitscounter、第三方统计图片、宣传图片等；浏览器会向这些站点发请求。 | 后台改用随前端打包的本地说明，删除本 Fork README 中第三方图片、访问统计和推广内容。 |
| AI 发布说明 | 服务端工作流在配置 AI 凭证时，将提交、PR、文件差异发往配置的 AI API。仅发生在 CI，非面板运行时。 | 删除此外发路径，改用 GitHub 自带 release notes API；不使用 AI 密钥。前端翻译工作流改为手动、无 AI 模式，本地翻译脚本也需明确加 --ai 才可调用 AI。 |
| 主题和插件市场 | 打开市场时下载第三方目录，安装时下载包；预览图可能由浏览器访问第三方站点。插件/主题可扩展执行行为。 | 默认上游目录源禁用，保留管理员主动启用/添加源功能；没有把未 Fork 的市场地址改成不存在的 URL。 |
| 安装镜像/代理 | Agent shell 安装器原直连失败后自动尝试公共 GitHub 代理，第三方代理可看到请求并参与二进制分发。 | 移除自动代理回退；仅直连或明确配置的代理。Windows 安装器仍会按需从 nssm.cc 下载 NSSM。 |
| 通知、OAuth、Webhook、自定义脚本 | 仅配置并启用相关功能后向相应服务发送消息或认证数据；登录通知包含 IP、User-Agent，告警可能包含节点名称等。QQ 默认聚合地址为 login.qjqq.cn。 | 保留功能；部署者应选择信任的服务，按需关闭登录通知/通知渠道/SSO。 |
| Agent 向面板上报 | 硬件、系统、IP、运行指标、任务结果、请求的文件数据，及管理员请求的启动配置，发送给配置的面板 endpoint。远程命令、终端、文件访问属于产品功能。 | 保留监控功能；必须信任面板，使用 HTTPS。无需远控时使用 `--disable-web-ssh`。启动配置不应被视为可公开内容。 |
| DNS | 默认系统解析；显式配置自定义 DNS 时，失败回退可能使用内置公共 DNS 列表。 | 保留并披露；对 DNS 有严格限制时使用系统 DNS 和网络策略。 |

## 链接和构建

- 服务端标准版安装脚本、Agent shell/PowerShell 安装、Agent 自更新、后台版本检测、Docker Agent 命令、后台仓库链接已指向本人的仓库。
- 服务端前端构建源改为本人的 `komari-web`，固定经过检查的提交；不再跟随上游默认分支。
- 修复重建工作流从上游 main 加载本地 action 的问题；Go 版本由 go.mod 决定。
- 上游技术文档、许可证、第三方服务地址、第三方 Lite 版入口保留原地址并明确其用途。Lite 版不是本维护版，不在此次审查范围内。

## 升级现有部署

1. 必须构建和发布这三个维护分支的程序；旧二进制与旧前端不会因为修改仓库自动改变。
2. 在后台关闭 GeoIP，或改用本地 MMDB。已有数据库中启用的设置不会被强制覆盖。
3. 检查主题/插件市场源、已安装主题/插件、自定义 Head/Body、通知、OAuth、Webhook 和代理；这些配置可以引入新的外部请求。
4. Agent 默认不再探测外网 IP，服务端可能只看到 NAT/隧道出口地址；需显示指定 IP 时使用 `--custom-ipv4` / `--custom-ipv6`。选网卡模式时可能得到私网地址。
5. 本 Fork 在发布自己的 Release assets 和 GHCR 镜像前，新下载链接尚不可用于安装；不会默默回退到上游二进制。

## 验证范围

源码检索覆盖服务端 HTTP/网络调用、GeoIP/通知/OAuth/市场/JS 插件宿主、Agent 上报/更新/IP 探测/安装器、前端外链请求和 CI 工作流。构建和测试的最终结果见提交说明与本次交付报告；未部署到生产环境，未做运行时全链路抓包或完整依赖漏洞审计。

### 本次验证结果

- 前端：npm ci、TypeScript 检查和生产构建通过。
- 服务端：完整构建通过；utils/geoip、internal/config、web/api/admin 相关检查通过。
- Agent：完整构建通过；monitoring/unit（包含禁止默认第三方请求的回归测试）、update、cmd 和指定的 server 版本切换/上传测试通过。
- Agent server 全包测试中的 ICMP/TCP/HTTP 外网连通性用例因目标 DNS、IPv6 连通性和 Windows ICMP 支持失败，不能宣称全套测试通过。
- shell 安装脚本与 PowerShell 安装脚本语法检查通过，24 个工作流/action YAML 文件解析通过。
- 未发布 Release、镜像或更新生产部署；未做运行时抓包及完整依赖审计。
