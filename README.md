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
- [ ] 支持123网盘。

## 更新日志
V1.0.1  增加非302方式strm文件生成。
V1.0.2  115账号独立请求数限制


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

*内置Redis版安装命令*

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