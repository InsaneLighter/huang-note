# Docker

## 配置镜像加速器

```shell
#配置镜像加速器
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://41c2w1xv.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

## 服务指令

```shell
#启动docker服务
systemctl start docker

#查看docker服务状态
systemctl status docker

#关闭docker服务
systemctl stop docker

#开机启动docker
systemctl enable docker

#重启docker服务
systemctl restart docker
```

## 镜像指令

```shell
#查看镜像
docker images
#查看镜像id
docker images -q 

#搜索镜像
docker search 镜像名

#拉取镜像
docker pull 镜像名:版本号

#删除镜像 移除一个或多个镜像[无容器使用该镜像才可删除，否则需删除相关容器才可继续或 -f 强制删除]
docker rmi 镜像id
```

## 容器指令

```shell
#查看容器
docker ps
docker ps -a

#运行容器(创建一个root-linux系统)
docker run -it --name=指定容器名称 镜像名 /bin/bash(可省略)
docker run -id --name=指定容器名称 镜像名

#进入容器
docker exec -it 

#启动容器
docker start 容器名称

#停止容器
docker stop 容器名称

#删除容器 运行状态需要先停止容器 再进行删除
docker rm 容器名称

#查看容器目录结构
docker inspect 容器名称

参数:
i 保持容器运行，当没有客户端连接时也不关闭
t 为容器重新分配一个伪输入终端
d 以后台形式运行容器，当退出容器时容器不会关闭
-it 交互式容器
-id 守护式容器
--name 创建容器名称
```

## 数据卷

- 数据卷是宿主机中的一个文件或者目录

- 当容器目录和数据卷目录绑定后，双方修改会立即同步

- 数据卷可被多个容器挂载，多个容器可以挂载多个数据卷

作用

- 容器数据持久化
- 外部机器与容器间接通信
- 容器间的通信

### 数据卷配置

```shell
docker run -it -v 宿主机目录/文件:容器内目录/文件 镜像名称
```

- 目录必须是绝对路径
- 目录不存在则会自动创建
- 可以同时挂载多个数据卷

### 数据卷容器配置

```shell
#创建数据卷容器
docker run -it --name=容器名称 -v /volume 镜像名 /bin/bash

docker run -it --name=容器名称 --volumes-from 数据卷容器名称 镜像名 /bin/bash
```

## Docker镜像原理

镜像本质是一个分层的文件系统，镜像是分层的，需要依赖于父镜像和基础镜像

![image-20210704185037272](C:\Users\Huang\AppData\Roaming\Typora\typora-user-images\image-20210704185037272.png)

### 镜像制作

- 容器制作镜像

```shell
#容器制作镜像
docker commit 容器id 自定义镜像名称:自定义版本号

#镜像转化成压缩文件(output)
docker save -o 压缩文件名称 镜像名称:版本号

#加载压缩文件
docker load -i 压缩文件名称

目录挂载产生的文件不会被制作进镜像中
```

- dockerFile

```shell
#构建镜像文件
docker build -f dockerfile文件路径 -t 镜像名称:版本 .
```

## 应用部署

```shell
#mybatis-plus代码生成
# 拉取镜像
docker pull fengwenyi/mybatis-plus-code-generator:3.5.2.3

# 启动服务
docker run -d -p 10000:10000 --name code-generator -v ~/temp:/temp fengwenyi/mybatis-plus-code-generator:3.5.2.3

# 查看日志
docker logs -f code-generator

# 进入容器
docker exec -it code-generator sh
# or
docker attach code-generator

#monio 安装
docker run -p 9000:9000 -p 9001:9001 -d --name minio -v /root/minio/data:/data -v /root/minio/config:/root/.minio -e "MINIO_ROOT_USER=huang" -e "MINIO_ROOT_PASSWORD=Huanghong1314..." quay.io/minio/minio server /data --console-address ":9000" --address ":9001"
```

