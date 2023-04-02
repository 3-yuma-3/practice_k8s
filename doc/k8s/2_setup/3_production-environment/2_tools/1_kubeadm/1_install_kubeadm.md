# [Kubernetesドキュメント / はじめに / プロダクション環境 / Kubernetesをデプロイツールでインストールする / kubeadmを使ってクラスターを構築する / kubeadmのインストール](https://kubernetes.io/ja/docs/setup/production-environment/tools/kubeadm/install-kubeadm/)

## 始める前に
- swapをオフにする
  1. `$ sudo swapon --show`
  2. `$ free -h`
  3. `$ sudo vim /etc/fstab`
  4.  `/swapfile` の行をコメントアウト
  5. `$ reboot`
  6. `$ sudo swapon --show`
  7. `$ free -h`

## MACアドレスとproduct_uuidが全てのノードでユニークであることの検証
1. `$ ip link`
2. `$ sudo cat /sys/class/dmi/id/product_uuid`

## 必須ポートの確認
1. `$ nc 127.0.0.1 6443`

## kubeadm, kubelet, kubectlのインストール
1. `$ sudo apt-get update`
2. `$ sudo apt-get install -y apt-transport-https ca-certificates curl`
3. `$ sudo curl -fsSLo /etc/apt/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg`
4. `$ echo "deb [signed-by=/etc/apt/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list`
5. `$ sudo apt-get update`
6. `$ sudo apt-get install -y kubelet kubeadm kubectl`
7. `$ sudo apt-mark hold kubelet kubeadm kubectl`
