# cockroachai-v2-deploy
- cockroachai-v2使用教程-暂时
- 现无会话隔离
- 现无法接入限速以及第三方账户

## 更新

- 更新了getsession方法，使用与蟑螂v1一致，但需要自行配置

## 更新方法

1.更新配置文件```bash./config/config```
```bash
PORT: 9315
JA3_PROXY: "http://cockroachai:cockroachai@172.17.0.1:9988"
# ASSET_PREFIX: "" # 用于配置静态资源，可选
# HTTPPROXY: "" # 用于配置getsession代理，可选
# ARKOSE_TOKEN: "" # 用于配置arkose代理，可选
ADMIN_PASSWORD: "xyhelper"
USERTOKENS:
  - "hello"
  - "123456"
```
2.配置ARKOSE_TOKEN(方法一）

创建安装目录(可自行更改为所需目录)

```bash
mkdir -p /root/arksoe && cd /root/arkose
```
```bash
vim docker-compose.yml
```

```bash
version: '3.8'
services:
  arkose:
    image: docker.io/lyy0709/funcaptcha:latest
	container_name: arkose
    restart: always
    ports:
      - "5006:5006" #左侧端口暴露在外,可根据需求更改
    environment:
      - VERSION=2.4.5
      - HASH=6c9d6e9be9aa044cc5ce9548b4abe1b0
```
按一下 esc，然后 :wq 保存退出

接着运行
```bash
docker compose up -d
```

ARKOSE_TOKEN:填写http://服务器ip:5006/token

ps：有一定的失败率，会请耐心等待

3.配置harPool(方法二)

抓取登录时名称为```bash0A1D34FC-659D-4E23-B17B-694DCFCF6A6C```的har放置在蟑螂v2的harPool下

并修改docker-compose.yml文件

```bash
version: '3.8'
services:
  cockroachai:
    image: lyy0709/cockroachai-v2:latest
    restart: always
    ports:
      - "9315:9315" #左侧端口暴露在外,可根据需求更改
    volumes:
      - ./config:/app/config
      - ./harPool:/app/harPool #增加卷映射，如果用方法一则无需添加
```

## 前置要求

- 一台不在某 AI 黑名单的服务器
- 一个某 AI 的账号
- 安装好了 Docker 和 Docker Compose

## 安装方法(本机部署)

1.克隆本仓库

```bash
git clone https://github.com/lyy0709/cockroachai-v2-deploy.git
```

2.进入目录

```bash
cd cockroachai-v2-deploy
```

3.修改配置文件

```bash
vim config/config.yaml
```
配置ja3代理 JA3_PROXY: "http://cockroachai:cockroachai@172.17.0.1:9988" 需要替换为你自己的ja3

配置管理员密码 ADMIN_PASSWORD: xxxxxxxx

修改 USERTOKENS 为用户 Token，可以使用多个

访问 http://服务器 IP:9315/setup 输入refreshcookie写入 session.json

4.启动

```bash
chmod +x deploy.sh && ./deploy.sh
```

## 安装方法(Docker)

1.安装Docker

```bash
wget -qO- get.docker.com | bash && systemctl enable docker  # 设置开机自动启动
```

2.创建安装目录(可自行更改为所需目录)

```bash
mkdir -p /root/cockroachai-v2 && cd /root/cockroachai-v2
```
```bash
vim docker-compose.yml
```

```bash
version: '3.8'
services:
  cockroachai:
    image: lyy0709/cockroachai-v2:latest
    restart: always
    ports:
      - "9315:9315" #左侧端口暴露在外,可根据需求更改
    volumes:
      - ./config:/app/config
```
按一下 esc，然后 :wq 保存退出

3.获取refreshtoken

首先在电脑端打开https://chat.openai.com/api/auth/session (打开前确保你已在chat.openai.com登录)

然后按F12 点击应用 获取cookie 即名为"__Secure-next-auth.session-token"的值

然后创建并打开当前目录下的config文件夹 创建配置文件(不创建配置文件将导致容器无法启动)
```bash
mkdir config && cd config && vim config.yaml
```

根据需求修改下述配置 并填写sessiontoken 配置管理员密码 ADMIN_PASSWORD: xxxxxxxx 修改 USERTOKENS 为用户 Token，可以使用多个

```bash
PORT: 9315 #容器内部端口，一般不修改，如果为空默认为8080
JA3_PROXY: "http://cockroachai:cockroachai@172.17.0.1:9988" #ja3代理
# ASSET_PREFIX: "" # 用于配置静态资源
ADMIN_PASSWORD: "xyhelper" #管理员密码
USERTOKENS:
  - "hello"
  - "123456"
```


按一下 esc，然后 :wq 保存退出

4.运行
```bash
cd .. && docker compose up -d
```
