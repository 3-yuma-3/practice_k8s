# [docker/k8s 実践コンテナ開発入門](https://www.amazon.co.jp/dp/4297100339)

## 7章 k8sの発展的な利用
### 7.1 k8sの様々なリソース
#### 7.1.1 Jon
- １つ以上のPodを作成し、指定された数のPodが正常に完了するまでを管理するリソース
- Jobによる全てのPodが正常に終了しても、Podは削除されずに保持されるため、終了後にPodのログや実行結果を分析可能
- [simple-job.yml](./7.1_k8s%E3%81%AE%E6%A7%98%E3%80%85%E3%81%AA%E3%83%AA%E3%82%BD%E3%83%BC%E3%82%B9/simple-job.yml)
- `restartPolicy` はPod終了時の再実行の設定。
  - JobリソースではAlwaysは設定できない。
  - NeverかOnFailureのどちらかを指定する必要がある
    - Never: 失敗時にPodを再作成して実行する
    - OnFailure: 失敗したPodを再実行する

#### 7.1.2 CronJob
- [simple-cronjob.yml](./7.1_k8s%E3%81%AE%E6%A7%98%E3%80%85%E3%81%AA%E3%83%AA%E3%82%BD%E3%83%BC%E3%82%B9/simple-cronjob.yml)

#### 7.1.3 Secret
- 機密情報の文字列をBase64エンコードした状態で扱える
- NginxのBasic認証の認証情報を記述したファイルをSecretで管理する
- opensslを利用してユーザー名とパスワードを暗号化し、その結果をBase64に変換する
- `$ echo "your_username: $(openssl passwd -quiet -crypt your_password)" | base64`
  - `> eW91cl91c2VybmFtZTogZFpaTzM1YVFHNHl0VQo=`
- [nginx-secret.yml](./7.1_k8s%E3%81%AE%E6%A7%98%E3%80%85%E3%81%AA%E3%83%AA%E3%82%BD%E3%83%BC%E3%82%B9/nginx-secret.yml)
- [basic-auth.yml](7.1_k8s%E3%81%AE%E6%A7%98%E3%80%85%E3%81%AA%E3%83%AA%E3%82%BD%E3%83%BC%E3%82%B9/basic-auth.yml)
1. `$ kubectl apply -f nginx-secret.yml`
2. `$ kubectl apply -f basic-auth.yml`
3. `$ curl http://127.0.0.1:30060`
    - 401が返る
4. `$ curl -i --user your_username:your_password http://127.0.0.1:30060`
    - 叩ける

- k8sのSecretの仕組みを理解している人であればSecretの内容をのぞけるので、リポジトリへのコミットや第3者がアクセス可能な場所には置かないこと

### 7.2 ユーザー管理とRole-Based Access Control(RBAC)

### 7.3 Helm
#### 7.3.1 Helomのセットアップ
- 本ではなくhelm公式を見てインストールする
  - [helmのインストール](../../../doc/helm/install.md)

#### 7.3.2 Helmの概念
- アプリケーションんオ挙動を制御するためのデフォルトの設定を `values.yaml` で定義している
- インストール時にデフォルトvalueファイルの値を変更したい場合は、デフォルトvalueファイルを上書きするための `カスタムvalueファイル` を作成する

```bash
chart_name/ --- templates/        マニフェストファイルのテンプレートディレクトリ
             |     |- xxxx.yaml   各種k8sリソースのマニフェストテンプレート
             |     |- _helper.tpl マニフェスト構築に利用されるテンプレートヘルパー
             |     |- NOTE.txt    Chart の利用方法などのドキュメントのテンプレート
             |
             |- charts/            依存するChartのディレクトリ
             |- Chart.yaml         Chart情報の定義ファイル
             |- values.yaml        Chartのデフォルトvalueファイル
```

#### 7.3.3 Chartをインストールする
- 試しにRedmineにログインするためのユーザーと初期パスワードに独自の値を設定する。
- [redmine.yml](./7.3_helm/redmine.yml)
- `$ helm install -f redmine.yaml -name redmine stable/redmine --version 4.0.0`
  - **動かないので下記にコマンド変更**
    - `$ helm install -f 7.3_helm/redmine.yml --generate-name stable/redmine`
- `$ helm ls`
- `$ kubectl get pod,service,deployment --selector release=redmine-1680349406`
  - chartが変わってるぽくて上書きできてない。NodePortが効いてない。
- `$ helm upgrade -f redmine.yaml redmine stable/redmine --version 4.0.0`

#### 7.3.4 Chartでアプリケーションをアンインストールする
1. `$ helm delete redmine`
2. `$ helm ls --all`
3. `$ helm rollback redmine 2`

#### 7.3.5 RBACに対応したアプリケーションをインストールする

