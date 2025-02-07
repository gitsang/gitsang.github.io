---
title: 'Install k8s cluster on debian'
slug: install-k8s-cluster-on-debian
description: |-
  An step by step guide to install k8s cluster on debian 12
date: 2025-02-07T14:01:24+08:00
lastmod: 2025-02-07T14:01:24+08:00
weight: 1
categories:
  - k8s
tags:
  - k8s
---

## Machine initialization

### 1. Disable swap

Run `sudo swapoff -a` then configure `/etc/fstab`

### 2. Configure kernel parameters

```sh
cat <<EOF | sudo tee /etc/modules-load.d/containerd.conf
overlay
br_netfilter
EOF
```

```sh
sudo modprobe overlay
sudo modprobe br_netfilter
```

```sh
cat <<EOF | sudo tee /etc/sysctl.d/99-kubernetes-k8s.conf
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
net.bridge.bridge-nf-call-ip6tables = 1
EOF
```

```sh
sudo sysctl --system
```

### 3. Install containerd

```sh
sudo apt update
sudo apt -y install containerd
```

### 4. Configure containerd

Config file is in `/etc/containerd/config.toml`

1. Generate default containerd config file

```sh
containerd config default | sudo tee /etc/containerd/config.toml >/dev/null 2>&1
```

2. Set cgroup driver to systemd.

```diff
  [plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc.options]
-   SystemdCgroup = false
+   SystemdCgroup = true
```

3. Change pause image

```diff
  [plugins."io.containerd.grpc.v1.cri"]
-   sandbox_image = "registry.k8s.io/pause:3.6"
+   sandbox_image = "registry.k8s.io/pause:3.10"
```

> In China use `registry.aliyuncs.com/google_containers/pause:3.10` instead.

4. Restart containerd

```sh
sudo systemctl restart containerd
sudo systemctl enable containerd
```

### 5. Install Kubernetes Tools

> Follow [Installing kubeadm, kubelet and kubectl](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/#installing-kubeadm-kubelet-and-kubectl)

> In China follow https://developer.aliyun.com/mirror/kubernetes to use mirror.

1. Install prerequisite packages.

```sh
sudo apt update
# apt-transport-https may be a dummy package; if so, you can skip that package
sudo apt install -y apt-transport-https ca-certificates curl gpg
```

2. Configure repository keyrings

```sh
export K8S_VERSION=v1.32
sudo mkdir -p -m 755 /etc/apt/keyrings
```

```sh
curl -fsSL "https://pkgs.k8s.io/core:/stable:/${K8S_VERSION}/deb/Release.key" | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
echo "deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/${K8S_VERSION}/deb/ /" | sudo tee /etc/apt/sources.list.d/kubernetes.list
```

> In China use:
>
> ```sh
> curl -fsSL "https://mirrors.aliyun.com/kubernetes-new/core/stable/${K8S_VERSION}/deb/Release.key" | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
> echo "deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://mirrors.aliyun.com/kubernetes-new/core/stable/${K8S_VERSION}/deb/ /" | sudo tee /etc/apt/sources.list.d/kubernetes.list
> ```

3. Install tools

```sh
sudo apt update
sudo apt install -y kubelet kubeadm kubectl
# sudo apt-mark hold kubelet kubeadm kubectl
```

4. Enable service

```sh
sudo systemctl enable --now kubelet
```

In this time `journalctl -f -u kubelet` should failure by
`error: failed to load Kubelet config file /var/lib/kubelet/config.yaml`,
it's ok, we will fix it later.

## Install k8s cluster

### 0. Configure hostnames

Configure hostname

```sh
sudo hostnamectl set-hostname "k8s-master.local"    // Run on master node
sudo hostnamectl set-hostname "k8s-worker-01.local" // Run on 1st worker node
sudo hostnamectl set-hostname "k8s-worker-02.local" // Run on 2nd worker node
```

Configure `/etc/hosts`

```hosts
192.168.5.100  k8s-master.local     k8s-master
192.168.5.101  k8s-worker-01.local  k8s-worker-01
192.168.5.102  k8s-worker-02.local  k8s-worker-02
```

### 1. Install k8s cluster with kubeadm (run on control panel node)

Create `kubelet.yaml`

```yaml
apiVersion: kubeadm.k8s.io/v1beta4
kind: InitConfiguration
---
apiVersion: kubeadm.k8s.io/v1beta4
kind: ClusterConfiguration
kubernetesVersion: '1.32.0' # Replace with your desired version
controlPlaneEndpoint: 'k8s-master' # Replace with your desired control plane endpoint
imageRepository: registry.k8s.io
---
apiVersion: kubelet.config.k8s.io/v1beta1
kind: KubeletConfiguration
```

> In China use `imageRepository: registry.aliyuncs.com/google_containers`.

Install control panel

```sh
sudo kubeadm init --config kubelet.yaml
```

> Use `sudo kubeadm reset` if you want to reset k8s cluster.

Configure default kube config

```sh
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

### 2. Join k8s cluster with kubeadm (run on worker nodes)

```sh
sudo kubeadm join k8s-master:6443 --token 21nm87.x1lgd4jf0lqiiiau \
    --discovery-token-ca-cert-hash sha256:28b503f1f2a2592678724c482776f04b445c5f99d76915552f14e68a24b78009
```

### 3. Check k8s cluster status (run on control panel node)

```sh
sudo kubectl get nodes
```

## Setup Pod Network

### 1. Install Calico (run on control panel node)

Install Calico

```sh
sudo kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.29.2/manifests/calico.yaml
```

> In China:
>
> ```sh
> curl -sSLO https://raw.githubusercontent.com/projectcalico/calico/v3.29.2/manifests/calico.yaml
> sed -i 's/docker.io/dockerhub.icu/g' calico.yaml
> ```

Verify

```sh
sudo kubectl get pods -n kube-system
```

When finished, you should see the `calico-node` pod running by
`sudo kubectl get pods -n kube-system` and see all nodes ready by
`sudo kubectl get nodes`.

## Test

```
sudo kubectl create deployment nginx-app --image=nginx --replicas 2
sudo kubectl expose deployment nginx-app --name=nginx-web-svc --type NodePort --port 80 --target-port 80
sudo kubectl describe svc nginx-web-svc
```

Curl using either of worker node's hostname

```sh
curl http://k8s-worker-01:32283
```

## Reference

- [How to Install Kubernetes Cluster on Debian 12 | 11 - Linuxtechi](https://www.linuxtechi.com/install-kubernetes-cluster-on-debian/)
- [Docker hub mirror](https://www.kelen.cc/dry/docker-hub-mirror)
- [Kubernetes mirrors - aliyun](https://developer.aliyun.com/mirror/kubernetes)
