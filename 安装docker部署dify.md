## 安装docker
- windows桌面版docker下载地址：https://www.docker.com/products/docker-desktop/
- 安装后修改镜像源：

```Docker Engine
{
  "builder": {
    "gc": {
      "defaultKeepStorage": "20GB",
      "enabled": true
    }
  },
  "dns": [
    "8.8.8.8",
    "8.8.4.4"
  ],
  "experimental": false,
  "registry-mirrors": [
    "https://0c105db5188026850f80c001def654a0.mirror.swr.myhuaweicloud.com",
    "https://docker.1panel.live",
    "https://hub.rat.dev/",
    "https://docker.1panel.live",
    "https://docker.m.daocloud.io"
  ]
}
```

- 修改镜像路径为C盘外其它盘符,先关掉WSL服务：
```wsl
wsl --shutdown
```
- 设置完应用并重启

## 部署dify
- 在电脑中任意地方建立dify文件夹，进入该文件夹后打开CMD
- 拉取dify，在CMD中运行以下命令:
```github
git clone https://github.com/langgenius/dify.git
```
- 导航到 Dify 源代码文件夹的的 docker 目录执行命令：
```cmd
# 导航到 docker 目录
cd dify/docker
# 复制并重命名配置文件
copy .env.example .env
```
- 如果要避免80端口被占用，可提前修改docker下的.env文件：
```.env
NGINX_PORT=8080
EXPOSE_NGINX_PORT=8080
```
- 启动 docker compose
```cmd
docker compose up -d
```

- 检查端口监听情况,确认宿主机的 8080 端口是否被监听,如果没有输出，说明端口未被监听，浏览器是无法打开的。
```bash
netstat -ano | findstr :8080
```
- 通过浏览器打开:http://127.0.0.1:8080打开dify

# 更新dify
- 进入dify源代码的docker目录，按顺序执行以下命令：：

```cmd
cd dify/docker
docker compose down
git pull origin main
docker compose pull
docker compose up -d
```
