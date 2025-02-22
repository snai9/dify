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
- 通过浏览器打开dify：http://127.0.0.1:8080

# 更新dify
- 进入dify源代码的docker目录，按顺序执行以下命令：：

```cmd
cd dify/docker
docker compose down
git pull origin main
docker compose pull
docker compose up -d
```

# 白嫖Groq平台算力
- 访问地址：https://groq.com/ ，可以查询支持的模型名称
- 在https://console.groq.com/keys 申请APIKEY

# Deno实现代理
- 访问 https://dash.deno.com
- 点击 右上角**new  playground**，进入发布页面
- 将左边的服务代码修改为以下代码：
```ts
const OPENAI_API_HOST = "api.groq.com";//API基础地址

Deno.serve(async (request) => {
 const url = new URL(request.url);
 url.host = OPENAI_API_HOST;

 const newRequest = new Request(url.toString(), {
   headers: request.headers,
   method: request.method,
   body: request.body,
   redirect: "follow",
 });
 return await fetch(newRequest);
});
```

- 类型设置为TS后保存并发布
- 右边的输出可以看到生成的二级域名，例如：https://clean-wasp-20.deno.dev ，这个就是代理的代码中的API基础地址，比如： https://clean-wasp-20.deno.dev 其实是 https://api.groq.com
- 在dify的模型供应商中如果不能科学上网的话，是不能直接选择**gropcloud**的，所以我们需要选择兼容 **openAI-API-compatible**
- 模型名称在Groq中查询后填写支持模型的全称，如：**DeepSeek-R1-Distill-Llama-70B**
- API Key就是上面Groq平台API-KEY 申请的KEY，例如：**gsk_TN7oCE5eJBTB9HTedek3WGdyb3FYzdKsF4QL591qA9DKHClGsWV**
- API endpoint URL就是基础地址加各模型的接口，例如：https://clean-wasp-20.deno.dev/openai/v1 ， 其实就是代理前的
https://api.groq.com/openai/v1， 这个接口写法也应该在Groq上查看文档。
- 保存后可以使用了
  ## 补充一下也可以把欧派算力中的模型部署到dify
    - https://ppinfra.com/model-api/console 这里是所有支持的模型
    - 点开模型后有这个模型的名称和接口，例如：
    ``` 

      Base URL：  https://api.ppinfra.com/v3/openai

      Model ID：  deepseek/deepseek-r1/community

      API Key：  sk_6z3UrHfqjZKgBxM1164yFl1XMxWasm9v5Ygw5AeT71p  
    ```
    - 同样也是要在**openAI-API-compatible**中添加模型


# 知识库模型
- 先通过 https://jina.ai/reader /拿到免费的APIKEY，如**jina_2bd1b6a107b64f31b45f98e4f05ed597hub6-yojR7OjP6QXWahAGaCstkgH**
- 在dify中选择同步自web站点，把jina的API输入后直接输入知识网址生成markdown
- 要保证更好的效果还要添加嵌入模型，也可以在模型供应商中使用jina，同样是输入APIKEY就行

