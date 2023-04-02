# [Kubernetesドキュメント / チュートリアル / Hello Minikube](https://kubernetes.io/ja/docs/tutorials/hello-minikube/)
1. `$ minikube start`
2. `$ minikube dashboard`
3. `$ kubectl create deployment hello-node --image=registry.k8s.io/echoserver:1.4`
4. `$ kubectl get deployments`
5. `$ kubectl get pods`
6. `$ kubectl get events`
7. `$ kubectl config view`
8. `$ kubectl expose deployment hello-node --type=LoadBalancer --port=8080`
   - Podはk8sクラスター内部のIPアドレスからのみアクセス可能
   - コンテナをk8sの仮想ネットワークの外部からアクセス可能にするためには、k8sのServiceとしてpodを公開する必要がある
   - `--type=LoadBalancer` フラグはServiceをクラスター外部に公開したいことを示しています。
9. `$ kubectl get services`
10. `$ minikube service hello-node`
11. `$ minikube addons list`
12. `$ minikube addons enable metrics-server`
13. `$ kubectl get pod,svc -n kube-system`
14. `$ minikube addons disable metrics-server`
15. `$ kubectl delete service hello-node`
16. `$ kubectl delete deployment hello-node`
17. `$ minikube stop`
18. `$ minikube delete`
