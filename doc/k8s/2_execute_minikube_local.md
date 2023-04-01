## [Kubernetesドキュメント / はじめに / 学習環境 / Minikubeを使用してローカル環境でKubernetesを動かす](https://kubernetes.io/ja/docs/setup/learning-environment/minikube/)
1. `$ minikube start`
2. `$ kubectl create deployment hello-minikube --image=registry.k8s.io/echoserver:1.10`
3. `$ kubectl expose deployment hello-minikube --type=NodePort --port=8080`
4. `$ kubectl get pod`
5. `$ minikube service hello-minikube --url`
6. `$ kubectl delete services hello-minikube`
7. `$ kubectl delete deployment hello-minikube`
8. `$ minikube stop`
9. `$ minikube delete`
