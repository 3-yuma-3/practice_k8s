# practice_k8s
# 順番
1. [install-kubectl](./doc/k8s/4_tasks/1_tools/4_install-kubectl/memo.md)
2. [execute-minikube-local](./doc/k8s/2_setup/2_learning-environment/1_mi
3. [hello-minikube](./doc/k8s/5_uttorials/1_hello-minikube/1_hello_minikube.md)nikube/2_execute_minikube_local.md)
4. [docker_k8s_実践コンテナ開発入門](./book/docker_k8s_%E5%AE%9F%E8%B7%B5%E3%82%B3%E3%83%B3%E3%83%86%E3%83%8A%E9%96%8B%E7%99%BA%E5%85%A5%E9%96%80/)
5. [helm](./doc/helm/)
6. [container-runtimes](./doc/k8s/2_setup/3_production-environment/1_container-runtimes/memo.md)
7. [install-kubeadm](./doc/k8s/2_setup/3_production-environment/2_tools/1_kubeadm/1_install_kubeadm.md)
8. [create-cluster-kubeadm](./doc/k8s/2_setup/3_production-environment/2_tools/1_kubeadm/3_create-cluster-kubeadm.md)

# doc
## k8s ドキュメント
1. [Kubernetesドキュメント / ホーム](https://kubernetes.io/ja/docs/home/)
2. [Kubernetesドキュメント / タスク / ツールのインストール](https://kubernetes.io/ja/docs/tasks/tools/)
   1. [Kubernetesドキュメント / タスク / ツールのインストール / kubectlのインストールおよびセットアップ](https://kubernetes.io/ja/docs/tasks/tools/install-kubectl/)
   2. [Documentation / Get Started! / minikube start](https://minikube.sigs.k8s.io/docs/start/)
3. [Kubernetesドキュメント / はじめに / 学習環境 / Minikubeを使用してローカル環境でKubernetesを動かす](https://kubernetes.io/ja/docs/setup/learning-environment/minikube/)
4. [Kubernetesドキュメント / チュートリアル / Hello Minikube](https://kubernetes.io/ja/docs/tutorials/hello-minikube/)
5. [Kubernetesドキュメント / チュートリアル / Kubernetesの基本を学ぶ](https://kubernetes.io/ja/docs/tutorials/kubernetes-basics/)

---

- [Kubernetesドキュメント / はじめに / プロダクション環境 / コンテナランタイム](https://kubernetes.io/ja/docs/setup/production-environment/container-runtimes/)
   - `kubeadmを使用したクラスター内の各kubeletの設定`

## helm
- [概要 > クイックスタートガイド](https://helm.sh/ja/docs/intro/quickstart/)
- [ArtifactHUB](https://artifacthub.io/)

# book
## [docker/k8s 実践コンテナ開発入門](https://www.amazon.co.jp/dp/4297100339)
- [memo](./book/docker_k8s_実践コンテナ開発入門/memo.md)

# others
- [2019年版・Kubernetesクラスタ構築入門 (2019/03/06) (さくらのナレッジ)](https://knowledge.sakura.ad.jp/20955/)
- [Deploying a Rails application to Kubernetes](https://kubernetes-rails.com/)
- [Oracle Cloudの無料枠でKubernetesクラスタを構築する(完全版) (2021/06/01) (blog.potproject.net)](https://blog.potproject.net/2021/06/01/oracle-cloud-kubernetes)
- [事実上の標準ツールとなっているKubernetes向けデプロイツール「Helm」入門 (2020/03/05) (さくらのナレッジ)](https://knowledge.sakura.ad.jp/23603/)

## gitlab runner
- [install > install gitlab runner > GitLab Runner Helm Chart (gitlab)](https://docs.gitlab.com/runner/install/kubernetes.html)
- [use gitlab > build your application > runners > runner executors > Kubernetes executor (gitlab)](https://docs.gitlab.com/runner/executors/kubernetes.html)
- [Helmを使ってGitLab RunnerをKubernetesに配置する (2021/06/18)](https://www.orzs.tech/deploy-helm-gitlab-runner-on-kubernetes/)
- [Gitlab Runner を helm を使って k8s 上にデプロイしてみた (2021/04/08) (hawksnowlog)](https://hawksnowlog.blogspot.com/2021/04/install-gitlab-runner-with-helm.html)
