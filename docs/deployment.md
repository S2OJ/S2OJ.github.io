# 部署与维护

本项目使用了 Docker Compose 进行部署，因此在开始前请在宿主机上安装 Docker 和 Docker Compose。

## 部署

将仓库中的 [`docker-compose.yml`](https://github.com/renbaoshuo/S2OJ/blob/master/docker-compose.yml) 拷贝至将要存放数据的目录下，执行以下命令即可拉取镜像并启动服务：

```bash
docker-compose up -d
```

> 请注意，这将从 `git.m.ac` 中拉取镜像，如果到达此站点的网络不畅，可以将文件中的 `git.m.ac/baoshuo` 替换为 `ghcr.io/renbaoshuo` 再重试。

## 开发环境部署

仓库中存放了专门用于开发环境的 Docker Compose 配置文件（[`docker-compose.development.yml`](https://github.com/renbaoshuo/S2OJ/blob/master/docker-compose.development.yml)），可以通过以下命令启动：

```bash
docker-compose -f docker-compose.development.yml up -d --build
```

启动时会自动进行镜像构建，这个过程可能会从中国大陆以外的地区下载大约 1 GiB 的数据，请耐心等待。

启动完成后访问 `http://localhost` 即可查看本地站点。

## 更新

执行以下命令：

```bash
docker-compose pull
docker-compose up -d
```

## 备份

所有数据均会存放在与 `docker-compose.yml` 同级的 `uoj_data` 目录下。如果需要备份，可以将此目录和 `docker-compose.yml` 打包并复制到其他地方。

## 恢复

将备份文件中的 `uoj_data` 目录和 `docker-compose.yml` 解压出来，然后执行以下命令：

```bash
docker-compose up -d
```

## 针对校内用户

校内的本地部署文件是 `docker-compose.local.yml`，需要在 `docker-compose` 后指定配置文件，如：

```bash
docker-compose -f docker-compose.local.yml up -d
```

### 更新

```bash
docker-compose -f docker-compose.local.yml pull
docker-compose -f docker-compose.local.yml up -d
```

### 备份

所有数据均会存放在与 `docker-compose.local.yml` 同级的 `uoj_data` 目录下。如果需要备份，可以将此目录和 `docker-compose.local.yml` 一并打包并复制到其他地方妥善保存。
