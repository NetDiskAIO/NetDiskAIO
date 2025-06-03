# NetDiskAIO

一站式网盘流媒体服务器助手

- 发布频道：https://t.me/NetDiskAIO
- 交流群: https://t.me/+rV-nVN-YDcZlMjM8


## 主要特点

- 响应式布局,简洁美观易用的界面,移动端适配。
- 内置302重定向功能,可自定义多个Emby服务器。
- 支持Emby webhook可联动删除网盘文件。
- 自动化备份及生成Strm文件。
- 自定义通知渠道,目前支持Bark、Telegram方式。
- 当前支持115网盘开放平台。

## 目前计划增加功能

- [x] 增加非302方式strm文件生成。
- [ ] 支持123网盘(进行中)。

## 更新日志
V1.0.1  增加非302方式strm文件生成。

V1.0.2  115账号独立请求数限制。

V1.0.3  修复115账号独立请求数限制导致的key更新后的请求限制器获取异常。

V1.0.4  去除token失效后自动刷新,仅采用定时轮询。

V1.0.5  修复编译文件运行异常。

## 安装使用

*推荐配合Redis镜像使用,更稳定*

*Redis安装命令*

```
docker run --name redis -p 6379:6379 # 冒号左边请修改为你用于访问Redis的端口 \
        -v /home/redis/data:/data # 冒号左边请修改为你想在本地保存数据的路径  \
        -v /home/redis/conf/redis.conf:/etc/redis/redis.conf  # 冒号左边请修改为你想在本地保存配置文件的路径 \
        --privileged=true \
        -d redis:latest \
        redis-server /etc/redis/redis.conf
```

*示例Redis配置文件`redis.conf`,可根据实际环境自行修改*

```
# 基础配置
bind 0.0.0.0
port 6379
# 数据目录
dir /data
dbfilename dump.rdb
protected-mode no
# RDB持久化策略（每5分钟至少10次写操作保存）
save 300 10
# AOF持久化增强可靠性
appendonly yes
appendfsync everysec
# 内存管理（生产环境需调整）
maxmemory 512mb
maxmemory-policy allkeys-lru
```

*无Redis版安装命令*

```
docker run -d \
  -v 本地日志保存路径:/app/logs  # 冒号左边请修改为你想在本地保存日志数据的路径 \
  -v 本地配置文件保存路径:/app/resources   # 冒号左边请修改为你想在本地保存配置文件的路径 \
  -p 5001:5001 # 冒号左边请修改为你想用于访问管理界面的端口 \
  -e LICENSE_KEY="许可证" # 冒号右边请修改为你获取到的许可证 \
  -e REDIS_IP="127.0.0.1" # 冒号右边请修改为你的Redis访问IP \
  -e REDIS_PORT="6379" # 冒号右边请修改为你的Redis访问端口 \
  --name netdiskaio \
  --restart=always \
  netdiskaio/netdiskaio-noredis:latest
```

*docker-compose方式安装*
```
version: '3.8'

services:
  redis:
    image: redis:latest
    container_name: redis
    ports:
      - "6379:6379"  # 替换为实际访问端口（如 6379）
    volumes:
      - "/volume1/docker/NetDiskAIO/data:/data"  # 替换为本地数据路径
      - "/volume1/docker/NetDiskAIO/redis.conf:/etc/redis/redis.conf"  # 替换为配置文件路径（redis配置文件在上方有示例配置）
    privileged: true
    command: redis-server /etc/redis/redis.conf
    restart: unless-stopped

  netdiskaio:
    image: netdiskaio/netdiskaio-noredis:latest
    container_name: netdiskaio-noredis
    depends_on:
      - redis
    ports:
      - "9001:5001"  # 替换为管理界面访问端口（如 5001）
      - "9002:5002"  # 替换为Emby反向代理访问端口（如 5002,可配置多个对应多个Emby服务器,如不使用302可不配置）
    volumes:
      - "/volume1/docker/NetDiskAIO/logs:/app/logs"  # 替换为日志保存路径
      - "/volume1/docker/NetDiskAIO/resources:/app/resources"  # 替换为配置文件路径
    environment:
      - LICENSE_KEY=<key>        # 填入许可证
      - REDIS_IP=redis  # 使用服务名连接
      - REDIS_PORT=6379
    restart: always
```

*内置Redis版安装命令(不推荐使用)*

```
docker run -d \
  -v 本地数据保存路径:/data   # 冒号左边请修改为你想在本地保存数据的路径 \
  -v 本地日志保存路径:/app/logs  # 冒号左边请修改为你想在本地保存日志数据的路径 \
  -v 本地配置文件保存路径:/app/resources   # 冒号左边请修改为你想在本地保存配置文件的路径 \
  -p 5001:5001 # 冒号左边请修改为你想用于访问管理界面的端口 \
  -e LICENSE_KEY="许可证" # 冒号右边请修改为你获取到的许可证 \
  --name netdiskaio \
  --restart=always \
  netdiskaio/netdiskaio:latest
```

*请将`-v`命令中的路径替换为实际路径。*

*请将`-e`命令中的`许可证`替换为获取的许可证。*

*以上方式运行后访问`ip:5001`,默认用户名为`admin`,默认密码为`admin`。*

*若需修改用户名,请增加`-e USERNAME="用户名"`。*

*若需修改密码,请增加`-e PASSWORD="密码"`。*

*若需添加代理服务器以支持telegram,请增加`-e PROXY_URL="代理地址(http方式)"`。*

*若增加本地文件夹,请增加`-v`命令,例如增加了`/video`目录,想要映射到容器的`/video`中,则增加`-v /video:/video`。*

*若配置了Emby服务器的302重定向功能,请增加`-p`命令,例如增加了`5002`作为Emby反向代理的入口,则增加`-p 5002:5002`。*

## 使用引导

*操作逻辑*

    1. 已尽可能隐藏按钮图标以保证界面的简洁,所以操作多在右键菜单中,右键菜单包括但不限于 打开、移动、复制、重命名
    2. 移动端操作单击为打开,长按为显示右键菜单 
    3. 常态显示的按钮会根据当前显示的目录进行 切换/隐藏,避免误操作或显示混乱,如根目录下不显示 云下载、回收站 等功能
    4. 支持多选,可shift多选,多选后右键菜单仅对选中项生效
    5. 目录除根目录外均在最上方有 ... 文件夹显示,双击后会返回上一层,也可通过上方导航栏进行跳转

*首页图标*

![首页图标](https://github.com/NetDiskAIO/NetDiskAIO/blob/main/images/home_page.png "首页图标")

*访问URL配置*

![访问URL配置](https://github.com/NetDiskAIO/NetDiskAIO/blob/main/images/url_config_show.png "访问URL配置")

*Emby配置*

![Emby配置](https://github.com/NetDiskAIO/NetDiskAIO/blob/main/images/Emby_config.png "Emby配置")

*备份配置*

![备份配置](https://github.com/NetDiskAIO/NetDiskAIO/blob/main/images/backup_config_show.png "备份配置")

*STRM生成*

![STRM生成](https://github.com/NetDiskAIO/NetDiskAIO/blob/main/images/strm_config.png "STRM生成")

*通知配置*

![通知配置](https://github.com/NetDiskAIO/NetDiskAIO/blob/main/images/notification.png "通知配置")

*日志级别配置*

![日志级别配置](https://github.com/NetDiskAIO/NetDiskAIO/blob/main/images/logs.png "日志级别配置")

*挂载目录*

![挂载目录](https://github.com/NetDiskAIO/NetDiskAIO/blob/main/images/file_config1.png "挂载目录")

![挂载目录](https://github.com/NetDiskAIO/NetDiskAIO/blob/main/images/file_config2.png "挂载目录")

*115目录*

![115目录](https://github.com/NetDiskAIO/NetDiskAIO/blob/main/images/115.png "115目录")

*右键菜单*

![右键菜单](https://github.com/NetDiskAIO/NetDiskAIO/blob/main/images/right_button.png "右键菜单")


## 效果展示

*目录界面*

![目录界面](https://github.com/NetDiskAIO/NetDiskAIO/blob/main/images/ui.png "目录界面")

*移动端UI*

![移动端UI](https://github.com/NetDiskAIO/NetDiskAIO/blob/main/images/mobile.png "移动端UI")

*备份路径配置*

![备份路径配置](https://github.com/NetDiskAIO/NetDiskAIO/blob/main/images/backup_config.png "备份路径配置")

*任务列表*

![任务列表](https://github.com/NetDiskAIO/NetDiskAIO/blob/main/images/task.png "任务列表")

*链接上传*

![链接上传](https://github.com/NetDiskAIO/NetDiskAIO/blob/main/images/upload.png "链接上传")

*strm访问URL配置*

![strm访问URL配置](https://github.com/NetDiskAIO/NetDiskAIO/blob/main/images/url_config.png "strm访问URL配置")


## 如果对您有帮助,请支持我,这将使这个项目更持久的发展

*支付宝(请备注邮箱,会发送许可证到邮箱)*

![支付宝](https://github.com/NetDiskAIO/NetDiskAIO/blob/main/images/alipay.png "支付宝")

[//]: # (*微信*)

[//]: # (![微信]&#40;https://github.com/NetDiskAIO/NetDiskAIO/blob/main/images/alipay.png "微信"&#41;)