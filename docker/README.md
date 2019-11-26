# Docker

## Dockerfile

```dockerfile
FROM node:6.11.5

WORKDIR /usr/src/app
COPY package.json .
RUN npm install
COPY . .

CMD [ "npm", "start" ]
```

- FROM node:6.11.5
  - 镜像建立在 `node:6.11.5` 镜像的基础上，`node:6.11.5` 是一个公共镜像，经过了良好的测试和验证。
- WORKDIR /usr/src/app
  - 指定工作目录，后续的所有操作将在工作目录进行（镜像的文件系统）
- COPY package.json .
  - 从当前目录复制 `package.json` （你的主机）到镜像的工作目录中（此处为 `/usr/src/app`）
- RUN npm install
  - 在镜像的工作目录中运行 `npm install` 命令，将项目所需的依赖安装到镜像工作目录中；
- COPY . .
  - 将剩余的源码复制到镜像工作目录中
- CMD [ "npm", "start" ]
  - 容器内运行的进程（指定运行的命令）；

## Docker Command

### 启动容器

```bash
docker container run --publish 8000:8080 --detach --name bb bulletinboard:1.0
```

- --publish 8000:8080
  - 将主机上 8000 端口的网络流量转发到容器中的 8080 端口；（如果想要通过网络访问容器内的端口，必须要在这一步设置端口流量转发，否则会被防火墙阻止访问）
- --detach
  - 后台运行该容器
- --name
  - 容器名称

### 删除容器

```bash
docker container rm --force bb
```

## Kubernetes

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: bb-demo
  namespace: default
spec:
  replicas: 1
  selector:
    matchLabels:
      bb: web
  template:
    metadata:
      labels:
        bb: web
    spec:
      containers:
      - name: bb-site
        image: bulletinboard:1.0
---
apiVersion: v1
kind: Service
metadata:
  name: bb-entrypoint
  namespace: default
spec:
  type: NodePort
  selector:
    bb: web
  ports:
  - port: 8080
    targetPort: 8080
    nodePort: 30001
```

- 两个对象
  - Deployment：展示了一组可扩展的相同的 pods；
  - NodePort：一个将本机 30001 端口的流量转发到 pod 中的 8080 端口的服务；
- K8S 的配置项基本上都遵循下面的格式
  - apiVersion：指定用于解析 yaml 配置文件的 k8s 版本；
  - kind：类型；
  - metadata：描述信息；
  - spec：pod 的定制化配置参数；

## 共享 Docker 镜像

