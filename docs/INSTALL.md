# FrpDeck 1.6.0 安装与更新

[网页完整教程](https://frpdeck.playsong.cn/guide.html) · [8 页 PDF](https://github.com/songdone/FrpDeck-Release/releases/download/v1.6.0/FrpDeck-getting-started-1.6.0.pdf)

## 一、先准备网络与服务

常规部署需要 NAS 的 Docker 环境，以及 VPS 上的 frps、Lucky 和指向 VPS 的域名。FrpDeck 镜像已内置 frpc。先按照完整教程配置 VPS、Lucky、证书和域名，再在面板添加线路及服务。

如果家庭网络有可用公网 IPv6，可以使用 NAS 上的 Lucky，选择不经 VPS 的本地线路。80/443 不可用或使用 NAT VPS 时，按教程填写实际映射与外网访问端口；非 443 端口的访问链接需要包含该端口。

## 二、Docker Compose 安装

1. 在 NAS 的持久存储中建立专用目录，例如 `frpdeck`，将发布页的 `docker-compose-1.6.0.yaml` 下载并重命名为 `docker-compose.yaml`。
2. 配置中的 `./data:/data` 会把数据存入当前专用目录。需要其他路径时，改成 NAS 上的实际持久目录，不要使用临时目录。
3. 默认映射 `15566:5000`。15566 已占用时只修改冒号左侧宿主端口，并使用修改后的端口打开面板。
4. 在该目录执行：

```sh
docker compose pull
docker compose up -d
docker compose logs --tail 80 frpdeck
```

首次日志中显示初始密码。也可在 NAS 上查看专用目录里的 `data/initial-password.txt`。打开 `http://NAS地址:15566`，输入初始密码并设置管理员账号与至少 8 位的新密码。首次设置成功后初始密码失效。

随附配置使用 `666uos/frpdeck:1.6.0`。若 NAS 镜像源不可用，检查 NAS 的镜像源和网络设置；不要把陌生脚本或未经验证的镜像作为替代。

### Docker 权限

容器默认以 root 运行，挂载 `/var/run/docker.sock:/var/run/docker.sock:ro`。此接口具备容器管理能力，`:ro` 不限制 Docker API 的停止、启动等操作。用于容器发现与旧 frpc 迁移，也是 FD2 读取引擎编号的必要接口；长期仅使用基础版时可删除该挂载。数据目录应限制为维护者可访问。

### 添加线路与服务

- 在线路页添加 VPS 的 frps 地址、端口、Token、Lucky API 地址及 OpenToken、域名后缀，按需填写外网访问端口。
- 使用线路“测试”检查隧道、域名、Lucky 与 HTTPS 配置。
- 在服务页填写 NAS 内网服务地址、端口与域名前缀，选择使用的线路。
- 开启服务后从外网访问该线路对应域名。更换线路后使用新线路的域名；各线路拥有独立域名。

基础版可管理 1 条线路，服务数量不限。付费功能在授权页激活。购买与售后通过 [作者公开联系渠道](https://t.me/Play_6uos) 对接，不要把机器码或授权码发到公开 Issues。

## FD2 与活动资格首次兑换

在 FrpDeck 的“授权”页输入永久 FD2，可本地离线激活。输入 FDV 资格码时，需要首次联网兑换；也可主动点击“参加开放活动”。只有作者开启且符合窗口、总名额与固定资格期限时才会签发，当前所有活动关闭。

兑换成功后 App 保存永久码，不因活动结束失效。相同安装与引擎可用原已兑换资格码重试找回；清空数据产生新安装编号不能再次领取，应恢复原数据或联系作者。1.6.0 不再兼容 FD1，开发者本人已迁移新码；持有旧码者应先联系作者再升级。

## 三、飞牛 fnOS 手动安装测试包

`FrpDeck-1.6.0-fnos.fpk` 是**尚未通过官方应用中心审核的测试包**。本版本的实际设备验证范围见发布说明，仍不代表所有架构与完整安装、业务、卸载、恢复流程均已验收。

1. 确保飞牛 Docker 服务已安装且可用，先备份现有 FrpDeck 数据。
2. 从本仓库 Releases 下载 FPK 及 `SHA256SUMS.txt`，核对校验值。
3. 在飞牛应用中心的手动安装入口选择 FPK，查看权限说明后按提示安装。
4. 默认入口为 `http://NAS地址:15566`。首次设置密码可从 `frpdeck-fnos` 容器日志或应用运行时数据目录的 `initial-password.txt` 获取。

FPK 固定拉取 FrpDeck 1.6.0 镜像摘要，需要能访问对应镜像源。数据位于应用运行时目录的 `data` 子目录；升级前从面板导出备份，同时备份整个数据目录。平台卸载是否清理数据，以实际设备行为为准，不能假定卸载后授权和配置必然保留。

## 四、升级与恢复

### Compose

先从面板导出配置，并备份整个 `data` 目录，包含安装标识和授权。将镜像改成希望升级到的固定版本，再执行：

```sh
docker compose pull
docker compose up -d
docker compose logs --tail 80 frpdeck
```

保留原数据挂载路径。查看界面版本、登录、线路和授权是否正常；出现问题时停止应用，恢复备份数据与此前镜像版本。升级与重建不要删除数据卷。

### FPK

等待相应测试或正式包发布后，按飞牛平台的升级入口操作。当前 FPK 的生命周期与授权恢复仍需设备验证，重要部署先使用备份或独立测试环境。

## 五、下载校验

在安装包、教程和 `SHA256SUMS.txt` 所在目录执行：

```sh
# macOS
shasum -a 256 -c SHA256SUMS.txt

# Linux
sha256sum -c SHA256SUMS.txt
```

校验文件列出本次发布的全部资产；只下载其中部分时，可比对对应文件的 SHA256，或下载全部资产后运行整份清单校验。
