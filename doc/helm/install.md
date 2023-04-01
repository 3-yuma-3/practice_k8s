# [ドキュメントホーム](https://helm.sh/ja/docs/)

## [Helm のインストール](https://helm.sh/ja/docs/intro/install/)
1. `$ curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3`
2. `$ chmod 700 get_helm.sh`
3. `$ ./get_helm.sh`

## [クイックスタートガイド](https://helm.sh/ja/docs/intro/quickstart/)
1. `$ helm repo add stable https://charts.helm.sh/stable`
2. `$ helm search repo stable`
3. `$ helm repo update`
4. `$ helm install stable/mysql --generate-name`
5. `$ helm show chart stable/mysql`
6. `$ helm ls`
7. `$ helm status mysql-1680347909`
8. `$ helm uninstall mysql-1680347909 --keep-history`
