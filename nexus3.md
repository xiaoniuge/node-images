---
typora-root-url: ./
---

# maven私服搭建

**使用docker 安装nexus3**

```bash
## 搜索 nexus3 maven私服的镜像
docker search nexus3

## 拉取镜像
docker pull docker.io/sonatype/nexus3

## 运行镜像 
docker run -id --privileged=true --name=nexus3 --restart=always -p 8081:8081 -v /kichun/nexus3/nexus-data:/var/nexus-data image-id

```

**nexus3 初始密码的位置**

```bash
## 进入nexus3 容器
sudo docker exec -it container-id /bin/bash
## 进入目录
cd /opt/sonatype/sonatype-work/nexus3/
## 查看密码
cat admin.password
```



**浏览器访问私服**

```http
http://your-ip:8081/
```



**settings.xml**

```XML
<?xml version="1.0" encoding="UTF-8"?>
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0" 
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
          xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd">
  <localRepository>/Users/your-username/.m2/repository</localRepository>
  
  <pluginGroups>

  </pluginGroups>
    
  <proxies>
    
  </proxies>

  <servers>
    <!--拉取私服镜像的服务 my-maven 为repository id-->
    <server>
      <id>my-maven</id>  
      <username>admin</username>  
      <password>maven123456</password>  
    </server>
    <!--发布maven镜像到私服的release-->
    <server>
      <id>maven-releases</id>  
      <username>admin</username>  
      <password>maven123456</password>  
    </server>
    <!--发布maven镜像到私服的snapshots-->
    <server>  
      <id>maven-snapshots</id>  
      <username>admin</username>  
      <password>maven123456</password>  
    </server>
  </servers>

  
  <mirrors>
    <!--私服镜像库-->
    <mirror>
      <id>my-maven</id>
      <mirrorOf>!internal.repo,*</mirrorOf>
      <name>my-maven</name>
      <url>http://your-ip:8081/repository/maven-public/</url>
    </mirror>
    <!--阿里镜像库-->
    <mirror>
        <id>alimaven</id>
        <mirrorOf>central</mirrorOf>
        <name>aliyun maven</name>
      <url>http://maven.aliyun.com/nexus/content/repositories/central/</url>
    </mirror>
  </mirrors>
  
  
  <profiles>    
    <profile>  
      <id>my-maven</id>  
      <repositories>  
        <repository>  
          <id>my-maven</id>
          <url>http://your-ip:8081/repository/maven-public/</url>  
          <releases>  
            <enabled>true</enabled>
          </releases>  
          <snapshots>  
            <enabled>true</enabled>  
          </snapshots>  
        </repository> 
      </repositories>  
    </profile>
  </profiles>
    
   <activeProfiles>  
     <activeProfile>my-maven</activeProfile>  
   </activeProfiles>
</settings>

```

**pom文件配置**

```XML
    <!--需要发布的项目 pom 文件加入发布的配置-->
	<distributionManagement>
        <repository>
            <id>maven-releases</id>
            <name>Nexus Release Repository</name>
            <url>http://your-ip:8081/repository/maven-releases/</url>
        </repository>
        <snapshotRepository>
            <id>maven-snapshots</id>
            <name>Nexus Snapshot Repository</name>
            <url>http://your-ip:8081/repository/maven-snapshots/</url>
        </snapshotRepository>
    </distributionManagement>
```



# npm

**创建nx-deployer 角色**

![npm-role]( https://raw.githubusercontent.com/xiaoniuge/node-images/master/npm-role.png )

**创建deployer 用户 密码为：123456789**

![npm-user]( https://raw.githubusercontent.com/xiaoniuge/node-images/master/npm-user.png )



**注意  Realms**

![npm-realms]( https://raw.githubusercontent.com/xiaoniuge/node-images/master/npm-realms.png )



**创建 group 、host（发布和下载的库）、  proxy **