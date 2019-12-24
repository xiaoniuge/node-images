# docker harbor 私有镜像仓库

```bash
## 拉取 仓库镜像
docker pull registry:2
## 运行
docker run -d -p 5000:5000 registry:2

docker pull busybox
docker tag busybox localhost:5000/busy
docker push localhost:5000/busy

## 前置条件
yum install -y epel-release
yum clean all
yum install -y docker-compose
yum install -y git wget
wget http://harbor.orientsoft.cn/harbor-v1.3.0-rc4/harbor-offline-installer-v1.3.0-rc4.tgz
tar -zxf harbor-offline-installer-v1.3.0-rc4.tgz
cd harbor

## 配置
vi harbor.cfg
hostname = 192.168.118.77
db_password = harbor123
clair_db_password = harbor123
harbor_admin_password = harbor123

## 设置要上传的仓库
vi /etc/docker/daemon.json
{
"registry-mirrors": ["http://f1361db2.m.daocloud.io"],
"insecure-registries":["192.168.118.77"]
}

## irstflask:v1 已有的镜像 
## 192.168.118.77/library/ 私服的库名
docker tag firstflask:v1 192.168.118.77/library/firstflask:v1
## 推送
docker push 192.168.118.77/library/firstflask:v1
```

