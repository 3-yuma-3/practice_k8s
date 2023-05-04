# [Kubernetesドキュメント / はじめに / プロダクション環境 / Kubernetesをデプロイツールでインストールする / kubeadmを使ってクラスターを構築する / kubeadmを使用したクラスターの作成](https://kubernetes.io/ja/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/)

## コントロールプレーンノードの初期化
1. `$ sudo kubeadm init`

```bash
[init] Using Kubernetes version: v1.26.3
[preflight] Running pre-flight checks
[preflight] Pulling images required for setting up a Kubernetes cluster
[preflight] This might take a minute or two, depending on the speed of your internet connection
[preflight] You can also perform this action in beforehand using 'kubeadm config images pull'
[certs] Using certificateDir folder "/etc/kubernetes/pki"
[certs] Generating "ca" certificate and key
[certs] Generating "apiserver" certificate and key
[certs] apiserver serving cert is signed for DNS names [kubernetes kubernetes.default kubernetes.default.svc kubernetes.default.svc.cluster.local ubuntu22-3-containerd] and IPs [10.96.0.1 10.0.2.15]
[certs] Generating "apiserver-kubelet-client" certificate and key
[certs] Generating "front-proxy-ca" certificate and key
[certs] Generating "front-proxy-client" certificate and key
[certs] Generating "etcd/ca" certificate and key
[certs] Generating "etcd/server" certificate and key
[certs] etcd/server serving cert is signed for DNS names [localhost ubuntu22-3-containerd] and IPs [10.0.2.15 127.0.0.1 ::1]
[certs] Generating "etcd/peer" certificate and key
[certs] etcd/peer serving cert is signed for DNS names [localhost ubuntu22-3-containerd] and IPs [10.0.2.15 127.0.0.1 ::1]
[certs] Generating "etcd/healthcheck-client" certificate and key
[certs] Generating "apiserver-etcd-client" certificate and key
[certs] Generating "sa" key and public key
[kubeconfig] Using kubeconfig folder "/etc/kubernetes"
[kubeconfig] Writing "admin.conf" kubeconfig file
[kubeconfig] Writing "kubelet.conf" kubeconfig file
[kubeconfig] Writing "controller-manager.conf" kubeconfig file
[kubeconfig] Writing "scheduler.conf" kubeconfig file
[kubelet-start] Writing kubelet environment file with flags to file "/var/lib/kubelet/kubeadm-flags.env"
[kubelet-start] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
[kubelet-start] Starting the kubelet
[control-plane] Using manifest folder "/etc/kubernetes/manifests"
[control-plane] Creating static Pod manifest for "kube-apiserver"
[control-plane] Creating static Pod manifest for "kube-controller-manager"
[control-plane] Creating static Pod manifest for "kube-scheduler"
[etcd] Creating static Pod manifest for local etcd in "/etc/kubernetes/manifests"
[wait-control-plane] Waiting for the kubelet to boot up the control plane as static Pods from directory "/etc/kubernetes/manifests". This can take up to 4m0s
[apiclient] All control plane components are healthy after 9.002816 seconds
[upload-config] Storing the configuration used in ConfigMap "kubeadm-config" in the "kube-system" Namespace
[kubelet] Creating a ConfigMap "kubelet-config" in namespace kube-system with the configuration for the kubelets in the cluster
[upload-certs] Skipping phase. Please see --upload-certs
[mark-control-plane] Marking the node ubuntu22-3-containerd as control-plane by adding the labels: [node-role.kubernetes.io/control-plane node.kubernetes.io/exclude-from-external-load-balancers]
[mark-control-plane] Marking the node ubuntu22-3-containerd as control-plane by adding the taints [node-role.kubernetes.io/control-plane:NoSchedule]
[bootstrap-token] Using token: w6tjej.bbbnua153mrynjsr
[bootstrap-token] Configuring bootstrap tokens, cluster-info ConfigMap, RBAC Roles
[bootstrap-token] Configured RBAC rules to allow Node Bootstrap tokens to get nodes
[bootstrap-token] Configured RBAC rules to allow Node Bootstrap tokens to post CSRs in order for nodes to get long term certificate credentials
[bootstrap-token] Configured RBAC rules to allow the csrapprover controller automatically approve CSRs from a Node Bootstrap Token
[bootstrap-token] Configured RBAC rules to allow certificate rotation for all node client certificates in the cluster
[bootstrap-token] Creating the "cluster-info" ConfigMap in the "kube-public" namespace
[kubelet-finalize] Updating "/etc/kubernetes/kubelet.conf" to point to a rotatable kubelet client certificate and key
[addons] Applied essential addon: CoreDNS
[addons] Applied essential addon: kube-proxy

Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

Alternatively, if you are the root user, you can run:

  export KUBECONFIG=/etc/kubernetes/admin.conf

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 10.0.2.15:6443 --token w6tjej.bbbnua153mrynjsr \
	--discovery-token-ca-cert-hash sha256:f0af0abc16ab3331c718699adbb2ab4ae034ba9fb4f1274f19f4e28bbeec7cc9
```

2. `$ mkdir -p $HOME/.kube`
3. `$ sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config`
4. `$ sudo chown $(id -u):$(id -g) $HOME/.kube/config`

## [2019年版・Kubernetesクラスタ構築入門](https://knowledge.sakura.ad.jp/20955/)

1. `$ kubectl get node` (まだstatusがnotReady)
    ```bash
      NAME         STATUS     ROLES           AGE     VERSION
      ubuntu22-1   NotReady   control-plane   6m31s   v1.26.3
    ```
2. `$ wget https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml`
3. `$ kubectl apply -f kube-flannel.yml`
4. `$ kubectl get node` (statusがreadyになる)
    ```bash
      NAME         STATUS   ROLES           AGE     VERSION
      ubuntu22-1   Ready    control-plane   8m16s   v1.26.3
    ```
5. 非マスターノード側で `$ kube init` 時に出力されたコマンドを実行
  - [Invalid x509 certificate for kubernetes master (stackoverflow)](https://stackoverflow.com/questions/46360361/invalid-x509-certificate-for-kubernetes-master)
    - `$ sudo kubeadm reset`
    - `sudo kubeadm init --apiserver-advertise-address=192.168.1.154 --apiserver-cert-extra-sans=192.168.1.154`
    - `$ sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config`
    - 再度、非マスターノード側で `$ kube init` 時に出力されたコマンドを実行
      ```
        [preflight] Running pre-flight checks
        [preflight] Reading configuration from the cluster...
        [preflight] FYI: You can look at this config file with 'kubectl -n kube-system get cm kubeadm-config -o yaml'
        [kubelet-start] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
        [kubelet-start] Writing kubelet environment file with flags to file "/var/lib/kubelet/kubeadm-flags.env"
        [kubelet-start] Starting the kubelet
        [kubelet-start] Waiting for the kubelet to perform the TLS Bootstrap...

        This node has joined the cluster:
        * Certificate signing request was sent to apiserver and a response was received.
        * The Kubelet was informed of the new secure connection details.

        Run 'kubectl get nodes' on the control-plane to see this node join the cluster.
      ```
6. `$ kubectl get node`
    ```bash
      NAME                        STATUS     ROLES           AGE   VERSION
      asusu-ubuntu22-virtualbox   NotReady   <none>          26s   v1.26.3
      ubuntu22-1                  Ready      control-plane   95s   v1.26.3
    ```
- **40分経っても非master nodeがreadyにならない**
  - [Kubernetesのノードステータス"NotReady"を"Ready"へ](https://www.n-novice.com/entry/2018/03/18/030512#nodes%E3%82%B9%E3%83%86%E3%83%BC%E3%82%BF%E3%82%B9%E7%A2%BA%E8%AA%8D-1)
    - `$ kubectl describe nodes`
      ```
        container runtime network not ready: NetworkReady=false reason:NetworkPluginNotReady message:Network plugin returns error: cni plugin not initialized
      ```
      - 非master nodeのCRIがnot readyでこけてた
      - master nodeの方はエラーなし
        ```
          kubelet is posting ready status. AppArmor enabled
        ```
  - [kubeadmをセットアップしてみたが、いつも通りハマる](https://yunkt.hatenablog.com/entry/2018/08/13/200123)
    - 非master nodeで `$ sudo systemctl status kubelet`
      - master nodeで `kubectl describe nodes` を打った時のエラーと同じメッセージが出てた
    - 非master nodeで `$ sudo vim /etc/containerd/config.toml` で `SystemdCgroup` を確認したところ、`true` になってたのでエラー原因謎


- [Kubernetesドキュメント / タスク / クラスター内アプリケーションへのアクセス / Web UI (Dashboard)](https://kubernetes.io/ja/docs/tasks/access-application-cluster/web-ui-dashboard/) へ
