# cockroachai-v2-deploy
- cockroachai-v2使用教程-暂时
- 现无会话隔离
- 现无法接入限速以及第三方账户

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

访问 http://服务器 IP:9315/setup 输入accesstoken写入 accesstoken.json

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

3.获取accesstoken

Accesstoken获取略，填入/setup中

然后创建并打开当前目录下的config文件夹 创建配置文件(不创建配置文件将导致容器无法启动)
```bash
mkdir config && cd config && vim config.yaml
```

根据需求修改下述配置 配置管理员密码 ADMIN_PASSWORD: xxxxxxxx 
修改 USERTOKENS 为用户 Token，可以使用多个

```bash
PORT: 9315 #容器内部端口，一般不修改，如果为空默认为8080
JA3_PROXY: "http://cockroachai:cockroachai@172.17.0.1:9988" #ja3代理
# ASSET_PREFIX: "" # 用于配置静态资源
ADMIN_PASSWORD: "xyhelper" #管理员密码
REFRESH_TOKEN: "" #可永久刷新的Apple获取的token，未测试
USERTOKENS:
  - "hello"
  - "123456"
```


按一下 esc，然后 :wq 保存退出

4.运行
```bash
cd .. && docker compose up -d
```
