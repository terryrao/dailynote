# K8s 安装步骤

基于 centos 7 安装。

## 1. 安装 k8s 集群

### 系统要求

Master 需要 2 core 及 4 GB 以上内存 node 需要 4 core 及16 GB。

禁用防火墙：

```bash
systemctl disable firewalld
systemctl stop firewalld
setenforce 0
```



###　安装 kubeadm 工具

1. 添加 yum.repo 文件到 `/etc/yum.repos.d/kubernetes.repo` 中。

```bash
[kubernetes]
name=kubernetes Repository
baseurl=http://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64/
enabled=1
gpgcheck=0
```

2. 安装 kubeadm

```bash
yum install -y kubelet kubeadm kubectl --disableexcludes=kubernetes
```

3. 安装对应版本的docker 

安装过程略过，添加国内镜像地址：

```bash
echo '{"registry-mirrors":["https://registry.docker-cn.com"]}' > /etc/docker/daemon.json
```



4. 启动 docker 服务

```bash
systemctl enable docker && systemctl start docker
systemctl enable kubelet && systemctl start kubelet
```

### kubadm config

保存 `kubeadm` 的默认配置：

```bash
kubeadm config print init-defaults > init.default.yaml
cp init.default.yaml init-config.yaml
```

kubeadm 下载下关镜像

```bash
kubeadm config images pull --config=init-config.yaml
```

