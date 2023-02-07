# Application-chart-template
通用 Helm Chart 模版

## Edit  `releasename.values.yaml`

1. Set imageCredentials for image pulling

```yaml

imageCredentials: 
  registry: registry.cn-hangzhou.aliyuncs.com
  username: deploy-man@skylark
  password: changeit
```

2. Set image info for image pulling

```yaml

image:
  repository: skylark/production
  tag: "latest"
  pullPolicy: IfNotPresent
```

3. Set host for app

```yaml

# Application 代理设置
applicationHost: 
  - factory.jet.localhost

# traefik 代理匹配规则
traefik:
  - name: web
    path: /
```

4. Set environments for app

```yaml

# 根据实际需要填写环境变量
env:
  auth: YKeLit
  ...
```

### Install

```bash
# set release-name install
helm install -f $release_name.values.yaml release-name ./
```

## HowTo

### Setup TLS

> 注：# 使用命令 `base64 -i tlsFile` 生成  `certificate` 与 `key` 的值

```yaml
# $release_name.values.yaml

jetFactoryTLSSecret:
  certificate: |-
    base64 encoded certificate
  key: |-
    base64 encoded key
```

### Set initContainers for app
```yaml
initContainers: {}
  - name: init
    image: busybox:latest
    command: ["sh", "-c"]
    args: 
      - ｜
         ls -a
    envFrom:
      - configMapRef:
          name: {{ .Chart.Name }}
```

### Set application volumes
```yaml
volumeMounts: {}
  - mountPath: /path/file
    name: volumeName

volumes: {}
  - name: volumeName
    hostPath: 
      path: /local/path
      type: DirectoryOrCreate
```

### Set import external service

```yaml
externalEndPoint: {}
  - name: ""
    ip: ""
    port: ""

externalService: {}
  - name: ""
    port: ""
    targetPort: ""
```

### Uninstall

```bash
helm uninstall release-name
```

### Upgrade

```bash
helm upgrade -f $release_name.values.yaml release-name ./
```

### 指定 namespace 请使用一下参数

`--namespace jet-factory` : 指定名称空间

`--create-namespace` : 创建名称空间
