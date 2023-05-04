# [Kubernetesドキュメント / はじめに / プロダクション環境 / コンテナランタイム](https://kubernetes.io/ja/docs/setup/production-environment/container-runtimes)

## インストールと設定の必須要件
### IPv4フォワーディングを有効化し、iptablesからブリッジされたトラフィックを見えるようにする
1. ```bash
    cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
    overlay
    br_netfilter
    EOF
   ```
2. `$ sudo modprobe overlay`
3. `$ sudo modprobe br_netfilter`
4. ```bash
    # この構成に必要なカーネルパラメーター、再起動しても値は永続します
    cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
    net.bridge.bridge-nf-call-iptables  = 1
    net.bridge.bridge-nf-call-ip6tables = 1
    net.ipv4.ip_forward                 = 1
    EOF
  ```
5. ```bash
    # 再起動せずにカーネルパラメーターを適用
    sudo sysctl --system
  ```
6. `$ lsmod | grep br_netfilter`
7. `$ lsmod | grep overlay`
8. `$ sysctl net.bridge.bridge-nf-call-iptables net.bridge.bridge-nf-call-ip6tables net.ipv4.ip_forward`

## cgroupドライバー
### systemd cgroupドライバー


## コンテナランタイム
### containerd
1. `$ wget https://github.com/containerd/containerd/releases/download/v1.6.20/containerd-1.6.20-linux-amd64.tar.gz`
    - [github > containerd / containerd > releases](https://github.com/containerd/containerd/releases)
2. `$ sudo tar Cxzvf /usr/local containerd-1.6.20-linux-amd64.tar.gz`
3. `$ sudo mkdir -p /usr/local/lib/systemd/system`
4. `$ sudo wget -P /usr/local/lib/systemd/system/ https://raw.githubusercontent.com/containerd/containerd/main/containerd.service`
5. `$ systemctl daemon-reload`
6. `$ systemctl enable --now containerd`
7. `$ wget https://github.com/opencontainers/runc/releases/download/v1.1.5/runc.amd64`
    - [github > opencontainers /runc > releases](https://github.com/opencontainers/runc/releases)
8. `$ sudo install -m 755 runc.amd64 /usr/local/sbin/runc`
9. `$ wget https://github.com/containernetworking/plugins/releases/download/v1.2.0/cni-plugins-linux-amd64-v1.2.0.tgz`
    - [github > containernetworking / plugins > releases](https://github.com/containernetworking/plugins/releases)
10. `$ sudo mkdir -p /opt/cni/bin`
11. `$ sudo tar Cxzvf /opt/cni/bin cni-plugins-linux-amd64-v1.2.0.tgz`
12. `$ ctr --help`
13. `$ sudo mkdir -p /etc/containerd`
    - [Installing and Configuring containerd as a Kubernetes Container Runtime > Create a containerd configuration file](https://www.nocentino.com/posts/2021-12-27-installing-and-configuring-containerd-as-a-kubernetes-container-runtime/)
14. `$ sudo containerd config default | sudo tee /etc/containerd/config.toml`
15. `$ sudo vim /etc/containerd/config.toml`
16. `SystemdCgroup = false` → `SystemdCgroup = true`
17. `$ sudo systemctl restart containerd`
