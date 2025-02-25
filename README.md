# SearXNG Docker 配置指南

SearXNG 是一个注重隐私、可定制的元搜索引擎。本仓库包含使用 Docker 运行 SearXNG 的配置文件和说明。

## 快速开始

使用以下 Docker 命令运行 SearXNG：

```bash
docker run --rm \
    -d \
    -p 8080:8080 \
    -v "${PWD}/searxng:/etc/searxng" \
    -e "BASE_URL=http://0.0.0.0:8080/" \
    -e "INSTANCE_NAME=searxng" \
    registry.cn-hangzhou.aliyuncs.com/dockerhub_mirror/searxng:2025.2.20-28d1240fc
```

该命令说明：
- `-d`：以分离模式运行容器（在后台运行）
- `-p 8080:8080`：将容器的 8080 端口映射到主机的 8080 端口
- `-v "${PWD}/searxng:/etc/searxng"`：将本地 `searxng` 目录挂载到容器内的 `/etc/searxng`
- `-e "BASE_URL=http://0.0.0.0:8080/"`：设置 SearXNG 的基础 URL
- `-e "INSTANCE_NAME=searxng"`：设置实例名称
- 使用阿里云镜像仓库中的 SearXNG 镜像版本 2025.2.20-28d1240fc

## 目录结构

```
single/
├── README.md          # 本文档
└── searxng/           # 配置文件目录
    ├── settings.yml   # SearXNG 主要配置文件
    └── uwsgi.ini      # uWSGI 服务器配置
```

## 配置说明

### 主要配置文件

配置文件位于 `searxng` 目录中：

- `settings.yml`：SearXNG 的主要配置文件，包含搜索引擎设置、UI 选项等
- `uwsgi.ini`：uWSGI 服务器配置，控制 Web 服务器行为

### 重要设置

在 `settings.yml` 中可以自定义以下重要设置：

- 实例名称和调试模式
- 搜索引擎配置（本配置已启用 Bing、Bilibili；禁用了 Arch Linux Wiki、DuckDuckGo、GitHub、Wikipedia）
- 安全搜索级别
- 结果格式（已启用 JSON 响应格式）
- 缓存设置（已配置 Redis 用于缓存）

## API 使用方法

您可以通过 API 查询 SearXNG。以下是使用 curl 的示例：

```bash
curl -kLX GET --data-urlencode q='langchain' -d format=json http://localhost:8080
```

此命令搜索 "langchain" 并以 JSON 格式返回结果。

### 支持的参数

- `q`：搜索查询（必需）
- `format`：响应格式（json、html、csv、rss）
- `categories`：限制搜索类别（general、images、videos 等）
- `engines`：指定使用的搜索引擎
- `language`：搜索语言代码（如 zh-CN、en-US）

## 安全提示

在部署到生产环境之前：

1. 更改 `settings.yml` 中的 `secret_key`
2. 禁用调试模式（设置 `debug: false`）
3. 考虑设置反向代理（如 Nginx）并启用 HTTPS
4. 根据需要配置速率限制，防止 API 滥用

## 访问方式

- **Web 界面**：http://localhost:8080
- **API 端点**：http://localhost:8080

## 故障排除

如果遇到问题：

1. 检查 Docker 容器日志：`docker logs <container_id>`
2. 确认配置文件权限正确
3. 验证端口映射是否正确
4. 检查 `settings.yml` 中的语法错误

## 更多资源

- [SearXNG 官方文档](https://docs.searxng.org/)
- [SearXNG GitHub 仓库](https://github.com/searxng/searxng)
