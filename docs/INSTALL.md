# FrpDeck 安装与更新

VPS、frps、Lucky、域名和证书的完整步骤见官网[部署教程](https://frpdeck.playsong.cn/guide.html)。本页只说明安装、升级和下载校验。

## Docker Compose 安装

1. 在 NAS 上新建专用目录（例如 `frpdeck`），将发布页的 `docker-compose-1.7.1.yaml` 下载到该目录，并改名为 `docker-compose.yaml`。
2. 配置中的 `./data:/data` 会把数据保存在该目录的 `data` 子目录中。如需放到其他位置，请改为 NAS 上的持久目录，不要使用临时目录。
3. 默认端口映射为 `15566:5000`。15566 已被占用时，只修改冒号左侧的端口。
4. 在该目录执行：

```sh
docker compose pull
docker compose up -d
docker compose logs --tail 80 frpdeck
```

5. 在浏览器中打开 `http://NAS地址:15566`，输入日志中的初始密码（也保存在 `data/initial-password.txt`），设置管理员账号和至少 8 位的密码。

配置中的镜像为 `registry-1.docker.io/666uos/frpdeck:latest`。镜像名前的 `registry-1.docker.io/` 可避免 NAS 的镜像加速器返回旧版本；NAS 无法访问 Docker Hub 时，请删除该前缀，使用 NAS 自带的镜像加速。

## 飞牛 fnOS

FrpDeck 已提交飞牛应用中心，目前处于内测阶段。也可以在飞牛应用中心的「手动安装」中选择发布页的 `FrpDeck-1.7.1-fnos.fpk` 安装。安装时可在向导中选择访问端口（默认 15566），端口已被其他应用使用时会提示更换。安装后从飞牛桌面打开 FrpDeck，初始密码在 `frpdeck-fnos` 容器的日志中。

## 升级

升级前请备份整个 `data` 目录，其中保存了管理员、线路、安装编号和授权。

- **Compose**：保留 `data` 目录，在配置目录执行 `docker compose pull` 和 `docker compose up -d`。配置中的镜像固定在某一版本（以 `:1.6.1`、`@sha256:…` 等结尾）时，上述命令无法更新，请先将 image 改为 `registry-1.docker.io/666uos/frpdeck:latest`。升级后登录，确认版本号、线路和授权正常；出现问题时，用备份的 `data` 目录和上一版本的镜像恢复。
- **飞牛**：从应用中心更新。手动安装的版本，请先备份应用数据，在应用中心卸载旧版本后，手动安装新版本的飞牛安装包（.fpk 文件），原有数据会继续使用。安装未完成时（例如访问端口已被占用、镜像下载失败），飞牛会同时删除该应用的数据目录，请用备份恢复后重新安装。

## 下载校验

在下载目录执行以下命令。只下载了部分文件时，请比对对应文件的 SHA256。

```sh
# macOS
shasum -a 256 -c SHA256SUMS.txt

# Linux
sha256sum -c SHA256SUMS.txt
```
