# [docker/k8s 実践コンテナ開発入門](https://www.amazon.co.jp/dp/4297100339)

## 5章
### 5.3 k9sの概念

|       リソース名        |                               用途                                |
| ----------------------- | ----------------------------------------------------------------- |
| node                    | k8sクラスタで実行するコンテナを配置するためのサーバー             |
| namespace               | k8sクラスタ内で作る仮想的なクラスタ                               |
| pod                     | コンテナ集合体の単位で、コンテナを実行する方法を定義する          |
| replica set             | 同じ仕様の `pod` を複数生成・管理する                             |
| deployment              | `replica set` の世代管理をする                                    |
| service                 | `pod` の集合にアクセスするための経路を定義する                    |
| ingress                 | `service` をk8sクラスタの外に公開する                             |
| config map              | 設定情報を定義し、`pod` に共有する                                |
| persistent volume       | `pod` が利用するストレージのサイズや種別を定義する                |
| persistent volume claim | `persistent volume` を動的に確保する                              |
| storage class           | `persistent volume` が確保するストレージの種類を定義する          |
| stateful set            | 同じ仕様で一意性のある `pod` を複数生成・管理する                 |
| job                     | 常駐目的ではない複数の `pod` を作成し、正常終了することを保証する |
| cron job                | cron記法でスケジューリングして実行されるjob                       |
| secret                  | 認証情報などの機密データを定義する                                |
| role                    | `namespace` 内で操作可能なk8sリソースのルールを定義する           |
| role binding            | `role` とk8sリソースを利用するユーザーを紐づける                  |
| cluster role            | cluster 全体で操作可能なk8sリソースのルールを定義する             |
| cluster role binding    | `cluster role` とk8sリソースを利用するユーザーを紐づける          |
| service account         | `pod` にk8sリソースを操作させる際に利用するユーザー               |

### 5.4 k8sクラスタとnode
- master node を構成する管理コンポーネント
  - 非マネージド環境でk8sを運用する場合は、master node が単一障害店にならないように、マルチmasterで3台配置するのが一般的。

|    コンポーネント名     |                                      役割                                       |
| ----------------------- | ------------------------------------------------------------------------------- |
| kube-apiserver          | k8sのAPIを公開するコンポーネント。<br> kubectl からのリソース操作を受け付ける。 |
| etcd                    | 高可用性を備えた分散kvs。<br> k8sクラスタのバッキングストアとして利用される。   |
| kube-scheduler          | node を監視し、コンテナを配置する最適な node を選択する。                       |
| kube-controller-manager | リソースを制御するコントローラーを実行する                                      |

### 5.5 namespace
- クラスタを構築すると予め4つのnamespaceが用意される
  - default
  - docker
  - kube-public
  - kube-system
- `$ kubectl get namespace`

### 5.6 pod
- 同じpodを複数のnodeに配置する、1つのnodeに複数podを配置することも可能。
- 同一pod内のコンテナはすべて同一のnodeに配置される。
- 1つのpod内のコンテナが複数nodeにわたって配置することはできない。

- [simple-pod.yml](5.6/simple-pod.yml)
1. `$ kubectl apply -f simple-pod.yml`
    - このままではpodにアクセスはできない(5.9で解説)
2. `$ kubectl get pod`
    - pod一覧取得コマンド。
3. `$ kubectl exec -it simple-echo sh -c nginx`
    - `docker exec` の要領でコンテナの中に入る
    - podの中に複数コンテナがある場合は `-c` でコンテナ名を指定する。
4. `$ kubectl logs -f simple-echo -c echo`
5. `$ kubectl delete pod simple-echo`
    - podの削除
    - pod以外のリソースにも使用可能。
6. `$ kubctl delete -f simple-pod.yml`
    - マニフェストファイルベースでのpod削除も可能。
    - マニフェストファイルの全てのリソースが削除される。

### 5.7 replica set
- podを定義したマニフェストファイルからは1つのpodしか作成できない。
- 同一のpodを複数実行することで可溶性を高める際にreplica setを使用する。

- [simple-replicaset.yml](5.7_replica_set/simple-replicaset.yml)
1. `$ kubectl apply -f simple-replicaset.yml`
2. `$ kubectl get pod`
   - podが3つ作成されたことが確認できる。
3. `$ kubectl delete -f simple-replicaset.yml`

### 5.8 deployment
- deploymentはreplicaseより上位のリソースで、アプリケーションデプロイの基本単位となるリソース。
- replica setは同一仕様podのレプリカの数を管理・制御するためのリソース。
- deploymentはreplica setを管理・操作するためのリソース。

---

- [simple-deployment.yml](5.8_deployment/simple-deployment.yml)
1. `$ kubectl apply -f simple-deployment.yml --record`
     - 実行したkubectlコマンドを記録するために `--record` オプションをつける
2. `$ kubectl get pod,replicaset,deployment --selector app=echo`
3. `$ kubectl rollout history deployment echo`
     - 初回の反映のため、`REVISION = 1` となっている

---

- 実運用ではreplica setを直接用いることなく、deploymentのマニフェストファイルを扱う運用がほとんど。

---

- pod数のみを更新しても新規replica setは生まれない
- [simple-deployment.yml](./5.8_deployment/simple-deployment.yml)
1. `replicas: 3` → `replicas: 4` に更新する
2. `$ kubectl apply -f simple-deployment.yml --record`
3. `$ kubectl get pod`
     - 既存のpodがそのままで、1つコンテナが新たに作成される。
4. `$ kubectl rollout history deployment echo`
     - 新しくreplica setが生成されていれば `REVISION = 2` になっているはずだが、`REVISION = 1` のまま
5. `image: gihyodocker/echo:latest` → `image: gihyodocker/echo:patched` に更新する
6. `$ kubectl apply -f simple-deployment.yml --record`
7. `$ kubectl get pod --selector app=echo`
     - 新しいpodが作成され、古いpodが段階的に停止していく
8. `$ kubectl rollout history deployment echo`
     - `REVISION = 2` が作成されている。
9. `$ kubectl rollout history deployment echo --revision=1`
     - 特定の revision の定義を確認できる
10. `$ kubectl rollout undo deployment echo`
      - `undo` を実行すれば直前の操作のrevisionにdeploymentをrollbackできる
11. `$ kubectl delete -f simple-deployment.yml`

### 5.9 service
- k8sクラスタ内において、podの集合(主にreplica set)に対する経路やサービスディスカバリを提供するためのリソース
- serviceのターゲットとなる一連のpodは、serviceで定義するラベルセレクタによって決定される

---

- [simple-replicaset-with-label.yml](./5.9_service/simple-replicaset-with-label.yml)
1. `$ kubectl apply -f simple-replicaset-with-label.yml`
2. `$ kubectl get pod -l app=echo -l release=spring`
      - `spring` というラベルのpodが作成されている
3. `$ kubectl get pod -l app=echo -l release=summer`
     - `summer` というラベルのpodが作成されている

---

- [simple-service.yml](./5.9_service/simple-service.yml)
    - `release=summer` のpodだけにアクセスできるようなserviceを作る
1. `$ kubectl apply -f simple-service.yml`
2. `$ kubectl get svc echo`
3. `$ kubectl run -i --rm --tty debug --image=gihyodocker/fundamental:0.1.0 --restart=Never -- bash -il`
     - `release = summer` のpodだけにtrafficが流れるか確認するためにcurl叩く
     - `$ curl http://echo/`
4. `$ kubectl logs -f echo-summer-xxxx -c echo`
     - `summer` のpodのlogを確認すると、`received request` が出力されている。
     - `spring` のpodにはログが出力されない

- serviceの名前解決
  - k8sクラスタ内のDNSでは、serviceを `Service名.Namespace名.svc.local` で名前解決できる。
  - echoはdefaultのNamespaceに配置しているので
  - `$ curl http://echo.default.svc.local`
  - `svc.local`は省略可能。
  - 異なるNamespaceのServiceの名前解決は下記が最短
  - `$ curl http://echo.default`
  - 同一Namespaceなら下記
  - `$ curl http://echo`

#### 5.9.1 ClusterIP Service
- `ClusterIP Service` はデフォルトで作成される
- ClusterIP では k8sクラスタ上の内部IPアドレスにServiceを公開できる
- あるpodから別のpod軍へのアクセスはServiceを介して行うことができ、Service名で名前解決可能。
- ただし、外からは到達できない

#### 5.9.2 NodePort Service
- `NodePort Service` はクラスタ外からアクセスできるService

```yml
apiVersion: v1
kind: Service
metadata:
  name: echo
spec:
  type: NodePort
  selector:
    app: echo
  ports:
    - name: http
      port: 80
```

1. `$ kubectl get svc echo`
    - `PORT(S) = 80:xxxx/TCP` nodeの `xxxx` portからServiceにアクセス可能
2. `$ curl http://127.0.0.1:xxxx` でlocalからアクセス可能

#### 5.9.4 ExternalName Service
- selector, portを持たない特殊なService
- k8sクラスタ内から外部のホストを解決するためのaliasを提供する
- `gihyo.jp` を `gihyo` で名前解決できるようになる

```yml
apiVersion: v1
kind: Service
metadata:
  name: gihyo
spec:
  type: ExternalName
  externalName: gihyo.jp
```

### 5.10 Ingress
- pathベースで転送先のServiceを切り替えるためのリソースが Ingress
- 素のk8s環境ではIngressを使ったServiceの公開ができない。
- クラスタの外からのhttpリクエストをServiceにroutingするための `nginx_ingress_controller` をデプロイする

1. `$ kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/nginx-0.16.2/deploy/mandatory.yaml`
    - **正誤表を参照**
2. `$ kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/nginx-0.16.2/deploy/provider/cloud-generic.yaml`
    - **正誤表を参照**
3. `$ kubectl -n ingress-nginx get service,pod`
    - `ingress-nginx` というNamespace上にService, Podが作成される

#### 5.10.1 Ingressを通じたアクセス
- [simple-service.yml](./5.9_service/simple-service.yml) を編集

```yml
apiVersion: v1
kind: Service
metadata:
  name: echo
spec:
  selector:
    app: echo
  ports:
    - name: http
      port: 80
```

- `$ kubectl apply -f simple-service.yml`
  - マニフェストファイルの変更を反映する
- [simple-ingress.yml](./5.9_service/simple-ingress.yml)
1. `$ kubectl apply -f simple-ingress.yml`
2. `$ kubectl get ingress`
3. `$ curl http://localhost -H 'Host: ch05.gihyo.local'`
    - backendに存在するecho Serviceから `Hello Docker!!` というレスポンスが返ってくる

- ↑のingress.ymlじゃ動かなかったので修正
  - [(参考) Kubernetesドキュメント / コンセプト / Service、負荷分散とネットワーキング / Ingress](https://kubernetes.io/ja/docs/concepts/services-networking/ingress/#ingress%E3%83%AA%E3%82%BD%E3%83%BC%E3%82%B9)
  - それでもcurlが `curl: (7) Failed to connect to localhost port 80 after 0 ms: Connection refused` のままで謎

```yml
apiVersion: extensions/v1beta1
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: echo
spec:
  rules:
    - host: ch05.gihyo.local
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
#              serviceName: echo
#              servicePort: 80
              service:
                name: echo
                port:
                  number: 80
```

---

- [simple-ingress.yml](./5.9_service/simple-ingress.yml) を編集

```yml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: echo
  annotations:
    nginx.ingress.kubernetes.io/server-snippet: |
      set $agentflag 0;

      if ($http_user_agent ~* "(Mobile)") {
        set $agentflag 1;
      }

      if ($agentflag = 1) {
        return 301 http://gihyo.jp/;
      }

spec:
  rules:
    - host: ch05.gihyo.local
      http:
        paths:
          - path: /
            backend:
              serviceName: echo
              servicePort: 80
```

- `nginx-ingress-controller` 独自の制御を `metadata.annotations` に設定可能
- `nginx.ingress.kubernetes.io/server-snippet` で、Nginxの記法が使える
- `$ curl http://localhost -H 'Host: ch05.gihyo.local' -H 'User-Agent: Mozilla/5.0 (iPhone; CPU iPhone OS 11_2_5 like Mac OS X) AppleWebKit/604.5.2 (KHTML, like Gecko) Version/11.0 Mobile/15D5046b Safari/604.1'`
  - `User-Agent` に `Mobile` が含まれているので、301でredirectされる

---

- `freshpod`
  - k8s上でデプロイされているコンテナイメージの更新を検知して、Podを自動で再デプロイするためのツール。
  - `$ kubectl get pod -l app=nginx -W`
    - `-w` Podの実行状態を監視しておくオプション
