# FrpDeck 安装与更新

适用于 **1.8.7**。其他 NAS 和 Linux 使用 Docker / Compose；飞牛用户也可从应用中心安装。[官网教程](https://frpdeck.playsong.cn/guide.html)另有服务器、域名和线路设置步骤。

## Docker Compose 安装

NAS 先安装并启动自带的 Docker 管理应用；Linux 需已安装 Docker Engine 和 Compose 插件。镜像支持 amd64、arm64 和 arm/v7。

### 1. 准备数据目录

在 NAS 上创建 `frpdeck/data` 文件夹，记下文件管理中显示的完整路径。它用于保存账号、线路和授权等数据，更新时必须保留。

| 平台 | 新建 Compose 项目的位置 | 数据目录示例 |
|---|---|---|
| 群晖 DSM 7.2+ | Container Manager → 项目 → 新增 | `/volume1/docker/frpdeck/data` |
| 威联通 | Container Station → 应用程序 → 创建 | `/share/Container/frpdeck/data` |
| 飞牛 fnOS | Docker → Compose → 新增项目 | `/vol1/1000/docker/frpdeck/data` |
| 极空间 | Docker → Compose → 添加项目 | 在文件夹映射中选中目录，复制显示的真实路径 |
| 绿联 UGOS Pro | Docker → 项目 → 创建 | `/volume1/docker/frpdeck/data` |
| Unraid | Compose Manager 插件 | `/mnt/user/appdata/frpdeck/data` |
| TrueNAS SCALE 24.10+ | 应用 → 发现应用 → 通过 YAML 安装 | `/mnt/存储池名/apps/frpdeck/data` |
| OpenMediaVault 7 | 服务 → Compose → 文件 → 添加 | `/srv/dev-disk-by-uuid-磁盘ID/docker/frpdeck/data` |
| Linux | 在项目目录运行 Compose 命令 | `/opt/frpdeck/data` |

卷号、账号编号和磁盘 ID 因设备而异，请使用实际路径。旧版 TrueNAS 的 Kubernetes 应用不适用此教程。

### 2. 保存配置并启动

在 NAS 的 Docker 界面新建 Compose 项目，粘贴下列配置，把 `./data` 换成上一步的完整路径，再部署项目。

```yaml
version: "3"
services:
  frpdeck:
    image: registry-1.docker.io/666uos/frpdeck:latest
    container_name: frpdeck
    restart: always
    network_mode: bridge
    ports:
      - "15566:5000"
    volumes:
      - ./data:/data
      - /var/run/docker.sock:/var/run/docker.sock:ro
    environment:
      - TZ=Asia/Shanghai
```

- **访问端口**：默认 15566。被占用时只改 `15566:5000` 左侧的数字，右侧保持 5000。群晖宿主机不要使用 DSM 已占用的 5000、5001。
- **数据路径**：只改 `./data:/data` 左侧，右侧保持 `/data`。命令行部署可保留 `./data`，表示配置文件旁的 `data` 文件夹。
- **Docker 接口**：`docker.sock` 用于授权、容器发现与迁移。即使写有 `:ro`，应用仍能通过 Docker API 管理容器。删除此挂载后可使用基础功能，但无法激活授权、发现或迁移容器。

**Linux 命令行**：先创建目录，将配置保存为 `/opt/frpdeck/docker-compose.yml`，再执行启动命令。以下操作需要目录写入权限和 Docker 操作权限，按系统要求使用 sudo。

```sh
mkdir -p /opt/frpdeck/data
cd /opt/frpdeck
```

保存好配置后，在同一目录执行：

```sh
docker compose config -q
docker compose pull frpdeck
docker compose up -d frpdeck
docker compose logs --tail 80 frpdeck
```

`latest` 随正式版更新。需要固定 1.8.7 时，把镜像末尾改为 `:1.8.7`；以后升级前再改成目标版本。无法直连 Docker Hub 时，可去掉 `registry-1.docker.io/`，使用 NAS 已配置的镜像加速，安装后核对版本。

### Docker 命令安装（可选）

已创建 `/opt/frpdeck/data` 时，也可用以下命令安装。NAS 用户应替换为自己的数据路径。此方法与 Compose 二选一。

```sh
docker run -d --name frpdeck --restart=always --network bridge \
  -p 15566:5000 \
  -v /opt/frpdeck/data:/data \
  -v /var/run/docker.sock:/var/run/docker.sock:ro \
  -e TZ=Asia/Shanghai \
  registry-1.docker.io/666uos/frpdeck:latest
```

## 首次登录

1. 打开 `http://NAS的局域网IP:15566`。改过端口时，使用新端口。
2. 在 Docker 界面查看 `frpdeck` 容器日志，找到初始密码；也可打开数据目录中的 `initial-password.txt`。重启后请使用最新生成的密码。
3. 输入初始密码，设置管理员用户名和至少 8 位的密码。完成后，初始密码文件会删除，之后使用自己的账号登录。
4. 在「账号安全」中按需开启两步验证，并保存恢复码。

没有通用默认管理员密码。添加服务时填写 NAS 或应用所在设备的局域网 IP；bridge 网络中的 `127.0.0.1` 指向 FrpDeck 容器本身。

## 飞牛应用中心

在飞牛应用中心搜索 **FrpDeck**，按向导安装；默认访问端口为 15566。1.8.7 起，新安装可在向导中设置 FrpDeck 管理员账号和密码，安装后从飞牛桌面打开并直接登录，无需查找初始密码。已有账号的升级保持原账号；尚未初始化的旧安装可在应用设置中补填首次账号。更新也通过应用中心完成，版本以应用中心实际显示为准。官网与公开仓不提供独立 FPK 下载。

飞牛应用中心安装可联网领取 **60 天专业版体验**，从首次成功领取时开始计算；通用 Docker / Compose 部署不提供体验。基础版永久免费，1 条线路，服务数量不限。本应用含付费功能，收费与飞牛官方无关。

体验到期固定一条保留线路运行，其余停用但配置保留，基础版不可切换。可提前选择；未选时优先已启用的默认线路，再选最早添加且启用的线路；全部关闭则保持关闭。已购买的永久授权不受影响。

## 完整备份

**配置导出不能代替完整数据备份**：导出文件不包含管理员密码、两步验证、部分探针凭据及网关连接信息。

升级前按以下顺序操作：

1. 记下当前版本，保存 Compose 文件及端口、环境变量、挂载设置。
2. 从 NAS 本地管理界面停止 FrpDeck。若远程连接经过 FrpDeck 的 SSH 隧道，停止后该连接会中断。
3. 复制整个数据目录到独立备份位置，包含隐藏文件，并保留权限与所有权。不要只复制 `frpdeck.json`：授权、安装标识、历史流量、探针密钥及网关文件都要保留。
4. 确认备份成功后，启动原容器或继续更新。备份中含有凭据，请妥善保管。

独立部署的 frps、Lucky、fn-knock 配置需另行备份，不包含在 FrpDeck 数据目录中。

### 命令行备份（可选）

以下仅适用于配置名为 `docker-compose.yml`、数据使用 `./data:/data` 的项目。在原项目目录执行；NAS 绝对路径挂载请按上面的图形界面步骤备份实际目录。

```sh
backup="../frpdeck-backup-$(date +%Y%m%d-%H%M%S).tar.gz"
docker compose stop frpdeck &&
  tar -czpf "$backup" docker-compose.yml data &&
  tar -tzf "$backup"
```

确认命令成功，归档中包含配置和数据后，执行 `docker compose start frpdeck` 恢复运行。备份失败时检查路径、空间和权限，不要继续升级；需要恢复服务时也可执行上述启动命令。

## 更新与回退

完成备份后，**保留原数据目录和部署参数**。固定版本或 digest 的配置，先把 `image` 改为目标版本。

- **Compose**：在原项目目录执行下列命令；NAS 图形界面则先拉取新镜像，再重新部署原项目。
- **Docker 命令安装**：保存原安装命令，备份后用 `docker pull` 拉取该命令中的镜像。停止并删除旧容器，再用原命令创建；保留原端口、环境变量和数据挂载，只删除容器，不删除宿主机数据目录。NAS 也可在 Docker 界面完成这些操作。
- **飞牛应用中心**：通过应用中心更新，不要用通用 Compose 命令管理 `frpdeck-fnos`。

```sh
docker compose pull frpdeck
docker compose up -d frpdeck
docker compose ps
docker compose logs --tail 80 frpdeck
```

只拉取镜像或只重启容器都不会完成更新。更新后刷新页面，检查版本、登录、线路、服务、授权和探针上报。

**1.8.7 升级提示**：从 1.8.4 / 1.8.5 升级只需更新主程序，无需重装探针。仍用 1.8.0 NAS 探针时，按应用内指引保留密钥、回调和参数，拉取新镜像并重建探针。

**回退**：停止主程序，另存当前数据，再恢复升级前的完整数据、部署配置和对应镜像。回退至 1.8.1 或更早版本必须恢复升级前备份，仅换回旧镜像可能无法登录。

**授权恢复**：保留原数据和 Docker 环境，更新或重建容器不会改变授权。清空数据、更换 NAS 或重装 Docker 后，请先恢复原数据，必要时联系作者审核换绑；不会自动释放旧设备名额。

## 常见问题

| 现象 | 检查方法 |
|---|---|
| 面板打不开 | 查看容器日志，确认数据目录可写、端口映射正确，NAS 防火墙允许访问 |
| 更新后仍是旧版 | 固定版本先改标签，再拉取镜像并重新部署；检查镜像加速器是否缓存旧版 |
| 找不到初始密码 | 查看正确容器的最新日志及实际数据目录；已有管理员的安装不会再次生成初始密码 |
| 忘记密码或丢失两步验证 | 在数据目录新建空文件 `reset-admin`，重启容器，用新初始密码重新设置。此操作会关闭两步验证和「仅允许局域网访问」，旧登录失效，线路与服务保留 |
| 授权无法激活 | 检查 Docker socket 挂载和权限，不要清空数据尝试重新领取 |
| 线路不通或域名显示 502 | 在线路页点击「测试」，核对 frps 地址、token、应用的局域网 IP 和端口 |

[版本记录](../CHANGELOG.md) · [官网部署教程](https://frpdeck.playsong.cn/guide.html) · [问题反馈](https://github.com/songdone/FrpDeck-Release/issues)
