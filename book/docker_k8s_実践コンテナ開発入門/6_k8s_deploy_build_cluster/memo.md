# [docker/k8s 実践コンテナ開発入門](https://www.amazon.co.jp/dp/4297100339)

## 6章 k8sのデプロイ・クラスタ構築
### 6.3 master slave構成のMySQLをGKE上に構築する
- dokerではコンテナをデプロイしたホストにデータボリュームが配置されている必要があった
- k8sだとノード間をまたがってコンテナの再配置を行うのに手間がかかる。
- そこでホストから分離可能な外部ストレージをボリュームとして利用できる。
- Podが別のホストに再配置された場合、外部ストレージとしてのボリュームはデプロイされたホストに自動で割り当てられる。
- 以下のリソースで実現している
  - `PersistentVolume`
  - `PersistentVolumeClaim`
  - `StorageClass`
  - `statefulSet`

#### 6.3.1 PersistentVolumeとPersistentVolumeClaim
- `PersistentVolume` ストレージの実体
- `PersistentVolumeClaim` ストレージを論理的に抽象化したリソース
  - `PersistentVolume` に対して必要な容量を動的に確保できる

---

```yml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-example
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: ssd
  resources:
    requests:
      storage: 4Gi
```

- `accessModes` はpodからストレージへのマウントポリシー
  - `ReqdWriteOnce` どこか1つのノードからのR/Wマウントのみが許可される
  - `ReadOnlyMany`, `ReqdWriteMany` は `ReqdWriteOnce` よりも緩いマウントポリシーで、platformによっては使用できない。
- podからは `PersistentVolumeClaim` を直接マウントできる
  - データをボリュームに持っておけばpodを停止・再作成してもアプリケーションとしての状態は維持される。

#### 6.3.2 StorageClass
- `PersistentVolume` が確保するストレージの種類を定義できるリソース
- `PersistentVolumeClaim` の `storageClassName` で指定していた値の実体

#### 6.3.3 StatefulSet
- `Deployment` は永続データを持つ必要のないステートレスなアプリをデプロイするのに向いている
- `StatefulSet` はデータストアのように永続化するステートフルなアプリの管理に向いたリソース
- masterの設定
  - [mysql-master.yml](./6.3_master_slave_mysql_on_GKE/mysql-master.yml)
- slaveの設定
  - [mysql-slave.yml](./6.3_master_slave_mysql_on_GKE/mysql-slave.yml)
    - slaveはmasterの場所を知る必要があるため、 `MYSQL_MASTER_HOST` でMasterのService名である `mysql-master` を指定

### 6.4 todo api をGKE上に構築する
- [todo-api.yml](./6.4_todo_api_on_GKE/todo-api.yml)

### 6.5 todo web アプリケーションをGKE上に構築する
- [todo-web.yml](./6.5_todo_web_on_GKE/todo-web.yml)
    1. `assets` という名前の仮想Volumeを作成。 `emptyDir: {}` で、Podが削除されると仮想Volumeも削除される
    3. `Lifecycle` ではコンテナの開始時や終了時のタイミングで任意のコマンドを実行する。Dockerfileに手を加えずに実行可能。 `postStart` を使ってwebコンテナの開始時に、仮想Volumeにassetsファイルをコピーしている。Pod内でコンテナ間のディレクトリ共有をする上で有用。

### 6.6 Ingressでwebアプリケーションをインターネットに公開する
- [ingress.yml](./6.7_ingress/ingress.yml)

### 6.7 オンプレミス環境でのk8sクラスタの構築
- `kubespray`
  - オンプレでk8sクラスターの構築ができる
