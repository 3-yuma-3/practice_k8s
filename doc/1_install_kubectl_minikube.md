# 打ったコマンド記録

## [Kubernetesドキュメント / タスク / ツールのインストール / kubectlのインストールおよびセットアップ](https://kubernetes.io/ja/docs/tasks/tools/install-kubectl/)
### [Linuxへkubectlをインストールする / curlを使用してLinuxへkubectlのバイナリをインストールする](https://kubernetes.io/ja/docs/tasks/tools/install-kubectl/#curl%E3%82%92%E4%BD%BF%E7%94%A8%E3%81%97%E3%81%A6linux%E3%81%B8kubectl%E3%81%AE%E3%83%90%E3%82%A4%E3%83%8A%E3%83%AA%E3%82%92%E3%82%A4%E3%83%B3%E3%82%B9%E3%83%88%E3%83%BC%E3%83%AB%E3%81%99%E3%82%8B)
1. `$ curl -LO "https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl"`
2. `$ chmod +x ./kubectl`
3. `$ sudo mv ./kubectl /usr/local/bin/kubectl`
4. `$ kubectl version --client`

### [kubectlの設定を検証する](https://kubernetes.io/ja/docs/tasks/tools/install-kubectl/#kubectl%E3%81%AE%E8%A8%AD%E5%AE%9A%E3%82%92%E6%A4%9C%E8%A8%BC%E3%81%99%E3%82%8B)
1. `$ kubectl cluster-info`
   1. `The connection to the server <server-name:port> was refused - did you specify the right host or port?`
   2. `たとえば、ラップトップ上(ローカル環境)でKubernetesクラスターを起動するような場合、Minikubeなどのツールを最初にインストールしてから、上記のコマンドを再実行する必要があります。`

### [Documentation / Get Started! / minikube start](https://minikube.sigs.k8s.io/docs/start/)
1. `$ curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64`
2. `$ sudo install minikube-linux-amd64 /usr/local/bin/minikube`
3. `$ minikube start`

### [kubectlの設定を検証する](https://kubernetes.io/ja/docs/tasks/tools/install-kubectl/#kubectl%E3%81%AE%E8%A8%AD%E5%AE%9A%E3%82%92%E6%A4%9C%E8%A8%BC%E3%81%99%E3%82%8B)
1. `$ kubectl cluster-info`

```bash
Kubernetes control plane is running at https://127.0.0.1:32769
CoreDNS is running at https://127.0.0.1:32769/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
```

### [Documentation / Get Started! / minikube start](https://minikube.sigs.k8s.io/docs/start/)
1. `$ kubectl get po -A`
2. `$ minikube dashboard`
3. `$ kubectl create deployment hello-minikube --image=kicbase/echo-server:1.0`
4. `$ kubectl expose deployment hello-minikube --type=NodePort --port=8080`
5. `$ kubectl get services hello-minikube`
6. `$ minikube service hello-minikube`
7. `$ kubectl port-forward service/hello-minikube 7080:8080`
8. `$ minikube pause`
9. `$ minikube stop`
10. `$ minikube delete --all`
