## Container > NHN Container Service(NCS) > APIガイド

ワークロードとテンプレートを構成するためのAPIを記述します。
APIを使用するにはAPIエンドポイントとトークンが必要です。[APIの呼び出しおよび認証](ko/nhncloud/ko/public-api/api-authentication)を参照して、API使用のためのトークンを準備してください。

APIエンドポイントは次のとおりです。

| リージョン | ドメイン |
| --- | --- |
| 韓国(パンギョ)リージョン | [https://kr1-ncs.api.nhncloudservice.com](https://kr1-ncs.api.nhncloudservice.com) |

### APIレスポンス共通情報

すべてのAPIリクエストに <strong>200 OK</strong>でレスポンスします。詳細なレスポンス結果はレスポンス本文ヘッダを参照してください。

| 名前 | 種類 | 形式 | 説明 |
| --- | --- | --- | --- |
| header | Body | Object | APIレスポンス情報 |
| header.isSuccessful | Body | Boolean | true:正常<br>false:エラー |
| header.resultCode | Body | Integer | 200:正常<br>10000以上:エラー |
| header.resultMessage | Body | String | "SUCCESS":正常<br>その他:エラー原因メッセージ |

<details>
  <summary>例</summary>

```json
{
  "header": {
    "isSuccessful": true,
    "resultCode": 200,
    "resultMessage": "SUCCESS"
  }
}
```

</details>

> [注意]
> APIレスポンスにガイドに記載されていないフィールドが表示される場合があります。このようなフィールドはNHN Cloud内部用途で使用され、予告なしに変更される可能性があるため、使用しないでください。

## テンプレート

### テンプレートリストの表示

テンプレートリストを照会します。

```bash
GET /ncs/v1.0/appkeys/{appKey}/templates
x-nhn-authorization: {token}
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|
| page | Query | Integer | X | 照会するページ番号 |
| size | Query | Integer | X | 照会するページサイズ(default: 10) |
| disable\_containers | Query | Boolean | X | true:コンテナは除外して照会 <br>false:コンテナも含めて照会(default) |

#### レスポンス

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| X-Total-Count | Header | Integer | O | Appkeyに作成されているテンプレートの全体数 |
| templates | Body | Array | O | テンプレートリスト |
| templates.id | Body | UUID | O | テンプレートID |
| templates.version | Body | String | O | テンプレートのバージョン |
| templates.name | Body | String | O | テンプレート名 |
| templates.createdAt | Body | String | O | 作成時間(UTC) |
| templates.description | Body | String | X | テンプレートの説明 |
| templates.versionDescription | Body | String | O | テンプレートバージョンの説明 |
| templates.networks | Body | Array | O | テンプレートのネットワーク情報 |
| templates.networks.vpcId | Body | String | O | テンプレートのVPC ID |
| templates.networks.subnetId | Body | String | O | テンプレートのSubnet ID |
| templates.dnsConfig | Body | String List | X | コンテナに設定されたDNS Server情報 |
| templates.hostAliases | Body | List | X | コンテナ`/etc/hosts`に設定された情報 |
| templates.hostAliases.ip | Body | String | O | コンテナに設定されたhostnamesのIP |
| templates.hostAliases.hostnames | Body | String List | O | コンテナに設定されたIPのhostnames |
| templates.versionCount | Body | Integer | O | テンプレートのバージョン数 |
| templates.workloadCount | Body | Integer | O | テンプレートを使用中のワークロード数 |
| templates.containers | Body | Array | O | テンプレートのコンテナリスト |
| templates.containers.name | Body | String | O | コンテナ名 |
| templates.containers.type | Body | String | O | コンテナタイプ<br>\* normal:一般<br>\* init:初期化 |
| templates.containers.image | Body | String | O | コンテナイメージ |
| templates.containers.cpus | Body | Float | O | コンテナに割り当てるCPUの数 |
| templates.containers.memoryLimit | Body | Object | O | コンテナに割り当てるメモリ情報 |
| templates.containers.memoryLimit.hard | Body | Integer | O | コンテナに割り当てるメモリ(MiB) |
| templates.containers.gpuFlavor | Body | String | X | GPU Flavor情報<br>\* ncs1.g1m5<br>\* ncs1.g2m10 |
| templates.containers.ports | Body | Array | X | コンテナで使用するポート情報 |
| templates.containers.ports.containerPort | Body | Integer | O | コンテナポート |
| templates.containers.ports.protocol | Body | String | O | コンテナプロトコル<br>\* TCP<br>\* UDP<br>\* HTTP<br>\* HTTPS<br>\* TERMINATED\_HTTPS |
| templates.containers.command | Body | String List | X | コンテナが起動する時に実行されるコマンド |
| templates.containers.args | Body | String List | X | コンテナが実行される時に使用される引数 |
| templates.containers.workDirectory | Body | String | X | コンテナの作業ディレクトリ |
| templates.containers.env | Body | Array | X | コンテナ環境変数 |
| templates.containers.env.name | Body | String | O | コンテナ環境変数名 |
| templates.containers.env.value | Body | String | O | コンテナ環境変数の値 |
| templates.containers.postStart | Body | String List | X | コンテナ作成直後に実行されるコマンド |
| templates.containers.preStop | Body | String List | X | コンテナが終了する直前に実行されるコマンド |
| templates.containers.configs | Body | List | X | コンテナで使用するConfigMap情報 |
| templates.containers.configs.id | Body | Integer | O | ConfigMap ID |
| templates.containers.configs.type | Body | String | O | ConfigMap情報を取得するservice type<br>obs: Object Storage |
| templates.containers.configs.value | Body | String | O | オブジェクトURL |
| templates.containers.configs.mountPath | Body | String | O | コンテナマウントパス |
| templates.containers.secrets | Body | List | X | コンテナで使用するSecret情報 |
| templates.containers.secrets.type | Body | String | O | Secret情報を取得するservice type<br>skm: Secure Key Manager |
| templates.containers.secrets.value | Body | String | O | キーID |
| templates.containers.secrets.mountPath | Body | String | O | コンテナマウントパス |
| templates.containers.volumes | Body | Array | X | コンテナで使用するNASストレージ情報 |
| templates.containers.volumes.name | Body | String | O | ストレージ名 |
| templates.containers.volumes.path | Body | String | O | NASストレージ接続パス |
| templates.containers.volumes.mountPath | body | String | X | コンテナの接続パス |
| templates.containers.probe | Body | List | X | コンテナProbeの設定 |
| templates.containers.probe.type | Body | String | O | コンテナProbeタイプ<br>\* startup<br>\* liveness |
| templates.containers.probe.failureThreshold | Body | Integer | O | Probeの失敗基準 |
| templates.containers.probe.initialDelaySeconds | Body | Integer | O | Probeの開始待機時間 |
| templates.containers.probe.periodSeconds | Body | Integer | O | Probeの実行間隔 |
| templates.containers.probe.timeoutSeconds | Body | Integer | O | Probeの実行制限時間 |
| templates.containers.probe.exec | Body | String List | O | Probeの実行コマンド |
| templates.containers.stopTimeout | Body | Integer | X | 初期化コンテナ実行制限時間(秒) |

<details>
  <summary>例</summary>

```json
{
  "header": {
    "resultCode": 200,
    "resultMessage": "SUCCESS",
    "isSuccessful": true
  },
  "templates": [
    {
      "createdAt": "2024-10-24T05:43:26.029Z",
      "updatedAt": "2024-10-24T05:43:26.029Z",
      "id": "bf095f99-1547-48f4-b57d-1124e853f6e2",
      "name": "nginx-template",
      "namespace": "abd7f92e-3353-4b45-944c-510a97ef89c9",
      "containers": [
        {
          "id": "51be9a5d-4737-4323-ae57-0ade225f4f4d",
          "name": "nginx",
          "image": "nginx:latest",
          "imageRegistry": "other",
          "imageRegistryType": "public",
          "cpus": 0.25,
          "memoryLimit": {
            "hard": 256
          },
          "ports": [
            {
              "hostPort": 80,
              "containerPort": 80,
              "protocol": "TCP"
            }
          ],
          "restartCount": 0,
          "type": "normal"
        }
      ],
      "networks": [
        {
          "vpcId": "aeeafe4e-287d-4dd4-b91a-294b87688457",
          "subnetId": "abd7f92e-3353-4b45-944c-510a97ef89c9"
        }
      ],
      "dnsConfig": [
        "223.255.201.241",
        "211.50.32.6"
      ],
      "versionCount": 2,
      "version": "second",
      "sourceVersion": "first"
    },
    {
      "createdAt": "2024-10-24T05:44:25.292Z",
      "updatedAt": "2024-10-24T05:44:25.292Z",
      "id": "edfabd08-187b-4195-bed9-dad5d0b4d854",
      "name": "ubuntu",
      "namespace": "abd7f92e-3353-4b45-944c-510a97ef89c9",
      "containers": [
        {
          "id": "aeb71431-01b9-4b4b-8f2d-326b7863d0a9",
          "name": "ubuntu",
          "image": "ubuntu:latest",
          "imageRegistry": "other",
          "imageRegistryType": "public",
          "cpus": 1,
          "memoryLimit": {
            "hard": 256
          },
          "command": [
            "bash",
            "-c",
            "sleep infinity"
          ],
          "restartCount": 0,
          "type": "normal"
    }
      ],
      "networks": [
        {
          "vpcId": "aeeafe4e-287d-4dd4-b91a-294b87688457",
          "subnetId": "abd7f92e-3353-4b45-944c-510a97ef89c9"
        }
      ],
      "dnsConfig": [
        "223.255.201.241",
        "211.50.32.6"
      ],
      "versionCount": 1,
      "version": "1",
      "versionDescription": "ubuntu test"
    }
  ]
}
```

</details>

### テンプレートの表示

個別テンプレート情報を照会します。

```bash
GET /ncs/v1.0/appkeys/{appKey}/templates/{templateId}
x-nhn-authorization: {token}
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| templateId | URL | String | O | テンプレートID |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|

#### レスポンス

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| template | Body | Object | O | テンプレート情報 |
| template.id | Body | UUID | O | テンプレートID |
| template.version | Body | String | O | テンプレートバージョン |
| template.name | Body | String | O | テンプレート名 |
| template.createdAt | Body | String | O | 作成時間(UTC) |
| template.description | Body | String | X | テンプレートの説明 |
| template.versionDescription | Body | String | X | テンプレートバージョンの説明 |
| template.networks | Body | Array | O | テンプレートのネットワーク情報 |
| template.networks.vpcId | Body | String | O | テンプレートのVPC ID |
| template.networks.subnetId | Body | String | O | テンプレートのSubnet ID |
| template.dnsConfig | Body | String List | X | コンテナに設定されたDNS Server情報 |
| template.hostAliases | Body | List | X | コンテナ`/etc/hosts`に設定された情報 |
| template.hostAliases.ip | Body | String | O | コンテナに設定されたhostnamesのIP |
| template.hostAliases.hostnames | Body | String List | O | コンテナに設定されたIPのhostnames |
| template.versionCount | Body | Integer | O | テンプレートのバージョン数 |
| template.workloadCount | Body | Integer | O | テンプレートを使用中のワークロード数 |
| template.containers | Body | Array | O | テンプレートのコンテナリスト |
| template.containers.name | Body | String | O | コンテナ名 |
| template.containers.type | Body | String | O | コンテナタイプ<br>\* normal:一般<br>\* init:初期化 |
| template.containers.image | Body | String | O | コンテナイメージ |
| template.containers.cpus | Body | Float | O | コンテナに割り当てるCPUの数 |
| template.containers.memoryLimit | Body | Object | O | コンテナに割り当てるメモリ情報 |
| template.containers.memoryLimit.hard | Body | Integer | O | コンテナに割り当てるメモリ(MiB) |
| template.containers.gpuFlavor | Body | String | X | GPU Flavor情報<br>\* ncs1.g1m5<br>\* ncs1.g2m10 |
| template.containers.ports | Body | Array | X | コンテナで使用するポート情報 |
| template.containers.ports.containerPort | Body | Integer | O | コンテナポート |
| template.containers.ports.protocol | Body | String | O | コンテナプロトコル<br>\* TCP<br>\* UDP<br>\* HTTP<br>\* HTTPS<br>\* TERMINATED\_HTTPS |
| template.containers.command | Body | String List | X | コンテナが起動する時に実行されるコマンド |
| template.containers.args | Body | String List | X | コンテナが起動する時に使用される引数 |
| template.containers.workDirectory | Body | String | X | コンテナの作業ディレクトリ |
| template.containers.env | Body | Array | X | コンテナ環境変数 |
| template.containers.env.name | Body | String | O | コンテナ環境変数名 |
| template.containers.env.value | Body | String | O | コンテナ環境変数の値 |
| template.containers.postStart | Body | String List | X | コンテナ作成直後に実行されるコマンド |
| template.containers.preStop | Body | String List | X | コンテナが終了する直前に実行されるコマンド |
| template.containers.configs | Body | List | X | コンテナで使用するConfigMap情報 |
| template.containers.configs.id | Body | Integer | O | ConfigMap ID |
| template.containers.configs.type | Body | String | O | ConfigMap情報を取得するservice type<br>obs: Object Storage |
| template.containers.configs.value | Body | String | O | オブジェクトURL |
| template.containers.configs.mountPath | Body | String | O | コンテナマウントパス |
| template.containers.secrets | Body | List | X | コンテナで使用するSecret情報 |
| template.containers.secrets.type | Body | String | O | Secret情報を取得するservice type<br>skm: Secure Key Manager |
| template.containers.secrets.value | Body | String | O | キーID |
| template.containers.secrets.mountPath | Body | String | O | コンテナマウントパス |
| template.containers.volumes | Body | Array | X | コンテナで使用するNASストレージ情報 |
| template.containers.volumes.name | Body | String | O | ストレージ名 |
| template.containers.volumes.path | Body | String | O | NASストレージ接続パス |
| template.containers.volumes.mountPath | body | String | X | コンテナの接続パス |
| template.containers.probe | Body | List | X | コンテナProbeの設定 |
| template.containers.probe.type | Body | String | O | コンテナProbeタイプ<br>\* startup<br>\* liveness |
| template.containers.probe.failureThreshold | Body | Integer | O | Probeの失敗基準 |
| template.containers.probe.initialDelaySeconds | Body | Integer | O | Probeの開始待機時間 |
| template.containers.probe.periodSeconds | Body | Integer | O | Probeの実行間隔 |
| template.containers.probe.timeoutSeconds | Body | Integer | O | Probeの実行制限時間 |
| template.containers.probe.exec | Body | String List | O | Probeの実行コマンド |
| template.containers.stopTimeout | Body | Integer | X | 初期化コンテナ実行制限時間(秒) |

<details>
  <summary>例</summary>

```json
{
  "header": {
    "resultCode": 200,
    "resultMessage": "SUCCESS",
    "isSuccessful": true
  },
  "template": {
    "createdAt": "2024-10-24T05:43:26.029Z",
    "updatedAt": "2024-10-24T05:43:26.029Z",
    "id": "bf095f99-1547-48f4-b57d-1124e853f6e2",
    "name": "nginx-template",
    "namespace": "abd7f92e-3353-4b45-944c-510a97ef89c9",
    "containers": [
      {
        "id": "51be9a5d-4737-4323-ae57-0ade225f4f4d",
        "name": "nginx",
        "image": "nginx:latest",
        "imageRegistry": "other",
        "imageRegistryType": "public",
        "cpus": 0.25,
        "memoryLimit": {
          "hard": 256
        },
        "ports": [
          {
            "hostPort": 80,
            "containerPort": 80,
            "protocol": "TCP"
          }
        ],
        "restartCount": 0,
        "type": "normal"
      }
    ],
    "networks": [
      {
        "vpcId": "aeeafe4e-287d-4dd4-b91a-294b87688457",
        "subnetId": "abd7f92e-3353-4b45-944c-510a97ef89c9"
      }
    ],
    "dnsConfig": [
      "223.255.201.241",
      "211.50.32.6"
    ],
    "versionCount": 2,
    "version": "second",
    "sourceVersion": "first"
  }
}
```

</details>

### テンプレートを作成する

テンプレートを作成します。

```bash
POST /ncs/v1.0/appkeys/{appKey}/templates
Content-Type: application/json
x-nhn-authorization: {token}
```

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|
| template | Body | Array | O | テンプレート情報 |
| template.name | Body | String | O | テンプレート名 |
| template.version | Body | String | X | テンプレートバージョン |
| template.description | Body | String | X | テンプレート説明 |
| template.versionDescription | Body | String | X | テンプレートバージョン説明 |
| template.networks | Body | Array | O | テンプレートのネットワーク情報 |
| template.networks.vpcId | Body | String | O | テンプレートのVPC ID |
| template.networks.subnetId | Body | String | O | テンプレートのSubnet ID |
| template.dnsConfig | Body | String List | X | コンテナで使用するDNS Server情報(最大3個設定可能) |
| template.hostAliases | Body | List | X | コンテナ`/etc/hosts`に設定する情報 |
| template.hostAliases.ip | Body | String | O | hostnamesで使用するIP情報 |
| template.hostAliases.hostnames | Body | String List | O | hostnamesで使用するhost情報 |
| template.containers | Body | Array | O | テンプレートのコンテナリスト |
| template.containers.name | Body | String | O | コンテナ名 |
| template.containers.type | Body | String | X | コンテナタイプ(default: normal)<br>\* normal:一般<br>\* init:初期化 |
| template.containers.image | Body | String | O | コンテナイメージ |
| template.containers.imageRegistryCredentials | Body | Object | X | Privateレジストリにアクセス可能な情報 |
| template.containers.imageRegistryCredentials.username | Body | String | O | PrivateレジストリID |
| template.containers.imageRegistryCredentials.password | Body | String | O | Privateレジストリパスワード |
| template.containers.cpus | Body | Float | O | コンテナに割り当てるCPUの数 |
| template.containers.memoryLimit | Body | Object | O | コンテナに割り当てるメモリ情報 |
| template.containers.memoryLimit.hard | Body | Integer | O | コンテナに割り当てるメモリ(MiB) |
| template.containers.gpuFlavor | Body | String | X | GPU Flavor情報<br>\* ncs1.g1m5<br>\* ncs1.g2m10 |
| template.containers.ports | Body | Array | X | コンテナで使用するポート情報 |
| template.containers.ports.containerPort | Body | Integer | O | コンテナポート |
| template.containers.ports.protocol | Body | String | O | コンテナプロトコル<br>\* TCP<br>\* UDP<br>\* HTTP<br>\* HTTPS<br>\* TERMINATED\_HTTPS |
| template.containers.command | Body | String List | X | コンテナが起動する時に実行されるコマンド |
| template.containers.args | Body | String List | X | コンテナが起動する時に使用される引数 |
| template.containers.workDirectory | Body | String | X | コンテナの作業ディレクトリ |
| template.containers.env | Body | Array | X | コンテナ環境変数 |
| template.containers.env.name | Body | String | O | コンテナ環境変数名 |
| template.containers.env.value | Body | String | O | コンテナ環境変数の値 |
| template.containers.postStart | Body | String List | X | コンテナ作成直後に実行されるコマンド |
| template.containers.preStop | Body | String List | X | コンテナが終了する直前に実行されるコマンド |
| template.containers.configs | Body | List | X | コンテナで使用するConfigMap情報(最大10個) |
| template.containers.configs.id | Body | Integer | O | ConfigMap ID |
| template.containers.configs.type | Body | String | O | ConfigMap情報を取得するservice type<br>obs: Object Storage |
| template.containers.configs.appKey | Body | String | O | Object Storage AppKey |
| template.containers.configs.userAccessKeyId | Body | String | O | Object StorageサービスにアクセスするユーザーのUser Access Key |
| template.containers.configs.secretAccessKey | Body | String | O | Object StorageサービスにアクセスするユーザーのSecret Access Key |
| template.containers.configs.value | Body | String | O | オブジェクトURL |
| template.containers.configs.mountPath | Body | String | O | コンテナマウントパス |
| template.containers.secrets | Body | List | X | コンテナで使用するSecret情報(最大10個) |
| template.containers.secrets.type | Body | String | O | Secret情報を取得するservice type<br>skm: Secure Key Manager |
| template.containers.secrets.value | Body | String | O | キーID |
| template.containers.secrets.mountPath | Body | String | O | コンテナマウントパス |
| template.containers.volumes | Body | Array | X | コンテナで使用するNASストレージ情報 |
| template.containers.volumes.name | Body | String | O | ストレージ名 |
| template.containers.volumes.path | Body | String | O | NASストレージ接続パス |
| template.containers.volumes.mountPath | body | String | X | コンテナの接続パス(default: /mnt) |
| template.containers.probe | Body | List | X | コンテナProbeの設定 |
| template.containers.probe.type | Body | String | O | コンテナProbeタイプ<br>\* startup<br>\* liveness |
| template.containers.probe.failureThreshold | Body | Integer | O | Probeの失敗基準 |
| template.containers.probe.initialDelaySeconds | Body | Integer | O | Probeの開始待機時間 |
| template.containers.probe.periodSeconds | Body | Integer | O | Probeの実行間隔<br>timeoutSecondsより小さい値に設定する必要があります。 |
| template.containers.probe.timeoutSeconds | Body | Integer | O | Probe実行制限時間<br>periodSecondsより大きい値に設定する必要があります。 |
| template.containers.probe.exec | Body | String List | O | Probeの実行コマンド |
| template.containers.stopTimeout | Body | Integer | X | 初期化コンテナ実行制限時間(秒)<br>\* 30 \～120 (default:30)|


<details>
  <summary>例</summary>

```json
{
  "template": {
    "containers": [
      {
        "cpus": 0.25,
        "image": "nginx:latest",
        "memoryLimit": {
          "hard": 256
        },
        "name": "nginx",
        "ports": [
          {
            "containerPort": 80,
            "protocol": "tcp"
          }
        ]
      }
    ],
    "description": "api template",
    "name": "api-template",
    "networks": [
      {
        "subnetId": "abd7f92e-3353-4b45-944c-510a97ef89c9",
        "vpcId": "aeeafe4e-287d-4dd4-b91a-294b87688457"
      }
    ],
    "version": "api-1"
  }
}
```

</details>

#### レスポンス

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| template | Body | Object | O | テンプレート情報 |
| template.id | Body | UUID | O | テンプレートID |
| template.name | Body | String | O | テンプレート名 |
| template.version | Body | String | O | テンプレートバージョン |
| template.createdAt | Body | String | O | 作成時間(UTC) |
| template.description | Body | String | X | テンプレートの説明 |
| template.versionDescription | Body | String | X | テンプレートバージョンの説明 |
| template.networks | Body | Array | O | テンプレートのネットワーク情報 |
| template.networks.vpcId | Body | String | O | テンプレートのVPC ID |
| template.networks.subnetId | Body | String | O | テンプレートのSubnet ID |
| template.dnsConfig | Body | String List | X | コンテナに設定されたDNS Server情報 |
| template.hostAliases | Body | List | X | コンテナ`/etc/hosts`に設定された情報 |
| template.hostAliases.ip | Body | String | O | コンテナに設定されたhostnamesのIP |
| template.hostAliases.hostnames | Body | String List | O | コンテナに設定されたIPのhostnames |
| template.containers | Body | Array | O | テンプレートのコンテナリスト |
| template.containers.name | Body | String | O | コンテナ名 |
| template.containers.type | Body | String | O | コンテナタイプ<br>\* normal:一般<br>\* init:初期化 |
| template.containers.image | Body | String | O | コンテナイメージ |
| template.containers.cpus | Body | Float | O | コンテナに割り当てるCPUの数 |
| template.containers.memoryLimit | Body | Object | O | コンテナに割り当てるメモリ情報 |
| template.containers.memoryLimit.hard | Body | Integer | O | コンテナ割り当てるメモリ(MiB) |
| template.containers.gpuFlavor | Body | String | X | GPU Flavor情報<br>\* ncs1.g1m5<br>\* ncs1.g2m10 |
| template.containers.ports | Body | Array | X | コンテナで使用するポート情報 |
| template.containers.ports.containerPort | Body | Integer | O | コンテナポート |
| template.containers.ports.protocol | Body | String | O | コンテナプロトコル<br>\* TCP<br>\* UDP<br>\* HTTP<br>\* HTTPS<br>\* TERMINATED\_HTTPS |
| template.containers.command | Body | String List | X | コンテナが起動する時に実行されるコマンド |
| template.containers.args | Body | String List | X | コンテナが起動する時に使用される引数 |
| template.containers.workDirectory | Body | String | X | コンテナの作業ディレクトリ |
| template.containers.env | Body | Array | X | コンテナ環境変数 |
| template.containers.env.name | Body | String | O | コンテナ環境変数名 |
| template.containers.env.value | Body | String | O | コンテナ環境変数の値 |
| template.containers.postStart | Body | String List | X | コンテナ作成直後に実行されるコマンド |
| template.containers.preStop | Body | String List | X | コンテナが終了する直前に実行されるコマンド |
| template.containers.configs | Body | List | X | コンテナで使用するConfigMap情報 |
| template.containers.configs.id | Body | Integer | O | ConfigMap ID |
| template.containers.configs.type | Body | String | O | ConfigMap情報を取得するservice type<br>obs: Object Storage |
| template.containers.configs.value | Body | String | O | オブジェクトURL |
| template.containers.configs.mountPath | Body | String | O | コンテナマウントパス |
| template.containers.secrets | Body | List | X | コンテナで使用するSecret情報 |
| template.containers.secrets.type | Body | String | O | Secret情報を取得するservice type<br>skm: Secure Key Manager |
| template.containers.secrets.value | Body | String | O | キーID |
| template.containers.secrets.mountPath | Body | String | O | コンテナマウントパス |
| template.containers.volumes | Body | Array | X | コンテナで使用するNASストレージ情報 |
| template.containers.volumes.name | Body | String | O | ストレージ名 |
| template.containers.volumes.path | Body | String | O | NASストレージ接続パス |
| template.containers.volumes.mountPath | body | String | X | コンテナの接続パス |
| template.containers.probe | Body | List | X | コンテナProbeの設定 |
| template.containers.probe.type | Body | String | O | コンテナProbeタイプ<br>\* startup<br>\* liveness |
| template.containers.probe.failureThreshold | Body | Integer | O | Probeの失敗基準 |
| template.containers.probe.initialDelaySeconds | Body | Integer | O | Probeの開始待機時間 |
| template.containers.probe.periodSeconds | Body | Integer | O | Probeの実行間隔 |
| template.containers.probe.timeoutSeconds | Body | Integer | O | Probeの実行制限時間 |
| template.containers.probe.exec | Body | String List | O | Probeの実行コマンド |
| template.containers.stopTimeout | Body | Integer | X | 初期化コンテナ実行制限時間(秒) |

<details>
  <summary>例</summary>

```json
{
  "header": {
    "resultCode": 200,
    "resultMessage": "SUCCESS",
    "isSuccessful": true
  },
  "template": {
    "createdAt": "2024-10-24T05:47:00.693Z",
    "updatedAt": "2024-10-24T05:47:00.693Z",
    "id": "89ab3d2f-385a-4322-8694-32cb7955f8de",
    "name": "api-template",
    "namespace": "abd7f92e-3353-4b45-944c-510a97ef89c9",
    "description": "api template",
    "containers": [
      {
        "id": "25f2753c-b7e3-42a3-ad8c-a0cfe4e6155f",
        "name": "nginx",
        "image": "nginx:latest",
        "imageRegistry": "other",
        "imageRegistryType": "public",
        "cpus": 0.25,
        "memoryLimit": {
          "hard": 256
        },
        "ports": [
          {
            "containerPort": 80,
            "protocol": "TCP"
          }
        ],
        "restartCount": 0,
        "type": "normal"
      }
    ],
    "networks": [
      {
        "vpcId": "aeeafe4e-287d-4dd4-b91a-294b87688457",
        "subnetId": "abd7f92e-3353-4b45-944c-510a97ef89c9"
      }
    ],
    "dnsConfig": [
      "223.255.201.241",
      "211.50.32.6"
    ],
    "version": "api-1"
  }
}
```

</details>

### テンプレートの削除

テンプレートを削除します。

```bash
DELETE /ncs/v1.0/appkeys/{appKey}/templates/{templateId}
x-nhn-authorization: {token}
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| templateId | URL | String | O | テンプレートID |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|

#### レスポンス

このAPIは共通情報のみレスポンスします。

### テンプレートバージョンリスト表示

```bash
GET /ncs/v1.0/appkeys/{appKey}/templates/{templateId}/versions
x-nhn-authorization: {token}
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| templateId | Path | String | O | テンプレートID |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|
| q | Query | String | X | 検索引数 |
| page | Query | Integer | X | 照会するページ番号 |
| size | Query | Integer | X | 照会するページサイズ(default:10) |
| sort | Query | String | X | ソート基準となるフィールド名<br>降順ソートの場合、フィールド名の前に`-`を付ける<br>例) `sort=-name` |

#### レスポンス

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| X-Total-Count | Header | Integer | O | テンプレートのバージョン数 |
| templates | Body | Array | O | テンプレートバージョンリスト |
| templates.id | Body | UUID | O | テンプレートID |
| templates.version | Body | String | O | テンプレートバージョン |
| templates.sourceVersion | Body | String | O | テンプレート基準バージョン |
| templates.name | Body | String | O | テンプレート名 |
| templates.createdAt | Body | String | O | 作成時間(UTC) |
| templates.description | Body | String | X | テンプレートの説明 |
| templates.versionDescription | Body | String | X | テンプレートバージョンの説明 |
| templates.networks | Body | Array | O | テンプレートのネットワーク情報 |
| templates.networks.vpcId | Body | String | O | テンプレートのVPC ID |
| templates.networks.subnetId | Body | String | O | テンプレートのSubnet ID |
| templates.dnsConfig | Body | String List | X | コンテナに設定されたDNS Server情報 |
| templates.hostAliases | Body | List | X | コンテナ`/etc/hosts`に設定された情報 |
| templates.hostAliases.ip | Body | String | O | コンテナに設定されたhostnamesのIP |
| templates.hostAliases.hostnames | Body | String List | O | コンテナに設定されたIPのhostnames |
| templates.versionCount | Body | Integer | O | テンプレートのバージョン数 |
| templates.workloadCount | Body | Integer | O | テンプレートを使用中のワークロード数 |
| templates.containers | Body | Array | O | テンプレートのコンテナリスト |
| templates.containers.name | Body | String | O | コンテナ名 |
| templates.containers.type | Body | String | O | コンテナタイプ<br>\* normal:一般<br>\* init:初期化 |
| templates.containers.image | Body | String | O | コンテナイメージ |
| templates.containers.cpus | Body | Float | O | コンテナに割り当てるCPUの数 |
| templates.containers.memoryLimit | Body | Object | O | コンテナに割り当てるメモリ情報 |
| templates.containers.memoryLimit.hard | Body | Integer | O | コンテナに割り当てるメモリ(MiB) |
| templates.containers.gpuFlavor | Body | String | X | GPU Flavor情報<br>\* ncs1.g1m5<br>\* ncs1.g2m10 |
| templates.containers.ports | Body | Array | X | コンテナで使用するポート情報 |
| templates.containers.ports.containerPort | Body | Integer | O | コンテナポート |
| templates.containers.ports.protocol | Body | String | O | コンテナプロトコル<br>\* TCP<br>\* UDP<br>\* HTTP<br>\* HTTPS<br>\* TERMINATED\_HTTPS |
| templates.containers.command | Body | String List | X | コンテナが起動する時に実行されるコマンド |
| templates.containers.args | Body | String List | X | コンテナが起動する時に使用される引数 |
| templates.containers.workDirectory | Body | String | X | コンテナの作業ディレクトリ |
| templates.containers.env | Body | Array | X | コンテナ環境変数 |
| templates.containers.env.name | Body | String | O | コンテナ環境変数名 |
| templates.containers.env.value | Body | String | O | コンテナ環境変数の値 |
| templates.containers.postStart | Body | String List | X | コンテナ作成直後に実行されるコマンド |
| templates.containers.preStop | Body | String List | X | コンテナが終了する前に実行されるコマンド |
| templates.containers.configs | Body | List | X | コンテナで使用するConfigMap情報 |
| templates.containers.configs.id | Body | Integer | O | ConfigMap ID |
| templates.containers.configs.type | Body | String | O | ConfigMap情報を取得するservice type<br>obs: Object Storage |
| templates.containers.configs.value | Body | String | O | オブジェクトURL |
| templates.containers.configs.mountPath | Body | String | O | コンテナマウントパス |
| templates.containers.secrets | Body | List | X | コンテナで使用するSecret情報 |
| templates.containers.secrets.type | Body | String | O | Secret情報を取得するservice type<br>skm: Secure Key Manager |
| templates.containers.secrets.value | Body | String | O | キーID |
| templates.containers.secrets.mountPath | Body | String | O | コンテナマウントパス |
| templates.containers.volumes | Body | Array | X | コンテナで使用するNASストレージ情報 |
| templates.containers.volumes.name | Body | String | O | ストレージ名 |
| templates.containers.volumes.path | Body | String | O | NASストレージ接続パス |
| templates.containers.volumes.mountPath | body | String | X | コンテナの接続パス |
| templates.containers.probe | Body | List | X | コンテナProbeの設定 |
| templates.containers.probe.type | Body | String | O | コンテナProbeタイプ<br>\* startup<br>\* liveness |
| templates.containers.probe.failureThreshold | Body | Integer | O | Probeの失敗基準 |
| templates.containers.probe.initialDelaySeconds | Body | Integer | O | Probeの開始待機時間 |
| templates.containers.probe.periodSeconds | Body | Integer | O | Probeの実行間隔 |
| templates.containers.probe.timeoutSeconds | Body | Integer | O | Probeの実行制限時間 |
| templates.containers.probe.exec | Body | String List | O | Probeの実行コマンド |
| templates.containers.stopTimeout | Body | Integer | X | 初期化コンテナ実行制限時間(秒) |

<details>
  <summary>例</summary>

```json
{
  "header": {
    "resultCode": 200,
    "resultMessage": "SUCCESS",
    "isSuccessful": true
  },
  "templates": [
    {
      "createdAt": "2024-10-24T05:24:14.053Z",
      "updatedAt": "2024-10-24T05:24:14.053Z",
      "id": "bf095f99-1547-48f4-b57d-1124e853f6e2",
      "name": "nginx-template",
      "namespace": "abd7f92e-3353-4b45-944c-510a97ef89c9",
      "containers": [
        {
          "id": "51be9a5d-4737-4323-ae57-0ade225f4f4d",
          "name": "nginx",
          "image": "nginx:latest",
          "imageRegistry": "other",
          "imageRegistryType": "public",
          "cpus": 0.25,
          "memoryLimit": {
            "hard": 256
          },
          "ports": [
            {
              "hostPort": 80,
              "containerPort": 80,
              "protocol": "TCP"
            }
          ],
          "restartCount": 0,
          "probe": [
            {
              "type": "liveness",
              "exec": [
                "bash",
                "-c",
                "curl -f http://localhost || exit 1"
              ],
              "initialDelaySeconds": 5,
              "failureThreshold": 3,
              "timeoutSeconds": 1,
              "periodSeconds": 10
            },
            {
              "type": "startup",
              "exec": [
                "bash",
                "-c",
                "curl -f http://localhost || exit 1"
              ],
              "initialDelaySeconds": 5,
              "failureThreshold": 3,
              "timeoutSeconds": 1,
              "periodSeconds": 10
            }
          ],
          "type": "normal"
        }
      ],
      "networks": [
        {
          "vpcId": "aeeafe4e-287d-4dd4-b91a-294b87688457",
          "subnetId": "abd7f92e-3353-4b45-944c-510a97ef89c9"
        }
      ],
      "dnsConfig": [
        "223.255.201.241",
        "211.50.32.6"
      ],
      "versionCount": 2,
      "version": "first"
    },
    {
      "createdAt": "2024-10-24T05:43:26.029Z",
      "updatedAt": "2024-10-24T05:43:26.029Z",
      "id": "bf095f99-1547-48f4-b57d-1124e853f6e2",
      "name": "nginx-template",
      "namespace": "abd7f92e-3353-4b45-944c-510a97ef89c9",
      "containers": [
        {
          "id": "51be9a5d-4737-4323-ae57-0ade225f4f4d",
          "name": "nginx",
          "image": "nginx:latest",
          "imageRegistry": "other",
          "imageRegistryType": "public",
          "cpus": 0.25,
          "memoryLimit": {
            "hard": 256
          },
          "ports": [
            {
              "hostPort": 80,
              "containerPort": 80,
              "protocol": "TCP"
            }
          ],
          "restartCount": 0,
          "type": "normal"
        }
      ],
      "networks": [
        {
          "vpcId": "aeeafe4e-287d-4dd4-b91a-294b87688457",
          "subnetId": "abd7f92e-3353-4b45-944c-510a97ef89c9"
        }
      ],
      "dnsConfig": [
        "223.255.201.241",
        "211.50.32.6"
      ],
      "versionCount": 2,
      "version": "second",
      "sourceVersion": "first"
    }
  ]
}
```

</details>

### テンプレートバージョン表示

個別テンプレートバージョン情報を照会します。

```bash
GET /ncs/v1.0/appkeys/{appKey}/templates/{templateId}/versions/{version}
x-nhn-authorization: {token}
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| templateId | URL | String | O | テンプレートID |
| version | URL | String | O | テンプレートバージョン |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|

#### レスポンス

* テンプレートの詳細照会と同じ

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| template | Body | Object | O | テンプレートバージョン情報 |
| template.id | Body | UUID | O | テンプレートID |
| template.version | Body | String | O | テンプレートバージョン |
| template.sourceVersion | Body | String | O | テンプレート基準バージョン |
| template.name | Body | String | O | テンプレート名 |
| template.createdAt | Body | String | O | 作成時間(UTC) |
| template.description | Body | String | X | テンプレートの説明 |
| template.versionDescription | Body | String | X | テンプレートバージョンの説明 |
| template.networks | Body | Array | O | テンプレートのネットワーク情報 |
| template.networks.vpcId | Body | String | O | テンプレートのVPC ID |
| template.networks.subnetId | Body | String | O | テンプレートのSubnet ID |
| template.dnsConfig | Body | String List | X | コンテナに設定されたDNS Server情報 |
| template.hostAliases | Body | List | X | コンテナ`/etc/hosts`に設定された情報 |
| template.hostAliases.ip | Body | String | O | コンテナに設定されたhostnamesのIP |
| template.hostAliases.hostnames | Body | String List | O | コンテナに設定されたIPのhostnames |
| template.versionCount | Body | Integer | O | テンプレートのバージョン数 |
| template.workloadCount | Body | Integer | O | テンプレートを使用中のワークロード数 |
| template.containers | Body | Array | O | テンプレートのコンテナリスト |
| template.containers.name | Body | String | O | コンテナ名 |
| template.containers.type | Body | String | O | コンテナタイプ<br>\* normal:一般<br>\* init:初期化 |
| template.containers.image | Body | String | O | コンテナイメージ |
| template.containers.cpus | Body | Float | O | コンテナに割り当てるCPUの数 |
| template.containers.memoryLimit | Body | Object | O | コンテナに割り当てるメモリ情報 |
| template.containers.memoryLimit.hard | Body | Integer | O | コンテナに割り当てるメモリ(MiB) |
| template.containers.gpuFlavor | Body | String | X | GPU Flavor情報<br>\* ncs1.g1m5<br>\* ncs1.g2m10 |
| template.containers.ports | Body | Array | X | コンテナで使用するポート情報 |
| template.containers.ports.containerPort | Body | Integer | O | コンテナポート |
| template.containers.ports.protocol | Body | String | O | コンテナプロトコル<br>\* TCP<br>\* UDP<br>\* HTTP<br>\* HTTPS<br>\* TERMINATED\_HTTPS |
| template.containers.command | Body | String List | X | コンテナが起動する時に実行されるコマンド |
| template.containers.args | Body | String List | X | コンテナが起動する時に使用される引数 |
| template.containers.workDirectory | Body | String | X | コンテナの作業ディレクトリ |
| template.containers.env | Body | Array | X | コンテナ環境変数 |
| template.containers.env.name | Body | String | O | コンテナ環境変数名 |
| template.containers.env.value | Body | String | O | コンテナ環境変数の値 |
| template.containers.postStart | Body | String List | X | コンテナ作成直後に実行されるコマンド |
| template.containers.preStop | Body | String List | X | コンテナが終了する直前に実行されるコマンド |
| template.containers.configs | Body | List | X | コンテナで使用するConfigMap情報 |
| template.containers.configs.id | Body | Integer | O | ConfigMap ID |
| template.containers.configs.type | Body | String | O | ConfigMap情報を取得するservice type<br>obs: Object Storage |
| template.containers.configs.value | Body | String | O | オブジェクトURL |
| template.containers.configs.mountPath | Body | String | O | コンテナマウントパス |
| template.containers.secrets | Body | List | X | コンテナで使用するSecret情報 |
| template.containers.secrets.type | Body | String | O | Secret情報を取得するservice type<br>skm: Secure Key Manager |
| template.containers.secrets.value | Body | String | O | キーID |
| template.containers.secrets.mountPath | Body | String | O | コンテナマウントパス |
| template.containers.volumes | Body | Array | X | コンテナで使用するNASストレージ情報 |
| template.containers.volumes.name | Body | String | O | ストレージ名 |
| template.containers.volumes.path | Body | String | O | NASストレージ接続パス |
| template.containers.volumes.mountPath | body | String | X | コンテナの接続パス |
| template.containers.probe | Body | List | X | コンテナProbeの設定 |
| template.containers.probe.type | Body | String | O | コンテナProbeタイプ<br>\* startup<br>\* liveness |
| template.containers.probe.failureThreshold | Body | Integer | O | Probeの失敗基準 |
| template.containers.probe.initialDelaySeconds | Body | Integer | O | Probeの開始待機時間 |
| template.containers.probe.periodSeconds | Body | Integer | O | Probeの実行間隔 |
| template.containers.probe.timeoutSeconds | Body | Integer | O | Probeの実行制限時間 |
| template.containers.probe.exec | Body | String List | O | Probeの実行コマンド |
| template.containers.stopTimeout | Body | Integer | X | 初期化コンテナ実行制限時間(秒) |

<details>
  <summary>例</summary>

```json
{
  "header": {
    "resultCode": 200,
    "resultMessage": "SUCCESS",
    "isSuccessful": true
  },
  "template": {
    "createdAt": "2024-10-24T05:24:14.053Z",
    "updatedAt": "2024-10-24T05:24:14.053Z",
    "id": "bf095f99-1547-48f4-b57d-1124e853f6e2",
    "name": "nginx-template",
    "namespace": "abd7f92e-3353-4b45-944c-510a97ef89c9",
    "containers": [
      {
        "id": "51be9a5d-4737-4323-ae57-0ade225f4f4d",
        "name": "nginx",
        "image": "nginx:latest",
        "imageRegistry": "other",
        "imageRegistryType": "public",
        "cpus": 0.25,
        "memoryLimit": {
          "hard": 256
        },
        "ports": [
          {
            "hostPort": 80,
            "containerPort": 80,
            "protocol": "TCP"
          }
        ],
        "restartCount": 0,
        "probe": [
          {
            "type": "liveness",
            "exec": [
              "bash",
              "-c",
              "curl -f http://localhost || exit 1"
            ],
            "initialDelaySeconds": 5,
            "failureThreshold": 3,
            "timeoutSeconds": 1,
            "periodSeconds": 10
          },
          {
            "type": "startup",
            "exec": [
              "bash",
              "-c",
              "curl -f http://localhost || exit 1"
            ],
            "initialDelaySeconds": 5,
            "failureThreshold": 3,
            "timeoutSeconds": 1,
            "periodSeconds": 10
          }
        ],
        "type": "normal"
      }
    ],
    "networks": [
      {
        "vpcId": "aeeafe4e-287d-4dd4-b91a-294b87688457",
        "subnetId": "abd7f92e-3353-4b45-944c-510a97ef89c9"
      }
    ],
    "dnsConfig": [
      "223.255.201.241",
      "211.50.32.6"
    ],
    "version": "first"
  }
}
```

</details>

### テンプレートバージョン作成

テンプレートバージョンを作成します。

```bash
POST /ncs/v1.0/appkeys/{appKey}/templates/{templateId}/versions
x-nhn-authorization: {token}
```

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| templateId | URL | String | O | テンプレートID |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|
| template | Body | Object | O | テンプレートバージョン情報 |
| template.version | Body | String | O | テンプレートバージョン |
| template.sourceVersion | Body | String | O | テンプレート基準バージョン |
| template.name | Body | String | O | テンプレート名 |
| template.versionDescription | Body | String | X | テンプレートバージョンの説明 |
| template.dnsConfig | Body | String List | X | コンテナに設定されたDNS Server情報 |
| template.hostAliases | Body | List | X | コンテナ`/etc/hosts`に設定された情報 |
| template.hostAliases.ip | Body | String | O | コンテナに設定されたhostnamesのIP |
| template.hostAliases.hostnames | Body | String List | O | コンテナに設定されたIPのhostnames |
| template.applyImmediately | Body | Boolean | X | true:即時配布使用、false:即時配布使用しない(default:false) |
| template.containers | Body | Array | O | テンプレートのコンテナリスト |
| template.containers.name | Body | String | O | コンテナ名 |
| template.containers.type | Body | String | O | コンテナタイプ<br>\* normal:一般<br>\* init:初期化 |
| template.containers.image | Body | String | O | コンテナイメージ |
| template.containers.imageRegistryCredentials | Body | Object | X | Privateレジストリにアクセス可能な情報 |
| template.containers.imageRegistryCredentials.changed | Body | Boolean | X | 既存アカウントの使用有無(default:false)<br>\* false:既存アカウントを使用<br>\* true:新規アカウントを使用(username、passwordの送信が必要) |
| template.containers.imageRegistryCredentials.username | Body | String | O | PrivateレジストリID |
| template.containers.imageRegistryCredentials.password | Body | String | O | Privateレジストリパスワード |
| template.containers.cpus | Body | Float | O | コンテナに割り当てるCPUの数 |
| template.containers.memoryLimit | Body | Object | O | コンテナに割り当てるメモリ情報 |
| template.containers.memoryLimit.hard | Body | Integer | O | コンテナに割り当てるメモリ(MiB) |
| template.containers.gpuFlavor | Body | String | X | GPU Flavor情報<br>\* ncs1.g1m5<br>\* ncs1.g2m10 |
| template.containers.ports | Body | Array | X | コンテナで使用するポート情報 |
| template.containers.ports.containerPort | Body | Integer | O | コンテナポート |
| template.containers.ports.protocol | Body | String | O | コンテナプロトコル<br>\* TCP<br>\* UDP<br>\* HTTP<br>\* HTTPS<br>\* TERMINATED\_HTTPS |
| template.containers.command | Body | String List | X | コンテナが起動する時に実行されるコマンド |
| template.containers.args | Body | String List | X | コンテナが起動する時に使用される引数 |
| template.containers.workDirectory | Body | String | X | コンテナの作業ディレクトリ |
| template.containers.env | Body | Array | X | コンテナ環境変数 |
| template.containers.env.name | Body | String | O | コンテナ環境変数名 |
| template.containers.env.value | Body | String | O | コンテナ環境変数の値 |
| template.containers.postStart | Body | String List | X | コンテナ作成直後に実行されるコマンド |
| template.containers.preStop | Body | String List | X | コンテナが終了する直前に実行されるコマンド |
| template.containers.configs | Body | List | X | コンテナで使用するConfigMap情報 |
| template.containers.configs.id | Body | Integer | O | ConfigMap ID |
| template.containers.configs.type | Body | String | O | ConfigMap情報を取得するservice type<br>obs: Object Storage |
| template.containers.configs.value | Body | String | O | オブジェクトURL |
| template.containers.configs.mountPath | Body | String | O | コンテナマウントパス |
| template.containers.configs.changedAppKey | Body | Boolean | X | 既存AppKeyの使用有無(default:false)<br>\* false:既存keyを使用<br>\* true:新規keyを使用(AppKeyの送信が必要) |
| template.containers.configs.appKey | Body | String | X | Object Storage AppKey |
| template.containers.configs.changedUserAccessKeyId | Body | Boolean | X | 既存UserAccessKeyIdの使用有無(default:false)<br>\* false:既存keyを使用<br>\* true:新規keyを使用(UserAccessKeyIdの送信が必要) |
| template.containers.configs.userAccessKeyId | Body | String | X | Object StorageサービスにアクセスするユーザーのUser Access Key |
| template.containers.configs.changedSecretAccessKey | Body | Boolean | X | 既存SecretAccessKeyの使用有無(default:false)<br>\* false:既存keyを使用<br>\* true:新規keyを使用(SecretAccessKeyの送信が必要) |
| template.containers.configs.secretAccessKey | Body | String | X | Object StorageサービスにアクセスするユーザーのSecret Access Key |
| template.containers.secrets | Body | List | X | コンテナで使用するSecret情報 |
| template.containers.secrets.type | Body | String | O | Secret情報を取得するservice type<br>skm: Secure Key Manager |
| template.containers.secrets.value | Body | String | O | キーID |
| template.containers.secrets.mountPath | Body | String | O | コンテナマウントパス |
| template.containers.volumes | Body | Array | X | コンテナで使用するNASストレージ情報 |
| template.containers.volumes.name | Body | String | O | ストレージ名 |
| template.containers.volumes.path | Body | String | O | NASストレージ接続パス |
| template.containers.volumes.mountPath | body | String | X | コンテナの接続パス |
| template.containers.probe | Body | List | X | コンテナProbeの設定 |
| template.containers.probe.type | Body | String | O | コンテナProbeタイプ<br>\* startup<br>\* liveness |
| template.containers.probe.failureThreshold | Body | Integer | O | Probeの失敗基準 |
| template.containers.probe.initialDelaySeconds | Body | Integer | O | Probeの開始待機時間 |
| template.containers.probe.periodSeconds | Body | Integer | O | Probeの実行間隔 |
| template.containers.probe.timeoutSeconds | Body | Integer | O | Probeの実行制限時間 |
| template.containers.probe.exec | Body | String List | O | Probeの実行コマンド |
| template.containers.stopTimeout | Body | Integer | X | 初期化コンテナ実行制限時間(秒) |

<details>
  <summary>例</summary>

```json
{
  "template": {
    "applyImmediately": false,
    "containers": [
      {
        "cpus": 1,
        "image": "nginx:latest",
        "imageRegistryType": "public",
        "memoryLimit": {
          "hard": 1024
        },
        "name": "nginx",
        "ports": [
          {
            "containerPort": 80,
            "protocol": "terminated-https"
          }
        ],
        "type": "normal"
      }
    ],
    "description": "new version",
    "name": "nginx-template",
    "sourceVersion": "first",
    "version": "v3"
  }
}
```

</details>

#### レスポンス

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| template | Body | Object | O | テンプレート情報 |
| template.id | Body | UUID | O | テンプレートID |
| template.version | Body | String | O | テンプレートバージョン |
| template.sourceVersion | Body | String | O | テンプレート基準バージョン |
| template.name | Body | String | O | テンプレート名 |
| template.createdAt | Body | String | O | 作成時間(UTC) |
| template.description | Body | String | X | テンプレートの説明 |
| template.versionDescription | Body | String | X | テンプレートバージョンの説明 |
| template.networks | Body | Array | O | テンプレートのネットワーク情報 |
| template.networks.vpcId | Body | String | O | テンプレートのVPC ID |
| template.networks.subnetId | Body | String | O | テンプレートのSubnet ID |
| template.dnsConfig | Body | String List | X | コンテナに設定されたDNS Server情報 |
| template.hostAliases | Body | List | X | コンテナ`/etc/hosts`に設定された情報 |
| template.hostAliases.ip | Body | String | O | コンテナに設定されたhostnamesのIP |
| template.hostAliases.hostnames | Body | String List | O | コンテナに設定されたIPのhostnames |
| template.containers | Body | Array | O | テンプレートのコンテナリスト |
| template.containers.name | Body | String | O | コンテナ名 |
| template.containers.type | Body | String | O | コンテナタイプ<br>\* normal:一般<br>\* init:初期化 |
| template.containers.image | Body | String | O | コンテナイメージ |
| template.containers.cpus | Body | Float | O | コンテナに割り当てるCPUの数 |
| template.containers.memoryLimit | Body | Object | O | コンテナに割り当てるメモリ情報 |
| template.containers.memoryLimit.hard | Body | Integer | O | コンテナに割り当てるメモリ(MiB) |
| template.containers.gpuFlavor | Body | String | X | GPU Flavor情報<br>\* ncs1.g1m5<br>\* ncs1.g2m10 |
| template.containers.ports | Body | Array | X | コンテナで使用するポート情報 |
| template.containers.ports.containerPort | Body | Integer | O | コンテナポート |
| template.containers.ports.protocol | Body | String | O | コンテナプロトコル<br>\* TCP<br>\* UDP<br>\* HTTP<br>\* HTTPS<br>\* TERMINATED\_HTTPS |
| template.containers.command | Body | String List | X | コンテナが起動する時に実行されるコマンド |
| template.containers.args | Body | String List | X | コンテナが起動する時に使用される引数 |
| template.containers.workDirectory | Body | String | X | コンテナの作業ディレクトリ |
| template.containers.env | Body | Array | X | コンテナ環境変数 |
| template.containers.env.name | Body | String | O | コンテナ環境変数名 |
| template.containers.env.value | Body | String | O | コンテナ環境変数の値 |
| template.containers.postStart | Body | String List | X | コンテナ作成直後に実行されるコマンド |
| template.containers.preStop | Body | String List | X | コンテナが終了する直前に実行されるコマンド |
| template.containers.configs | Body | List | X | コンテナで使用するConfigMap情報 |
| template.containers.configs.id | Body | Integer | O | ConfigMap ID |
| template.containers.configs.type | Body | String | O | ConfigMap情報を取得するservice type<br>obs: Object Storage |
| template.containers.configs.value | Body | String | O | オブジェクトURL |
| template.containers.configs.mountPath | Body | String | O | コンテナマウントパス |
| template.containers.secrets | Body | List | X | コンテナで使用するSecret情報 |
| template.containers.secrets.type | Body | String | O | Secret情報を取得するservice type<br>skm: Secure Key Manager |
| template.containers.secrets.value | Body | String | O | キーID |
| template.containers.secrets.mountPath | Body | String | O | コンテナマウントパス |
| template.containers.volumes | Body | Array | X | コンテナで使用するNASストレージ情報 |
| template.containers.volumes.name | Body | String | O | ストレージ名 |
| template.containers.volumes.path | Body | String | O | NASストレージ接続パス |
| template.containers.volumes.mountPath | body | String | X | コンテナの接続パス |
| template.containers.probe | Body | List | X | コンテナのProbe設定 |
| template.containers.probe.type | Body | String | O | コンテナProbeタイプ<br>\* startup<br>\* liveness |
| template.containers.probe.failureThreshold | Body | Integer | O | Probeの失敗基準 |
| template.containers.probe.initialDelaySeconds | Body | Integer | O | Probeの開始待機時間 |
| template.containers.probe.periodSeconds | Body | Integer | O | Probeの実行間隔 |
| template.containers.probe.timeoutSeconds | Body | Integer | O | Probeの実行制限時間 |
| template.containers.probe.exec | Body | String List | O | Probeの実行コマンド |
| template.containers.stopTimeout | Body | Integer | X | 初期化コンテナ実行制限時間(秒) |

<details>
  <summary>例</summary>

```json
{
  "header": {
    "resultCode": 200,
    "resultMessage": "SUCCESS",
    "isSuccessful": true
  },
  "template": {
    "createdAt": "2024-10-27T22:26:49.196Z",
    "updatedAt": "2024-10-27T22:26:49.196Z",
    "id": "bf095f99-1547-48f4-b57d-1124e853f6e2",
    "name": "nginx-template",
    "namespace": "abd7f92e-3353-4b45-944c-510a97ef89c9",
    "description": "new version",
    "containers": [
      {
        "id": "bf6bcdd7-6ef1-4f82-981a-c4fc4ebae16e",
        "name": "nginx",
        "image": "nginx:latest",
        "imageRegistry": "other",
        "imageRegistryType": "public",
        "cpus": 1,
        "memoryLimit": {
          "hard": 1024
        },
        "ports": [
          {
            "containerPort": 80,
            "protocol": "TERMINATED-HTTPS"
          }
        ],
        "restartCount": 0,
        "type": "normal"
      }
    ],
    "networks": [
      {
        "vpcId": "aeeafe4e-287d-4dd4-b91a-294b87688457",
        "subnetId": "abd7f92e-3353-4b45-944c-510a97ef89c9"
      }
    ],
    "dnsConfig": [
      "223.255.201.241",
      "211.50.32.6"
    ],
    "version": "v3",
    "sourceVersion": "first"
  }
}
```

</details>

### テンプレートバージョンの削除

```bash
DELETE /ncs/v1.0/appkeys/{appkey}/templates/{templateId}/versions/{version}
x-nhn-authorization: {token}
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| templateId | URL | String | O | テンプレートID |
| version | URL | String | O | テンプレートバージョン |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|

#### レスポンス

このAPIは共通情報のみレスポンスします。

## ワークロード

### ワークロードリスト表示

ワークロードリストを照会します。

```bash
GET /ncs/v1.0/appkeys/{appKey}/workloads
x-nhn-authorization: {token}
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|
| q | Query | String | X | ワークロード名とテンプレートID、テンプレートバージョンでフィルタリング<br>例：q=templateId=${テンプレートID}<br>       q=${ワークロード名)<br>       q=templateId=${テンプレートID}\&version=${テンプレートバージョン} |
| page | Query | Integer | X | 照会するページ番号 |
| size | Query | Integer | X | 照会するページサイズ(default: 10) |

#### レスポンス

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| X-Total-Count | Header | Integer | O | Appkeyに作成されているワークロード全体数 |
| workloads | Body | Array | O | ワークロードリスト |
| workloads.id | Body | UUID | O | ワークロードID |
| workloads.name | Body | String | O | ワークロード名 |
| workloads.type | Body | String | O | 配布コントローラー<br>\* deployment<br>\* statefulset |
| workloads.templateId | Body | String | O | ワークロードのテンプレートID |
| workloads.templateVersion | Body | String | O | ワークロードのテンプレートバージョン |
| workloads.createdAt | Body | String | O | 作成時間(UTC) |
| workloads.desired | Body | Integer | O | ワークロード作業リクエスト数 |
| workloads.available | Body | Integer | O | ワークロード作業実行数 |
| workloads.status | Body | Integer | O | ワークロード状態 |
| workloads.url | Body | String | X | ワークロードロードバランサーURL |
| workloads.loadBalancing | Body | Object | O | ワークロードロードバランサー情報 |
| workloads.loadBalancing.enabled | Body | Boolean | O | ワークロードロードバランサー使用有無 |
| workloads.loadBalancing.floatingIp | Body | Boolean | O | ワークロードロードバランサーFloating IP使用有無 |
| workloads.loadBalancing.healthMonitor | Body | Object | X | ロードバランサーのヘルスチェック情報 |
| workloads.loadBalancing.healthMonitor.delay | Body | Integer | O | ヘルスチェック周期 |
| workloads.loadBalancing.healthMonitor.timeout | Body | Integer | O | 最大レスポンス待機時間 |
| workloads.loadBalancing.healthMonitor.maxRetries | Body | Integer | O | 最大再試行回数 |
| workloads.loadBalancing.healthMonitor.httpMethod | Body | String | X | HTTPメソッド<br>\* GET |
| workloads.loadBalancing.healthMonitor.expectedCodes | Body | String | X | HTTPステータスコード<br>\* 200<br>\* 200,202<br>\* 200-204 |
| workloads.loadBalancing.healthMonitor.urlPath | Body | String | X | HTTP URL |
| workloads.loadBalancing.certificate | Body | String | X | TERMINATED\_HTTPS使用時にロードバランサーで使用する証明書 |
| workloads.loadBalancing.privateKey | Body | String | X | TERMINATED\_HTTPS使用時にロードバランサーで使用する秘密鍵 |
| workloads.loadBalancing.tlsVersion | Body | String | X | TERMINATED\_HTTPS使用時のTLSバージョン<ul><li>SSLv3</li><li>TLSv1.0</li><li>TLSv1.0\_2016</li><li>TLSv1.1</li><li>TLSv1.2</li><li>TLSv1.3</li></ul> |
| workloads.loadBalancing.containerHref | Body | String | X | シークレットコンテナID<br>Load Balancer APIを利用し、TERMINATED\_HTTPSで使用する証明書、秘密鍵を別途で登録した場合に使用 |
| workloads.loadBalancing.ipAclGroupsBinding | Body | List | X | ロードバランサーに適用するIPアクセス制御グループリスト |
| workloads.loadBalancing.ipAclGroupsBinding.ipAclGroupId | Body | String | O | IPアクセス制御グループID |
| workloads.schedule | Body | Object | X | 予約実行設定情報 |
| workloads.schedule.timeZone | Body | String | O | 予約実行基準時間<br>\* 例：Asia/Seoul, UTC<br>\* [https://en.wikipedia.org/wiki/List_of_tz_database_time_zones](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones) |
| workloads.schedule.cron | Body | String | O | 予約実行Cron式 |
| workloads.schedule.jobsHistoryLimit | Body | Integer | O | 予約実行ヒストリーの保管数 |
| workloads.schedule.concurrencyPolicy | Body | String | O | 同時実行ポリシー<br>\* Forbid, Replace |
| workloads.schedule.timeOffset | Body | String | O | 予約実行基準時間Offset |
| workloads.internalLoadBalancing | Body | Object | X | 内部ロードバランサー情報 |
| workloads.internalLoadBalancing.enalbed | Body | Boolean | O | 内部ロードバランサーの使用有無 |
| workloads.internalLoadBalancing.type | Body | String | X | 内部ロードバランサーのIP割り当て方法<br>\* dynamic:自動割り当て<br>\* static:IPを指定 |
| workloads.internalLoadBalancing.ip | Body | String | X | 内部ロードバランサーの指定IP |
| workloads.privateDns | Body | Object | X | Private DNSにワークロード作業IPを登録するかどうかを決定 |
| workloads.privateDns.ttl | Body | Integer | O | レコードセットのTTL値 |
| workloads.privateDns.zoneId | Body | String | O | ワークロードで使用するPrivate DNS Zone ID |
| workloads.privateDns.domain | Body | String | O | Private DNSに登録されたドメイン情報 |
| workloads.activeDeadline | Body | Object | X | ワークロードの予約終了情報 |
| workloads.activeDeadline.timeZone | Body | String | O | 予約終了基準時間<br>\* 例：Asia/Seoul、UTC |
| workloads.activeDeadline.timeOffset | Body | String | O | 予約終了基準時間Offset |
| workloads.activeDeadline.time | Body | String | O | 予約終了時間 |
| workloads.autoScaler | Body | Object | X | AutoScalerの設定情報 |
| workloads.autoScaler.scaleOut | Body | Object | O | ScaleOut情報 |
| workloads.autoScaler.scaleOut.enabled | Body | Boolean | O | ScaleOutの使用有無 |
| workloads.autoScaler.scaleOut.maxReplicas | Body | Integer | X | オートスケーリングの最大作業数 |
| workloads.autoScaler.scaleOut.coolDownMinute | Body | Integer | X | 増設後の待機時間 |
| workloads.autoScaler.scaleOut.condition | Body | List | X | 増設条件 |
| workloads.autoScaler.scaleOut.condition.resource | Body | String | X | 増設条件基準リソース<br>* cpu<br>* memory<br>* gpu<br>* gpu-memory |
| workloads.autoScaler.scaleOut.condition.threshold | Body | Integer | X | 増設条件リソース使用量(1～100) |
| workloads.autoScaler.scaleOut.condition.duration | Body | Integer | X | 増設条件リソース使用量の維持時間(分) |
| workloads.autoScaler.scaleIn | Body | Object | X | ScaleIn情報 |
| workloads.autoScaler.scaleIn.enabled | Body | Boolean | O | ScaleInの使用有無 |
| workloads.autoScaler.scaleIn.minReplicas | Body | Integer | X | オートスケーリングの最小作業数 |
| workloads.autoScaler.scaleIn.coolDownMinute | Body | Integer | X | 縮小後の待機時間 |
| workloads.autoScaler.scaleIn.condition | Body | List | X | 縮小条件 |
| workloads.autoScaler.scaleIn.condition.resource | Body | String | X | 縮小条件基準リソース<br>* cpu<br>* memory<br>* gpu<br>* gpu-memory |
| workloads.autoScaler.scaleIn.condition.threshold | Body | Integer | X | 縮小条件リソース使用量(1～100) |
| workloads.autoScaler.scaleIn.condition.duration | Body | Integer | X | 縮小条件リソース使用量の維持時間(分) |
| workloads.securityGroups | Body | List | X | SecurityGroups情報 |
| workloads.securityGroups.id | Body | String | O | SecurityGroups ID |

<details>
  <summary>例</summary>

```json
{
  "header": {
    "resultCode": 200,
    "resultMessage": "SUCCESS",
    "isSuccessful": true
  },
  "workloads": [
    {
      "createdAt": "2024-10-27T22:29:05.859Z",
      "updatedAt": "2024-10-27T22:29:05.859Z",
      "id": "08ef9e6e-5a57-49f3-8f52-7ce36b337a8a",
      "name": "nginx",
      "templateId": "bf095f99-1547-48f4-b57d-1124e853f6e2",
      "templateName": "nginx-template",
      "templateVersion": "first",
      "desired": 1,
      "available": 1,
      "status": "Running",
      "networks": [
        {
          "vpcId": "aeeafe4e-287d-4dd4-b91a-294b87688457",
          "subnetId": "abd7f92e-3353-4b45-944c-510a97ef89c9"
        }
      ],
      "loadBalancing": {
        "enabled": true,
        "floatingIp": false,
        "ipAddress": "192.168.0.102",
        "healthMonitor": {
          "delay": 30,
          "timeout": 5,
          "maxRetries": 2,
          "httpMethod": "GET",
          "expectedCodes": "200",
          "urlPath": "/"
        }
      },
      "type": "deployment",
      "internalLoadBalancing": {
        "enabled": false
      }
    }
  ]
}
```

</details>

### ワークロード表示

個別ワークロードを照会します。

```bash
GET /ncs/v1.0/appkeys/{appKey}/workloads/{workloadId}
x-nhn-authorization: {token}
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| workloadId | URL | String | O | ワークロードID |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|

#### レスポンス

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| workload | Body | Array | O | ワークロード情報 |
| workload.id | Body | UUID | O | ワークロードID |
| workload.name | Body | String | O | ワークロード名 |
| workload.type | Body | String | O | 配布コントローラー<br>\* deployment<br>\* statefulset |
| workload.templateId | Body | String | O | ワークロードのテンプレートID |
| workload.templateVersion | Body | String | O | ワークロードのテンプレートバージョン |
| workload.createdAt | Body | String | O | 作成時間(UTC) |
| workload.desired | Body | Integer | O | ワークロード作業リクエスト数 |
| workload.available | Body | Integer | O | ワークロード作業実行数 |
| workload.status | Body | Integer | O | ワークロード状態 |
| workload.url | Body | String | X | ワークロードロードバランサーURL |
| workload.loadBalancing | Body | Object | O | ワークロードロードバランサー情報 |
| workload.loadBalancing.enabled | Body | Boolean | O | ワークロードロードバランサーの使用有無 |
| workload.loadBalancing.floatingIp | Body | Boolean | O | ワークロードロードバランサーFloating IPの使用有無 |
| workload.loadBalancing.healthMonitor | Body | Object | X | ロードバランサーのヘルスチェック情報 |
| workload.loadBalancing.healthMonitor.delay | Body | Integer | O | ヘルスチェック周期 |
| workload.loadBalancing.healthMonitor.timeout | Body | Integer | O | 最大レスポンス待機時間 |
| workload.loadBalancing.healthMonitor.maxRetries | Body | Integer | O | 最大再試行回数 |
| workload.loadBalancing.healthMonitor.httpMethod | Body | String | X | HTTPメソッド<br>\* GET |
| workload.loadBalancing.healthMonitor.expectedCodes | Body | String | X | HTTPステータスコード<br>\* 200<br>\* 200,202<br>\* 200-204 |
| workload.loadBalancing.healthMonitor.urlPath | Body | String | X | HTTP URL |
| workload.loadBalancing.certificate | Body | String | X | TERMINATED\_HTTPS使用時にロードバランサーで使用する証明書 |
| workload.loadBalancing.privateKey | Body | String | X | TERMINATED\_HTTPS使用時にロードバランサーで使用する秘密鍵 |
| workload.loadBalancing.tlsVersion | Body | String | X | TERMINATED\_HTTPS使用時のTLSバージョン<ul><li>SSLv3</li><li>TLSv1.0</li><li>TLSv1.0\_2016</li><li>TLSv1.1</li><li>TLSv1.2</li><li>TLSv1.3</li></ul> |
| workload.loadBalancing.containerHref | Body | String | X | シークレットコンテナID<br>Load Balancer APIを利用し、TERMINATED\_HTTPSで使用する証明書、秘密鍵を別途で登録した場合に使用 |
| workload.loadBalancing.ipAclGroupsBinding | Body | List | X | ロードバランサーに適用するIPアクセス制御グループリスト |
| workload.loadBalancing.ipAclGroupsBinding.ipAclGroupId | Body | String | O | IPアクセス制御グループID |
| workload.schedule | Body | Object | X | 予約実行設定情報 |
| workload.schedule.timeZone | Body | String | O | 予約実行基準時間<br>\* 例：Asia/Seoul、UTC<br>\* [https://en.wikipedia.org/wiki/List_of_tz_database_time_zones](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones) |
| workload.schedule.cron | Body | String | O | 予約実行Cron式 |
| workload.schedule.jobsHistoryLimit | Body | Integer | O | 予約実行ヒストリーの保管数 |
| workload.schedule.concurrencyPolicy | Body | String | O | 同時実行ポリシー<br>\* Forbid, Replace |
| workload.schedule.timeOffset | Body | String | O | 予約実行基準時間Offset |
| workload.internalLoadBalancing | Body | Object | X | 内部ロードバランサー情報 |
| workload.internalLoadBalancing.enalbed | Body | Boolean | O | 内部ロードバランサーの使用有無 |
| workload.internalLoadBalancing.type | Body | String | X | 内部ロードバランサーのIP割り当て方法<br>\* dynamic:自動割り当て<br>\* static:IP指定 |
| workload.internalLoadBalancing.ip | Body | String | X | 内部ロードバランサーの指定IP |
| workload.privateDns | Body | Object | X | Private DNSにワークロード作業IPを登録するかどうかを決定 |
| workload.privateDns.ttl | Body | Integer | O | レコードセットのTTL値 |
| workload.privateDns.zoneId | Body | String | O | ワークロードで使用するPrivate DNS Zone ID |
| workload.privateDns.domain | Body | String | O | Private DNSに登録されたドメイン情報 |
| workload.activeDeadline | Body | Object | X | ワークロード予約終了情報 |
| workload.activeDeadline.timeZone | Body | String | O | 予約終了基準時間<br>\* 例：Asia/Seoul、UTC |
| workload.activeDeadline.timeOffset | Body | String | O | 予約終了基準時間Offset |
| workload.activeDeadline.time | Body | String | O | 予約終了時間 |
| workload.autoScaler | Body | Object | X | AutoScaler設定情報 |
| workload.autoScaler.scaleOut | Body | Object | O | ScaleOut情報 |
| workload.autoScaler.scaleOut.enabled | Body | Boolean | O | ScaleOutの使用有無 |
| workload.autoScaler.scaleOut.maxReplicas | Body | Integer | X | オートスケーリングの最大作業数 |
| workload.autoScaler.scaleOut.coolDownMinute | Body | Integer | X | 増設後の待機時間 |
| workload.autoScaler.scaleOut.condition | Body | List | X | 増設条件 |
| workload.autoScaler.scaleOut.condition.resource | Body | String | X | 増設条件基準リソース<br>* cpu<br>* memory<br>* gpu<br>* gpu-memory |
| workload.autoScaler.scaleOut.condition.threshold | Body | Integer | X | 増設条件リソース使用量(1～100) |
| workload.autoScaler.scaleOut.condition.duration | Body | Integer | X | 増設条件リソース使用量の維持時間(分) |
| workload.autoScaler.scaleIn | Body | Object | X | ScaleIn情報 |
| workload.autoScaler.scaleIn.enabled | Body | Boolean | O | ScaleInの使用有無 |
| workload.autoScaler.scaleIn.minReplicas | Body | Integer | X | オートスケーリングの最小作業数 |
| workload.autoScaler.scaleIn.coolDownMinute | Body | Integer | X | 縮小後の待機時間 |
| workload.autoScaler.scaleIn.condition | Body | List | X | 縮小条件 |
| workload.autoScaler.scaleIn.condition.resource | Body | String | X | 縮小条件基準リソース<br>* cpu<br>* memory<br>* gpu<br>* gpu-memory |
| workload.autoScaler.scaleIn.condition.threshold | Body | Integer | X | 縮小条件リソース使用量(1～100) |
| workload.autoScaler.scaleIn.condition.duration | Body | Integer | X | 縮小条件リソース使用量の維持時間(分) |
| workload.securityGroups | Body | List | X | SecurityGroups情報 |
| workload.securityGroups.id | Body | String | O | SecurityGroups ID |
| workload.tasks | Body | Array | O | ワークロードの作業リスト |
| workload.tasks.id | Body | UUID | O | 作業ID |
| workload.tasks.containers | Body | Array | O | 作業のコンテナリスト |
| workload.tasks.containers.name | Body | String | O | コンテナ名 |
| workload.tasks.containers.type | Body | String | O | コンテナタイプ<br>\* normal:一般<br>\* init:初期化 |
| workload.tasks.containers.image | Body | String | O | コンテナイメージ |
| workload.tasks.containers.ip | Body | String | X | コンテナIP |
| workload.tasks.containers.cpus | Body | Float | O | コンテナに割り当てるCPUの数 |
| workload.tasks.containers.memoryLimit | Body | Object | O | コンテナに割り当てるメモリ情報 |
| workload.tasks.containers.memoryLimit.hard | Body | Integer | O | コンテナに割り当てられたメモリ(MiB) |
| workload.tasks.containers.gpuFlavor | Body | String | X | コンテナに割り当てられたGPU Flavor情報 |
| workload.tasks.containers.ports | Body | Array | X | コンテナのポート情報 |
| workload.tasks.containers.ports.containerPort | Body | Integer | O | コンテナポート |
| workload.tasks.containers.ports.protocol | Body | String | O | コンテナプロトコル |
| workload.tasks.containers.command | Body | String List | X | コンテナが起動する時に実行されるコマンド |
| workload.tasks.containers.args | Body | String List | X | コンテナが起動する時に 使用される引数 |
| workload.tasks.containers.workDirectory | Body | String | X | コンテナの作業ディレクトリ |
| workload.tasks.containers.env | Body | Array | X | コンテナ環境変数 |
| workload.tasks.containers.env.name | Body | String | O | コンテナ環境変数名 |
| workload.tasks.containers.env.value | Body | String | O | コンテナ環境変数の値 |
| workload.tasks.containers.postStart | Body | String List | X | コンテナ作成直後に実行されるコマンド |
| workload.tasks.containers.preStop | Body | String List | X | コンテナが終了する直前に実行されるコマンド |
| workload.tasks.containers.configs | Body | List | X | コンテナで使用するConfigMap情報 |
| workload.tasks.containers.configs.id | Body | Integer | O | ConfigMap ID |
| workload.tasks.containers.configs.type | Body | String | O | ConfigMap情報を取得するservice type<br>obs: Object Storage |
| workload.tasks.containers.configs.value | Body | String | O | オブジェクトURL |
| workload.tasks.containers.configs.mountPath | Body | String | O | コンテナマウントパス |
| workload.tasks.containers.secrets | Body | List | X | コンテナで使用するSecret情報 |
| workload.tasks.containers.secrets.type | Body | String | O | Secret情報を取得するservice type<br>skm: Secure Key Manager |
| workload.tasks.containers.secrets.value | Body | String | O | キーID |
| workload.tasks.containers.secrets.mountPath | Body | String | O | コンテナマウントパス |
| workload.tasks.containers.volumes | Body | Array | X | コンテナで使用するストレージ情報 |
| workload.tasks.containers.volumes.name | Body | String | O | ストレージ名 |
| workload.tasks.containers.volumes.path | Body | String | O | NASストレージ接続パス |
| workload.tasks.containers.volumes.mountPath | body | String | X | コンテナの接続パス(default: /mnt) |
| workload.tasks.containers.probe | Body | List | X | コンテナProbeの設定 |
| workload.tasks.containers.probe.type | Body | String | O | コンテナProbeタイプ<br>\* startup<br>\* liveness |
| workload.tasks.containers.probe.failureThreshold | Body | Integer | O | Probeの失敗基準 |
| workload.tasks.containers.probe.initialDelaySeconds | Body | Integer | O | Probeの開始待機時間 |
| workload.tasks.containers.probe.periodSeconds | Body | String | O | Probeの実行間隔 |
| workload.tasks.containers.probe.timeoutSeconds | Body | String | O | Probeの実行制限時間 |
| workload.tasks.containers.probe.exec | Body | String List | O | Probeの実行コマンド |
| workload.tasks.containers.stopTimeout | Body | Integer | X | 初期化コンテナ実行制限時間(秒) |
| workload.tasks.containers.state | Body | String | O | コンテナ状態 |
| workload.tasks.containers.startedAt | Body | String | O | コンテナ開始時間 |
| workload.tasks.containers.finishedAt | Body | String | X | 初期化コンテナ完了時間 |
| workload.tasks.containers.restartCount | Body | String | O | コンテナ再起動回数 |

<details>
  <summary>例</summary>

```json
{
  "header": {
    "resultCode": 200,
    "resultMessage": "SUCCESS",
    "isSuccessful": true
  },
  "workload": {
    "createdAt": "2024-10-27T22:29:05.859Z",
    "updatedAt": "2024-10-27T22:29:05.859Z",
    "id": "08ef9e6e-5a57-49f3-8f52-7ce36b337a8a",
    "name": "nginx",
    "namespace": "abd7f92e-3353-4b45-944c-510a97ef89c9",
    "templateId": "bf095f99-1547-48f4-b57d-1124e853f6e2",
    "templateName": "nginx-template",
    "templateVersion": "first",
    "desired": 1,
    "available": 1,
    "status": "Running",
    "tasks": [
      {
        "id": "e75d7945-62f4-4cd2-9a93-14e14edccb2b",
        "containers": [
          {
            "id": "51be9a5d-4737-4323-ae57-0ade225f4f4d",
            "name": "nginx",
            "ip": "192.168.0.76",
            "image": "nginx:latest",
            "imageRegistry": "other",
            "imageRegistryType": "public",
            "cpus": 0.25,
            "memoryLimit": {
              "hard": 256
            },
            "ports": [
              {
                "hostPort": 80,
                "containerPort": 80,
                "protocol": "TCP"
              }
            ],
            "state": "Running",
            "startedAt": "2024-10-27T22:29:23Z",
            "restartCount": 0,
            "probe": [
              {
                "type": "liveness",
                "exec": [
                  "bash",
                  "-c",
                  "curl -f http://localhost || exit 1"
                ],
                "initialDelaySeconds": 5,
                "failureThreshold": 3,
                "timeoutSeconds": 1,
                "periodSeconds": 10
              },
              {
                "type": "startup",
                "exec": [
                  "bash",
                  "-c",
                  "curl -f http://localhost || exit 1"
                ],
                "initialDelaySeconds": 5,
                "failureThreshold": 3,
                "timeoutSeconds": 1,
                "periodSeconds": 10
              }
            ],
            "type": "normal"
          }
        ]
      }
    ],
    "networks": [
      {
        "vpcId": "aeeafe4e-287d-4dd4-b91a-294b87688457",
        "subnetId": "abd7f92e-3353-4b45-944c-510a97ef89c9"
      }
    ],
    "securityGroups": [
      {
        "id": "b1b4a7b3-d35f-4676-857b-4f8997f9f3b8"
      }
    ],
    "loadBalancing": {
      "enabled": true,
      "floatingIp": false,
      "ipAddress": "192.168.0.102",
      "healthMonitor": {
        "delay": 30,
        "timeout": 5,
        "maxRetries": 2,
        "httpMethod": "GET",
        "expectedCodes": "200",
        "urlPath": "/"
      }
    },
    "type": "deployment",
    "internalLoadBalancing": {
      "enabled": false
    }
  }
}
```

</details>

### ワークロードログ表示

ワークロードのコンテナログを照会します。

```bash
GET /ncs/v1.0/appkeys/{appKey}/workloads/{workloadId}/tasks/{taskId}/logs?container={ContainerName}&from={YYYY-MM-DDThh:mm:ssZ}&to={YYYY-MM-DDThh:mm:ssZ}
x-nhn-authorization: {token}
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| workloadId | URL | String | O | ワークロードID |
| taskId | URL | String | O | 作業ID |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|
| containerName | Query | String | O | コンテナ名 |
| from | Query | String | X | ログ開始時間<br>default:現在から5分前 |
| to | Query | String | X | ログ終了時間<br>default:現在時間 |
| page | Query | String | X | 照会するページ |
| size | Query | String | X | 照会するページサイズ(default:100) |

#### レスポンス

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| logs | Body | Array | O | コンテナログリスト |
| log | Body | String | O | ログ内容 |
| time | Body | String | O | ログ発生時間(UTC) |

<details>
  <summary>例</summary>

```json
{
  "header": {
    "resultCode": 200,
    "resultMessage": "SUCCESS",
    "isSuccessful": true
  },
  "logs": [
    {
      "log": "/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration",
      "time": "2024-10-27T22:29:23.546854524Z"
    },
    {
      "log": "/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/",
      "time": "2024-10-27T22:29:23.548332152Z"
    }
  ]
}
```

</details>

### ワークロードイベント表示

ワークロードのイベントを照会します。

```bash
GET /ncs/v1.0/appkeys/{appKey}/workloads/{workloadId}/tasks/{taskId}/events
x-nhn-authorization: {token}
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| workloadId | URL | String | O | ワークロードID |
| taskId | URL | String | O | 作業ID |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|
| type | Query | Integer | X | イベントタイプ<br>Normal<br>Warning |
| q | Query | String | X | イベント内容フィルタリング |
| page | Query | String | X | 照会するページ |
| size | Query | String | X | 照会するページサイズ(default: 10) |
| from | Query | String | X | イベントの最後の発生日時開始時間<br>default:現在から1時間前 |
| to | Query | String | X | イベントの最後の発生日時終了時間<br>default:現在時間 |

#### レスポンス

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| events | Body | Array | O | イベントリスト |
| type | Body | String | O | イベントタイプ<br>Normal<br>Warning |
| reason | Body | String | O | イベント発生原因 |
| message | Body | String | O | イベント内容 |
| createTimestamp | Body | String | O | イベントの最初の発生日時(UTC) |
| lastTimestamp | Body | String | O | イベントの最後の発生日時(UTC) |
| count | Body | Integer | O | イベント発生回数 |

<details>
  <summary>例</summary>

```json
{
  "header": {
    "resultCode": 200,
    "resultMessage": "SUCCESS",
    "isSuccessful": true
  },
  "events": [
    {
      "type": "Normal",
      "reason": "Scheduled",
      "message": "Successfully assigned abd7f92e-3353-4b45-944c-510a97ef89c9/nginx-5b64d9d5d4-rkcr2 to ncsac1-kxzed5wj-0424fc5a",
      "createTimestamp": "2024-10-27T22:29:06Z",
      "lastTimestamp": "2024-10-27T22:29:06Z",
      "count": 1
    }
  ]
}
```

</details>

### ワークロード実行ヒストリーリスト表示

ワークロード実行ヒストリーリストを照会します。

```bash
GET /ncs/v1.0/appkeys/{appKey}/workloads/{workloadId}/history
x-nhn-authorization: {token}
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| workloadId | URL | String | O | ワークロードID |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|
| page | Query | Integer | X | 照会するページ番号 |
| size | Query | Integer | X | 照会するページサイズ(default: 10) |
| sort | Query | String | X | ソート基準となるフィールド名<br>降順ソートの場合はフィールド名の前に`-`を付ける<br>例) `sort=-id` |

#### レスポンス

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| history | Body | Array | O | ヒストリーリスト |
| history.id | Body | Integer | O | ヒストリーID |
| history.createdAt | Body | String | O | 配布時間 |
| history.deletedAt | Body | String | O | 終了時間 |
| history.templateVersion | Body | UUID | O | ワークロードが使用したテンプレートバージョン |
| history.name | Body | String | O | テンプレート名 |
| history.status | Body | String | O | 状態<br>Succeeded<br>Terminated<br>Pending |

<details>
  <summary>例</summary>

```json
{
  "header": {
    "resultCode": 200,
    "resultMessage": "SUCCESS",
    "isSuccessful": true
  },
  "id": "08ef9e6e-5a57-49f3-8f52-7ce36b337a8a",
  "history": [
    {
      "id": 1,
      "createdAt": "2024-10-27T22:29:05.996Z",
      "deletedAt": null,
      "templateId": "bf095f99-1547-48f4-b57d-1124e853f6e2",
      "templateVersion": "first",
      "name": "nginx-template",
      "status": "Succeeded"
    }
  ]
}
```

</details>

### ワークロード実行ヒストリー表示

個別ワークロード実行ヒストリーを照会します。

```bash
GET /ncs/v1.0/appkeys/{appKey}/workloads/{workloadId}/history/{historyId}
x-nhn-authorization: {token}
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| workloadId | URL | String | O | ワークロードID |
| historyId | URL | Integer | O | ヒストリーID |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|

#### レスポンス

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| history | Body | Object | O | ヒストリー情報 |
| history.id | Body | Integer | O | ヒストリーID |
| history.createdAt | Body | String | O | 実行時間 |
| history.deletedAt | Body | String | O | 終了時間 |
| history.templateId | Body | UUID | O | ワークロードが使用したテンプレートID |
| history.name | Body | String | O | ワークロードが使用したテンプレート名 |
| history.status | Body | String | O | 状態<br>Succeeded<br>Terminated<br>Pending |
| template | Body | Object | O | テンプレート情報 |
| template.id | Body | UUID | O | テンプレートID |
| template.version | Body | String | O | テンプレートバージョン |
| template.name | Body | String | O | テンプレート名 |
| template.createdAt | Body | String | O | 作成時間(UTC) |
| template.description | Body | String | X | テンプレートの説明 |
| template.versionDescription | Body | String | X | テンプレートバージョンの説明 |
| template.networks | Body | Array | O | テンプレートのネットワーク情報 |
| template.networks.vpcId | Body | String | O | テンプレートのVPC ID |
| template.networks.subnetId | Body | String | O | テンプレートのSubnet ID |
| template.dnsConfig | Body | String List | X | コンテナに設定されたDNS Server情報 |
| template.hostAliases | Body | List | X | コンテナ`/etc/hosts`に設定された情報 |
| template.hostAliases.ip | Body | String | O | コンテナに設定されたhostnamesのIP |
| template.hostAliases.hostnames | Body | String List | O | コンテナに設定されたIPのhostnames |
| template.containers | Body | Array | O | テンプレートのコンテナリスト |
| template.containers.name | Body | String | O | コンテナ名 |
| template.containers.type | Body | String | O | コンテナタイプ<br>\* normal:一般<br>\* init:初期化 |
| template.containers.image | Body | String | O | コンテナイメージ |
| template.containers.cpus | Body | Float | O | コンテナに割り当てるCPUの数 |
| template.containers.memoryLimit | Body | Object | O | コンテナに割り当てるメモリ情報 |
| template.containers.memoryLimit.hard | Body | Integer | O | コンテナに割り当てるメモリ(MiB) |
| template.containers.gpuFlavor | Body | String | X | GPU Flavor情報<br>\* ncs1.g1m5<br>\* ncs1.g2m10 |
| template.containers.ports | Body | Array | X | コンテナで使用するポート情報 |
| template.containers.ports.containerPort | Body | Integer | O | コンテナポート |
| template.containers.ports.protocol | Body | String | O | コンテナプロトコル<br>\* TCP<br>\* UDP<br>\* HTTP<br>\* HTTPS<br>\* TERMINATED\_HTTPS |
| template.containers.command | Body | String List | X | コンテナが起動する時に 実行されるコマンド |
| template.containers.args | Body | String List | X | コンテナが起動する時に使用される引数 |
| template.containers.workDirectory | Body | String | X | コンテナの作業ディレクトリ |
| template.containers.env | Body | Array | X | コンテナ環境変数 |
| template.containers.env.name | Body | String | O | コンテナ環境変数名 |
| template.containers.env.value | Body | String | O | コンテナ環境変数の値 |
| template.containers.postStart | Body | String List | X | コンテナ作成直後に実行されるコマンド |
| template.containers.preStop | Body | String List | X | コンテナが終了する直前に実行されるコマンド |
| template.containers.configs | Body | List | X | コンテナで使用するConfigMap情報 |
| template.containers.configs.id | Body | Integer | O | ConfigMap ID |
| template.containers.configs.type | Body | String | O | ConfigMap情報を取得するservice type<br>obs: Object Storage |
| template.containers.configs.value | Body | String | O | オブジェクトURL |
| template.containers.configs.mountPath | Body | String | O | コンテナマウントパス |
| template.containers.secrets | Body | List | X | コンテナで使用するSecret情報 |
| template.containers.secrets.type | Body | String | O | Secret情報を取得するservice type<br>skm: Secure Key Manager |
| template.containers.secrets.value | Body | String | O | キーID |
| template.containers.secrets.mountPath | Body | String | O | コンテナマウントパス |
| template.containers.volumes | Body | Array | X | コンテナで使用するNASストレージ情報 |
| template.containers.volumes.name | Body | String | O | ストレージ名 |
| template.containers.volumes.path | Body | String | O | NASストレージ接続パス |
| template.containers.volumes.mountPath | body | String | X | コンテナの接続パス |
| template.containers.probe | Body | List | X | コンテナProbeの設定 |
| template.containers.probe.type | Body | String | O | コンテナProbeタイプ<br>\* startup<br>\* liveness |
| template.containers.probe.failureThreshold | Body | Integer | O | Probeの失敗基準 |
| template.containers.probe.initialDelaySeconds | Body | Integer | O | Probeの開始待機時間 |
| template.containers.probe.periodSeconds | Body | Integer | O | Probeの実行間隔 |
| template.containers.probe.timeoutSeconds | Body | Integer | O | Probeの実行制限時間 |
| template.containers.probe.exec | Body | String List | O | Probeの実行コマンド |
| template.containers.stopTimeout | Body | Integer | X | 初期化コンテナ実行制限時間(秒) |

<details>
  <summary>例</summary>

```json
{
  "header": {
    "resultCode": 200,
    "resultMessage": "SUCCESS",
    "isSuccessful": true
  },
  "history": {
    "id": 1,
    "createdAt": "2024-10-27T22:29:05.996Z",
    "deletedAt": null,
    "templateId": "bf095f99-1547-48f4-b57d-1124e853f6e2",
    "templateVersion": "first",
    "name": "nginx-template",
    "status": "Succeeded"
  },
  "template": {
    "createdAt": "2024-10-24T05:24:14.053Z",
    "updatedAt": "2024-10-24T05:24:14.053Z",
    "id": "bf095f99-1547-48f4-b57d-1124e853f6e2",
    "name": "nginx-template",
    "namespace": "abd7f92e-3353-4b45-944c-510a97ef89c9",
    "containers": [
      {
        "id": "51be9a5d-4737-4323-ae57-0ade225f4f4d",
        "name": "nginx",
        "image": "nginx:latest",
        "imageRegistry": "other",
        "imageRegistryType": "public",
        "cpus": 0.25,
        "memoryLimit": {
          "hard": 256
        },
        "ports": [
          {
            "hostPort": 80,
            "containerPort": 80,
            "protocol": "TCP"
          }
        ],
        "restartCount": 0,
        "probe": [
          {
            "type": "liveness",
            "exec": [
              "bash",
              "-c",
              "curl -f http://localhost || exit 1"
            ],
            "initialDelaySeconds": 5,
            "failureThreshold": 3,
            "timeoutSeconds": 1,
            "periodSeconds": 10
          },
          {
            "type": "startup",
            "exec": [
              "bash",
              "-c",
              "curl -f http://localhost || exit 1"
            ],
            "initialDelaySeconds": 5,
            "failureThreshold": 3,
            "timeoutSeconds": 1,
            "periodSeconds": 10
          }
        ],
        "type": "normal"
      }
    ],
    "networks": [
      {
        "vpcId": "aeeafe4e-287d-4dd4-b91a-294b87688457",
        "subnetId": "abd7f92e-3353-4b45-944c-510a97ef89c9"
      }
    ],
    "dnsConfig": [
      "223.255.201.241",
      "211.50.32.6"
    ],
    "version": "first"
  }
}
```

</details>

### ワークロード予約実行ヒストリー表示

予約実行ヒストリーを照会します。

```bash
GET /ncs/v1.0/appkeys/{appKey}/workloads/{workloadId}/schedulehistory
x-nhn-authorization: {token}
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| workloadId | URL | String | O | ワークロードID |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|
| page | Query | Integer | X | 照会するページ番号 |
| size | Query | Integer | X | 照会するページサイズ(default:10) |

#### レスポンス

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| schedulehistory | Body | List | O | 予約実行ヒストリーリスト |
| schedulehistory.id | Body | String | O | 作業ID |
| schedulehistory.createdAt | Body | String | O | 開始時間 |
| schedulehistory.finishedAt | Body | Object | O | 終了時間 |
| schedulehistory.status | Body | String | O | 予約タスク状態<br>\* Error<br>\* Completed <br>\* Waiting <br>\* Running |

<details>
  <summary>例</summary>

```json
{
  "header": {
    "resultCode": 200,
    "resultMessage": "SUCCESS",
    "isSuccessful": true
  },
  "schedulehistory": [
    {
      "id": "6a8241f7-13ba-468b-ae03-dc1e7a35ccb8",
      "createdAt": "2024-10-27T22:48:00Z",
      "finishedAt": "2024-10-27T22:48:45Z",
      "status": "Completed"
    }
  ]
}

```

</details>

### ワークロードを作成する

ワークロードを作成します。

```bash
POST /ncs/v1.0/appkeys/{appKey}/workloads
Content-Type: application/json
x-nhn-authorization: {token}
```

#### リクエスト

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|
| workload | Body | Object | O | ワークロード情報 |
| workload.name | Body | String | O | ワークロード名 |
| workload.type | Body | String | X | 配布コントローラー(default:deployment)<br>\* deployment<br>\* statefulset |
| workload.templateId | Body | String | O | ワークロードのテンプレートID |
| workload.templateVersion | Body | String | X | ワークロードのテンプレートバージョン(default:最新バージョン) |
| workload.desired | Body | Integer | O | ワークロード作業リクエスト数 |
| workload.loadBalancing | Body | Object | O | ワークロードロードバランサー情報 |
| workload.loadBalancing.enabled | Body | Boolean | O | ワークロードロードバランサーの使用有無 |
| workload.loadBalancing.floatingIp | Body | Boolean | O | ワークロードロードバランサーFloating IP使用有無 |
| workload.loadBalancing.healthMonitor | Body | Object | X | ロードバランサーのヘルスチェック情報 |
| workload.loadBalancing.healthMonitor.delay | Body | Integer | O | ヘルスチェック周期 |
| workload.loadBalancing.healthMonitor.timeout | Body | Integer | O | 最大レスポンス待機時間 |
| workload.loadBalancing.healthMonitor.maxRetries | Body | Integer | O | 最大再試行回数 |
| workload.loadBalancing.healthMonitor.httpMethod | Body | String | X | HTTPメソッド<br>\* GET |
| workload.loadBalancing.healthMonitor.expectedCodes | Body | String | X | HTTPステータスコード<br>\* 200<br>\* 200,202<br>\* 200-204 |
| workload.loadBalancing.healthMonitor.urlPath | Body | String | X | HTTP URL |
| workload.loadBalancing.certificate | Body | String | X | TERMINATED\_HTTPS使用時にロードバランサーで使用する証明書 |
| workload.loadBalancing.privateKey | Body | String | X | TERMINATED\_HTTPS使用時にロードバランサーで使用する秘密鍵 |
| workload.loadBalancing.tlsVersion | Body | String | X | TERMINATED\_HTTPS使用時のTLSバージョン<ul><li>SSLv3</li><li>TLSv1.0</li><li>TLSv1.0\_2016</li><li>TLSv1.1</li><li>TLSv1.2</li><li>TLSv1.3</li></ul> |
| workload.loadBalancing.containerHref | Body | String | X | シークレットコンテナID<br>Load Balancer APIを利用し、TERMINATED\_HTTPSで使用する証明書、秘密鍵を別途で登録した場合に使用 |
| workload.loadBalancing.ipAclGroupsBinding | Body | List | X | ロードバランサーに適用するIPアクセス制御グループリスト |
| workload.loadBalancing.ipAclGroupsBinding.ipAclGroupId | Body | String | O | IPアクセス制御グループID |
| workload.schedule | Body | Object | X | 予約実行設定情報 |
| workload.schedule.timeZone | Body | String | O | 予約実行基準時間<br>\* 例：Asia/Seoul、UTC<br>\* [https://en.wikipedia.org/wiki/List_of_tz_database_time_zones](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones) |
| workload.schedule.cron | Body | String | O | 予約実行Cron式 |
| workload.schedule.jobsHistoryLimit | Body | Integer | O | 予約実行ヒストリーの保管数 |
| workload.schedule.concurrencyPolicy | Body | String | O | 同時実行ポリシー<br>\* Forbid, Replace |
| workload.internalLoadBalancing | Body | Object | X | 内部ロードバランサー情報 |
| workload.internalLoadBalancing.enalbed | Body | Boolean | O | 内部ロードバランサーの使用有無 |
| workload.internalLoadBalancing.type | Body | String | X | 内部ロードバランサーのIP割り当て方法<br>\* dynamic:自動割り当て<br>\* static:IP指定 |
| workload.internalLoadBalancing.ip | Body | String | X | 内部ロードバランサーの指定IP |
| workload.privateDns | Body | Object | X | Private DNSにワークロード作業IPを登録するかどうかを決定 |
| workload.privateDns.ttl | Body | Integer | O | レコードセットのTTL値 |
| workload.privateDns.zoneId | Body | String | O | ワークロードで使用するPrivate DNS Zone ID |
| workload.privateDns.domain | Body | String | O | Private DNSに登録されたドメイン情報 |
| workload.activeDeadline | Body | Object | X | ワークロード予約終了情報 |
| workload.activeDeadline.timeZone | Body | String | O | 予約終了基準時間<br>\* 例：Asia/Seoul、UTC |
| workload.activeDeadline.timeOffset | Body | String | O | 予約終了基準時間Offset |
| workload.activeDeadline.time | Body | String | O | 予約終了時間 |
| workload.autoScaler | Body | Object | X | AutoScaler設定情報 |
| workload.autoScaler.scaleOut | Body | Object | O | ScaleOut情報 |
| workload.autoScaler.scaleOut.enabled | Body | Boolean | O | ScaleOutの使用有無 |
| workload.autoScaler.scaleOut.maxReplicas | Body | Integer | X | オートスケーリングの最大作業数 |
| workload.autoScaler.scaleOut.coolDownMinute | Body | Integer | X | 増設後の待機時間 |
| workload.autoScaler.scaleOut.condition | Body | List | X | 増設条件 |
| workload.autoScaler.scaleOut.condition.resource | Body | String | X | 増設条件基準リソース<br>* cpu<br>* memory<br>* gpu<br>* gpu-memory |
| workload.autoScaler.scaleOut.condition.threshold | Body | Integer | X | 増設条件リソース使用量(1～100) |
| workload.autoScaler.scaleOut.condition.duration | Body | Integer | X | 増設条件リソース使用量の維持時間(分) |
| workload.autoScaler.scaleIn | Body | Object | X | ScaleIn情報 |
| workload.autoScaler.scaleIn.enabled | Body | Boolean | O | ScaleInの使用有無 |
| workload.autoScaler.scaleIn.minReplicas | Body | Integer | X | オートスケーリングの最小作業数 |
| workload.autoScaler.scaleIn.coolDownMinute | Body | Integer | X | 縮小後の待機時間 |
| workload.autoScaler.scaleIn.condition | Body | List | X | 縮小条件 |
| workload.autoScaler.scaleIn.condition.resource | Body | String | X | 縮小条件基準リソース<br>* cpu<br>* memory<br>* gpu<br>* gpu-memory |
| workload.autoScaler.scaleIn.condition.threshold | Body | Integer | X | 縮小条件リソース使用量(1～100) |
| workload.autoScaler.scaleIn.condition.duration | Body | Integer | X | 縮小条件リソース使用量の維持時間(分) |
| workload.securityGroups | Body | List | X | SecurityGroups情報 |
| workload.securityGroups.id | Body | String | O | SecurityGroups ID |

<details>
  <summary>例</summary>

```json
{
  "workload": {
    "description": "api workload",
    "desired": 1,
    "loadBalancing": {
      "enabled": false,
      "floatingIp": false
    },
    "name": "ncs-workload",
    "templateId": "bf095f99-1547-48f4-b57d-1124e853f6e2",
    "templateVersion": "first",
    "type": "deployment"
  }
}
```

</details>

#### レスポンス

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| workload | Body | Object | O | ワークロード情報 |
| workload.id | Body | UUID | O | ワークロードID |
| workload.name | Body | String | O | ワークロード名 |
| workload.type | Body | String | X | 配布コントローラー<br>\* deployment<br>\* statefulset |
| workload.templateId | Body | String | O | ワークロードのテンプレートID |
| workload.templateVersion | Body | String | O | ワークロードのテンプレートバージョン |
| workload.createdAt | Body | String | O | 作成時間(UTC) |
| workload.desired | Body | Integer | O | ワークロード作業リクエスト数 |
| workload.loadBalancing | Body | Object | O | ワークロードロードバランサー情報 |
| workload.loadBalancing.enabled | Body | Boolean | O | ワークロードロードバランサーの使用有無 |
| workload.loadBalancing.floatingIp | Body | Boolean | O | ワークロードロードバランサーFloating IPの使用有無 |
| workload.loadBalancing.healthMonitor | Body | Object | X | ロードバランサーのヘルスチェック情報 |
| workload.loadBalancing.healthMonitor.delay | Body | Integer | O | ヘルスチェック周期 |
| workload.loadBalancing.healthMonitor.timeout | Body | Integer | O | 最大レスポンス待機時間 |
| workload.loadBalancing.healthMonitor.maxRetries | Body | Integer | O | 最大再試行回数 |
| workload.loadBalancing.healthMonitor.httpMethod | Body | String | X | HTTPメソッド<br>\* GET |
| workload.loadBalancing.healthMonitor.expectedCodes | Body | String | X | HTTPステータスコード<br>\* 200<br>\* 200,202<br>\* 200-204 |
| workload.loadBalancing.healthMonitor.urlPath | Body | String | X | HTTP URL |
| workload.loadBalancing.certificate | Body | String | X | TERMINATED\_HTTPS使用時にロードバランサーで使用する証明書 |
| workload.loadBalancing.privateKey | Body | String | X | TERMINATED\_HTTPS使用時にロードバランサーで使用する秘密鍵 |
| workload.loadBalancing.tlsVersion | Body | String | X | TERMINATED\_HTTPS使用時のTLSバージョン<ul><li>SSLv3</li><li>TLSv1.0</li><li>TLSv1.0\_2016</li><li>TLSv1.1</li><li>TLSv1.2</li><li>TLSv1.3</li></ul> |
| workload.loadBalancing.containerHref | Body | String | X | シークレットコンテナID<br>Load Balancer APIを利用し、TERMINATED\_HTTPSで使用する証明書、秘密鍵を別途で登録した場合に使用 |
| workload.loadBalancing.ipAclGroupsBinding | Body | List | X | ロードバランサーに適用するIPアクセス制御グループリスト |
| workload.loadBalancing.ipAclGroupsBinding.ipAclGroupId | Body | String | O | IPアクセス制御グループID |
| workload.schedule | Body | Object | X | 予約実行設定情報 |
| workload.schedule.timeZone | Body | String | O | 予約実行基準時間<br>\* 例：Asia/Seoul、UTC<br>\* [https://en.wikipedia.org/wiki/List_of_tz_database_time_zones](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones) |
| workload.schedule.cron | Body | String | O | 予約実行Cron式 |
| workload.schedule.jobsHistoryLimit | Body | Integer | O | 予約実行ヒストリーの保管数 |
| workload.schedule.concurrencyPolicy | Body | String | O | 同時実行ポリシー<br>\* Forbid, Replace |
| workload.schedule.timeOffset | Body | String | O | 予約実行基準時間Offset |
| workload.internalLoadBalancing | Body | Object | X | 内部ロードバランサー情報 |
| workload.internalLoadBalancing.enalbed | Body | Boolean | O | 内部ロードバランサーの使用有無 |
| workload.internalLoadBalancing.type | Body | String | X | 内部ロードバランサーのIP割り当て方法<br>\* dynamic:自動割り当て<br>\* static:IP指定 |
| workload.internalLoadBalancing.ip | Body | String | X | 内部ロードバランサーの指定IP |
| workload.privateDns | Body | Object | X | Private DNSにワークロード作業IPを登録するかどうかを決定 |
| workload.privateDns.ttl | Body | Integer | O | レコードセットのTTL値 |
| workload.privateDns.zoneId | Body | String | O | ワークロードで使用するPrivate DNS Zone ID |
| workload.privateDns.domain | Body | String | O | Private DNSに登録されたドメイン情報 |
| workload.activeDeadline | Body | Object | X | ワークロード予約終了情報 |
| workload.activeDeadline.timeZone | Body | String | O | 予約終了基準時間<br>\* 例：Asia/Seoul、UTC |
| workload.activeDeadline.timeOffset | Body | String | O | 予約終了基準時間Offset |
| workload.activeDeadline.time | Body | String | O | 予約終了時間 |
| workload.autoScaler | Body | Object | X | AutoScaler設定情報 |
| workload.autoScaler.scaleOut | Body | Object | O | ScaleOut情報 |
| workload.autoScaler.scaleOut.enabled | Body | Boolean | O | ScaleOutの使用有無 |
| workload.autoScaler.scaleOut.maxReplicas | Body | Integer | X | オートスケーリングの最大作業数 |
| workload.autoScaler.scaleOut.coolDownMinute | Body | Integer | X | 増設後の待機時間 |
| workload.autoScaler.scaleOut.condition | Body | List | X | 増設条件 |
| workload.autoScaler.scaleOut.condition.resource | Body | String | X | 増設条件基準リソース<br>* cpu<br>* memory<br>* gpu<br>* gpu-memory |
| workload.autoScaler.scaleOut.condition.threshold | Body | Integer | X | 増設条件リソース使用量(1～100) |
| workload.autoScaler.scaleOut.condition.duration | Body | Integer | X | 増設条件リソース使用量の維持時間(分) |
| workload.autoScaler.scaleIn | Body | Object | X | ScaleIn情報 |
| workload.autoScaler.scaleIn.enabled | Body | Boolean | O | ScaleInの使用有無 |
| workload.autoScaler.scaleIn.minReplicas | Body | Integer | X | オートスケーリングの最小作業数 |
| workload.autoScaler.scaleIn.coolDownMinute | Body | Integer | X | 縮小後の待機時間 |
| workload.autoScaler.scaleIn.condition | Body | List | X | 縮小条件 |
| workload.autoScaler.scaleIn.condition.resource | Body | String | X | 縮小条件基準リソース<br>* cpu<br>* memory<br>* gpu<br>* gpu-memory |
| workload.autoScaler.scaleIn.condition.threshold | Body | Integer | X | 縮小条件リソース使用量(1～100) |
| workload.autoScaler.scaleIn.condition.duration | Body | Integer | X | 縮小条件リソース使用量の維持時間(分) |
| workload.securityGroups | Body | List | X | SecurityGroups情報 |
| workload.securityGroups.id | Body | String | O | SecurityGroups ID |

<details>
  <summary>例</summary>

```json
{
  "header": {
    "resultCode": 200,
    "resultMessage": "SUCCESS",
    "isSuccessful": true
  },
  "workload": {
    "createdAt": "2024-10-27T22:51:32.305Z",
    "updatedAt": "2024-10-27T22:51:32.305Z",
    "id": "6c6201b5-5b68-4035-adf7-40cd5f9402e5",
    "name": "ncs-workload",
    "namespace": "abd7f92e-3353-4b45-944c-510a97ef89c9",
    "description": "api workload",
    "templateId": "bf095f99-1547-48f4-b57d-1124e853f6e2",
    "templateName": "nginx-template",
    "templateVersion": "first",
    "desired": 1,
    "available": 0,
    "status": "",
    "loadBalancing": {
      "enabled": false,
      "floatingIp": false
    },
    "type": "deployment"
  }
}
```

</details>

### ワークロードを変更する

ワークロードを変更します。

```bash
PUT /ncs/v1.0/appkeys/{appKey}/workloads/{workloadId}
Content-Type: application/json
x-nhn-authorization: {token}
```

#### リクエスト

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| workloadId | URL | String | O | ワークロードID |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|
| workload | Body | Object | O | ワークロード情報 |
| workload.name | Body | String | O | ワークロード名 |
| workload.templateId | Body | String | O | ワークロードのテンプレートID |
| workload.templateVersion | Body | String | O | ワークロードのテンプレートバージョン |
| workload.desired | Body | Integer | O | ワークロード作業リクエスト数 |
| workload.loadBalancing | Body | Object | O | ワークロードロードバランサー情報 |
| workload.loadBalancing.enabled | Body | Boolean | O | ワークロードロードバランサーの使用有無 |
| workload.loadBalancing.floatingIp | Body | Boolean | O | ワークロードロードバランサーFloating IP使用有無 |
| workload.loadBalancing.healthMonitor | Body | Object | X | ロードバランサーのヘルスチェック情報 |
| workload.loadBalancing.healthMonitor.delay | Body | Integer | O | ヘルスチェック周期 |
| workload.loadBalancing.healthMonitor.timeout | Body | Integer | O | 最大レスポンス待機時間 |
| workload.loadBalancing.healthMonitor.maxRetries | Body | Integer | O | 最大再試行回数 |
| workload.loadBalancing.healthMonitor.httpMethod | Body | String | X | HTTPメソッド<br>\* GET |
| workload.loadBalancing.healthMonitor.expectedCodes | Body | String | X | HTTPステータスコード<br>\* 200<br>\* 200,202<br>\* 200-204 |
| workload.loadBalancing.healthMonitor.urlPath | Body | String | X | HTTP URL |
| workload.loadBalancing.certificate | Body | String | X | TERMINATED\_HTTPS使用時にロードバランサーで使用する証明書 |
| workload.loadBalancing.privateKey | Body | String | X | TERMINATED\_HTTPS使用時にロードバランサーで使用する秘密鍵 |
| workload.loadBalancing.tlsVersion | Body | String | X | TERMINATED\_HTTPS使用時のTLSバージョン<ul><li>SSLv3</li><li>TLSv1.0</li><li>TLSv1.0\_2016</li><li>TLSv1.1</li><li>TLSv1.2</li><li>TLSv1.3</li></ul> |
| workload.loadBalancing.containerHref | Body | String | X | シークレットコンテナID<br>Load Balancer APIを利用し、TERMINATED\_HTTPSで使用する証明書、秘密鍵を別途で登録した場合に使用 |
| workload.loadBalancing.ipAclGroupsBinding | Body | List | X | ロードバランサーに適用するIPアクセス制御グループリスト |
| workload.loadBalancing.ipAclGroupsBinding.ipAclGroupId | Body | String | O | IPアクセス制御グループID |
| workload.schedule | Body | Object | X | 予約実行設定情報 |
| workload.schedule.timeZone | Body | String | O | 予約実行基準時間<br>\* 例：Asia/Seoul、UTC<br>\* [https://en.wikipedia.org/wiki/List_of_tz_database_time_zones](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones) |
| workload.schedule.cron | Body | String | O | 予約実行Cron式 |
| workload.schedule.jobsHistoryLimit | Body | Integer | O | 予約実行ヒストリーの保管数 |
| workload.schedule.concurrencyPolicy | Body | String | O | 同時実行ポリシー<br>\* Forbid, Replace |
| workload.internalLoadBalancing | Body | Object | X | 内部ロードバランサー情報 |
| workload.internalLoadBalancing.enalbed | Body | Boolean | O | 内部ロードバランサーの使用有無 |
| workload.internalLoadBalancing.type | Body | String | X | 内部ロードバランサーのIP割り当て方法<br>\* dynamic；自動 割り当て<br>\* static:IP指定 |
| workload.internalLoadBalancing.ip | Body | String | X | 内部ロードバランサーの指定IP |
| workload.activeDeadline | Body | Object | X | ワークロード予約終了情報 |
| workload.activeDeadline.timeZone | Body | String | O | 予約終了基準時間<br>\* 例：Asia/Seoul、UTC |
| workload.activeDeadline.timeOffset | Body | String | O | 予約終了基準時間Offset |
| workload.activeDeadline.time | Body | String | O | 予約終了時間 |
| workload.autoScaler | Body | Object | X | AutoScaler設定情報 |
| workload.autoScaler.scaleOut | Body | Object | O | ScaleOut情報 |
| workload.autoScaler.scaleOut.enabled | Body | Boolean | O | ScaleOutの使用有無 |
| workload.autoScaler.scaleOut.maxReplicas | Body | Integer | X | オートスケーリングの最大作業数 |
| workload.autoScaler.scaleOut.coolDownMinute | Body | Integer | X | 増設後の待機時間 |
| workload.autoScaler.scaleOut.condition | Body | List | X | 増設条件 |
| workload.autoScaler.scaleOut.condition.resource | Body | String | X | 増設条件基準リソース<br>* cpu<br>* memory<br>* gpu<br>* gpu-memory |
| workload.autoScaler.scaleOut.condition.threshold | Body | Integer | X | 増設条件リソース使用量(1～100) |
| workload.autoScaler.scaleOut.condition.duration | Body | Integer | X | 増設条件リソース使用量の維持時間(分) |
| workload.autoScaler.scaleIn | Body | Object | X | ScaleIn情報 |
| workload.autoScaler.scaleIn.enabled | Body | Boolean | O | ScaleInの使用有無 |
| workload.autoScaler.scaleIn.minReplicas | Body | Integer | X | オートスケーリングの最小作業数 |
| workload.autoScaler.scaleIn.coolDownMinute | Body | Integer | X | 縮小後の待機時間 |
| workload.autoScaler.scaleIn.condition | Body | List | X | 縮小条件 |
| workload.autoScaler.scaleIn.condition.resource | Body | String | X | 縮小条件基準リソース<br>* cpu<br>* memory<br>* gpu<br>* gpu-memory |
| workload.autoScaler.scaleIn.condition.threshold | Body | Integer | X | 縮小条件リソース使用量(1～100) |
| workload.autoScaler.scaleIn.condition.duration | Body | Integer | X | 縮小条件リソース使用量の維持時間(分) |
| workload.securityGroups | Body | List | X | SecurityGroups情報 |
| workload.securityGroups.id | Body | String | O | SecurityGroups ID |

<details>
  <summary>例</summary>

```json
{
  "workload": {
    "description": "api workload",
    "desired": 2,
    "loadBalancing": {
      "enabled": true,
      "floatingIp": false
    },
    "name": "ncs-workload",
    "templateId": "bf095f99-1547-48f4-b57d-1124e853f6e2",
    "templateVersion": "first",
    "type": "deployment"
  }
}
```

</details>

#### レスポンス

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| workloads | Body | Object | O | ワークロード情報 |
| workload.id | Body | UUID | O | ワークロードID |
| workload.name | Body | String | O | ワークロード名 |
| workload.type | Body | String | O | 配布コントローラー<br>\* deployment<br>\* statefulset |
| workload.templateId | Body | String | O | ワークロードのテンプレートID |
| workload.templateVersion | Body | String | O | ワークロードのテンプレートバージョン |
| workload.createdAt | Body | String | O | 作成時間(UTC) |
| workload.desired | Body | Integer | O | ワークロード作業リクエスト数 |
| workload.loadBalancing | Body | Object | O | ワークロードロードバランサー情報 |
| workload.loadBalancing.enabled | Body | Boolean | O | ワークロードロードバランサーの使用有無 |
| workload.loadBalancing.floatingIp | Body | Boolean | O | ワークロードロードバランサーFloating IPの使用有無 |
| workload.loadBalancing.healthMonitor | Body | Object | X | ロードバランサーのヘルスチェック情報 |
| workload.loadBalancing.healthMonitor.delay | Body | Integer | O | ヘルスチェック周期 |
| workload.loadBalancing.healthMonitor.timeout | Body | Integer | O | 最大レスポンス待機時間 |
| workload.loadBalancing.healthMonitor.maxRetries | Body | Integer | O | 最大再試行回数 |
| workload.loadBalancing.healthMonitor.httpMethod | Body | String | X | HTTPメソッド<br>\* GET |
| workload.loadBalancing.healthMonitor.expectedCodes | Body | String | X | HTTPステータスコード<br>\* 200<br>\* 200,202<br>\* 200-204 |
| workload.loadBalancing.healthMonitor.urlPath | Body | String | X | HTTP URL |
| workload.loadBalancing.certificate | Body | String | X | TERMINATED\_HTTPS使用時にロードバランサーで使用する証明書 |
| workload.loadBalancing.privateKey | Body | String | X | TERMINATED\_HTTPS使用時にロードバランサーで使用する秘密鍵 |
| workload.loadBalancing.tlsVersion | Body | String | X | TERMINATED\_HTTPS使用時のTLSバージョン<ul><li>SSLv3</li><li>TLSv1.0</li><li>TLSv1.0\_2016</li><li>TLSv1.1</li><li>TLSv1.2</li><li>TLSv1.3</li></ul> |
| workload.loadBalancing.containerHref | Body | String | X | シークレットコンテナID<br>Load Balancer APIを利用し、TERMINATED\_HTTPSで使用する証明書、秘密鍵を別途で登録した場合に使用 |
| workload.loadBalancing.ipAclGroupsBinding | Body | List | X | ロードバランサーに適用するIPアクセス制御グループリスト |
| workload.loadBalancing.ipAclGroupsBinding.ipAclGroupId | Body | String | O | IPアクセス制御グループID |
| workload.schedule | Body | Object | X | 予約実行設定情報 |
| workload.schedule.timeZone | Body | String | O | 予約実行基準時間<br>\* 例：Asia/Seoul、UTC<br>\* [https://en.wikipedia.org/wiki/List_of_tz_database_time_zones](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones) |
| workload.schedule.cron | Body | String | O | 予約実行Cron式 |
| workload.schedule.jobsHistoryLimit | Body | Integer | O | 予約実行ヒストリーの保管数 |
| workload.schedule.concurrencyPolicy | Body | String | O | 同時実行ポリシー<br>\* Forbid, Replace |
| workload.schedule.timeOffset | Body | String | O | 予約実行基準時間Offset |
| workload.internalLoadBalancing | Body | Object | X | 内部ロードバランサー情報 |
| workload.internalLoadBalancing.enalbed | Body | Boolean | O | 内部ロードバランサーの使用有無 |
| workload.internalLoadBalancing.type | Body | String | X | 内部ロードバランサーのIP割り当て方法<br>\* dynamic:自動 割り当て<br>\* static:IP指定 |
| workload.internalLoadBalancing.ip | Body | String | X | 内部ロードバランサーの指定IP |
| workload.privateDns | Body | Object | X | Private DNSにワークロード作業IPを登録するかどうかを決定 |
| workload.privateDns.ttl | Body | Integer | O | レコードセットのTTL値 |
| workload.privateDns.zoneId | Body | String | O | ワークロードで使用するPrivate DNS Zone ID |
| workload.privateDns.domain | Body | String | O | Private DNSに登録されたドメイン情報 |
| workload.activeDeadline | Body | Object | X | ワークロード予約終了情報 |
| workload.activeDeadline.timeZone | Body | String | O | 予約終了基準時間<br>\* 例：Asia/Seoul、UTC |
| workload.activeDeadline.timeOffset | Body | String | O | 予約終了基準時間Offset |
| workload.activeDeadline.time | Body | String | O | 予約終了時間 |
| workload.autoScaler | Body | Object | X | AutoScaler設定情報 |
| workload.autoScaler.scaleOut | Body | Object | O | ScaleOut情報 |
| workload.autoScaler.scaleOut.enabled | Body | Boolean | O | ScaleOutの使用有無 |
| workload.autoScaler.scaleOut.maxReplicas | Body | Integer | X | オートスケーリングの最大作業数 |
| workload.autoScaler.scaleOut.coolDownMinute | Body | Integer | X | 増設後の待機時間 |
| workload.autoScaler.scaleOut.condition | Body | List | X | 増設条件 |
| workload.autoScaler.scaleOut.condition.resource | Body | String | X | 増設条件基準リソース<br>* cpu<br>* memory<br>* gpu<br>* gpu-memory |
| workload.autoScaler.scaleOut.condition.threshold | Body | Integer | X | 増設条件リソース使用量(1～100) |
| workload.autoScaler.scaleOut.condition.duration | Body | Integer | X | 増設条件リソース使用量の維持時間(分) |
| workload.autoScaler.scaleIn | Body | Object | X | ScaleIn情報 |
| workload.autoScaler.scaleIn.enabled | Body | Boolean | O | ScaleInの使用有無 |
| workload.autoScaler.scaleIn.minReplicas | Body | Integer | X | オートスケーリングの最小作業数 |
| workload.autoScaler.scaleIn.coolDownMinute | Body | Integer | X | 縮小後の待機時間 |
| workload.autoScaler.scaleIn.condition | Body | List | X | 縮小条件 |
| workload.autoScaler.scaleIn.condition.resource | Body | String | X | 縮小条件基準リソース<br>* cpu<br>* memory<br>* gpu<br>* gpu-memory |
| workload.autoScaler.scaleIn.condition.threshold | Body | Integer | X | 縮小条件リソース使用量(1～100) |
| workload.autoScaler.scaleIn.condition.duration | Body | Integer | X | 縮小条件リソース使用量の維持時間(分) |
| workload.securityGroups | Body | List | X | SecurityGroups情報 |
| workload.securityGroups.id | Body | String | O | SecurityGroups ID |

<details>
  <summary>例</summary>

```json
{
  "header": {
    "resultCode": 200,
    "resultMessage": "SUCCESS",
    "isSuccessful": true
  },
  "workload": {
    "createdAt": "2024-10-27T22:51:32.305Z",
    "updatedAt": "2024-10-27T22:54:30.734Z",
    "id": "6c6201b5-5b68-4035-adf7-40cd5f9402e5",
    "name": "ncs-workload",
    "namespace": "abd7f92e-3353-4b45-944c-510a97ef89c9",
    "description": "api workload",
    "templateId": "bf095f99-1547-48f4-b57d-1124e853f6e2",
    "templateName": "nginx-template",
    "templateVersion": "first",
    "desired": 2,
    "available": 0,
    "status": "",
    "networks": [
      {
        "vpcId": "aeeafe4e-287d-4dd4-b91a-294b87688457",
        "subnetId": "abd7f92e-3353-4b45-944c-510a97ef89c9"
      }
    ],
    "loadBalancing": {
      "enabled": true,
      "floatingIp": false
    },
    "type": "deployment"
  }
}
```

</details>

### ワークロードの部分変更

ワークロードの一部のみを修正できます。

#### リクエスト

* このAPIを使用する場合、Content-Typeはapplication/json-patch+jsonに設定する必要があります。

```bash
PATCH /ncs/v1.0/appkeys/{appKey}/workloads/{workloadId}
Content-Type: application/json-patch+json
x-nhn-authorization: {token}
```

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| workloadId | URL | String | O | ワークロードID |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|
| op | Body | String | O | Operation<br>\* Add<br>\* Remove<br>\* Replace<br>\* Copy<br>\* Move<br>\* Test |
| path | Body | String | O | 変更するデータパス |
| value | Body | String | X | 変更値 |
| from | Body | String | X | 既存データパス |

<details>
   <summary>例</summary>

```json
[
  {
    "op": "replace",
    "path": "/workload/desired",
    "value": 1
  }
]
```

</details>

#### レスポンス

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| workloads | Body | Object | O | ワークロード情報 |
| workload.id | Body | UUID | O | ワークロードID |
| workload.name | Body | String | O | ワークロード名 |
| workload.type | Body | String | O | 配布コントローラー<br>\* deployment<br>\* statefulset |
| workload.templateId | Body | String | O | ワークロードのテンプレートID |
| workload.templateVersion | Body | String | O | ワークロードのテンプレートバージョン |
| workload.createdAt | Body | String | O | 作成時間(UTC) |
| workload.desired | Body | Integer | O | ワークロード作業リクエスト数 |
| workload.loadBalancing | Body | Object | O | ワークロードロードバランサー情報 |
| workload.loadBalancing.enabled | Body | Boolean | O | ワークロードロードバランサーの使用有無 |
| workload.loadBalancing.floatingIp | Body | Boolean | O | ワークロードロードバランサーFloating IPの使用有無 |
| workload.loadBalancing.healthMonitor | Body | Object | X | ロードバランサーのヘルスチェック情報 |
| workload.loadBalancing.healthMonitor.delay | Body | Integer | X | ヘルスチェック周期 |
| workload.loadBalancing.healthMonitor.timeout | Body | Integer | X | 最大レスポンス待機時間 |
| workload.loadBalancing.healthMonitor.maxRetries | Body | Integer | X | 最大再試行回数 |
| workload.loadBalancing.healthMonitor.httpMethod | Body | String | X | HTTPメソッド<br>\* GET |
| workload.loadBalancing.healthMonitor.expectedCodes | Body | String | X | HTTPステータスコード<br>\* 200<br>\* 200,202<br>\* 200-204 |
| workload.loadBalancing.healthMonitor.urlPath | Body | String | X | HTTP URL |
| workload.loadBalancing.certificate | Body | String | X | TERMINATED\_HTTPS使用時にロードバランサーで使用する証明書 |
| workload.loadBalancing.privateKey | Body | String | X | TERMINATED\_HTTPS使用時にロードバランサーで使用する秘密鍵 |
| workload.loadBalancing.tlsVersion | Body | String | X | TERMINATED\_HTTPS使用時のTLSバージョン<ul><li>SSLv3</li><li>TLSv1.0</li><li>TLSv1.0\_2016</li><li>TLSv1.1</li><li>TLSv1.2</li><li>TLSv1.3</li></ul> |
| workload.loadBalancing.containerHref | Body | String | X | シークレットコンテナID<br>Load Balancer APIを利用し、TERMINATED\_HTTPSで使用する証明書、秘密鍵を別途で登録した場合に使用 |
| workload.loadBalancing.ipAclGroupsBinding | Body | List | X | ロードバランサーに適用するIPアクセス制御グループリスト |
| workload.loadBalancing.ipAclGroupsBinding.ipAclGroupId | Body | String | O | IPアクセス制御グループID |
| workload.schedule | Body | Object | X | 予約実行設定情報 |
| workload.schedule.timeZone | Body | String | O | 予約実行基準時間<br>\* 例：Asia/Seoul、UTC<br>\* [https://en.wikipedia.org/wiki/List_of_tz_database_time_zones](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones) |
| workload.schedule.cron | Body | String | O | 予約実行Cron式 |
| workload.schedule.jobsHistoryLimit | Body | Integer | O | 予約実行ヒストリーの保管数 |
| workload.schedule.concurrencyPolicy | Body | String | O | 同時実行ポリシー<br>\* Forbid, Replace |
| workload.schedule.timeOffset | Body | String | O | 予約実行基準時間Offset |
| workload.internalLoadBalancing | Body | Object | X | 内部ロードバランサー情報 |
| workload.internalLoadBalancing.enalbed | Body | Boolean | O | 内部ロードバランサーの使用有無 |
| workload.internalLoadBalancing.type | Body | String | X | 内部ロードバランサーのIP割り当て方法<br>\* dynamic:自動割り当て<br>\* static:IP指定 |
| workload.internalLoadBalancing.ip | Body | String | X | 内部ロードバランサーの指定IP |
| workload.privateDns | Body | Object | X | Private DNSにワークロード作業IPを登録するかどうかを決定 |
| workload.privateDns.ttl | Body | Integer | O | レコードセットのTTL値 |
| workload.privateDns.zoneId | Body | String | O | ワークロードで使用するPrivate DNS Zone ID |
| workload.privateDns.domain | Body | String | O | Private DNSに登録されたドメイン情報 |
| workload.activeDeadline | Body | Object | X | ワークロード予約終了情報 |
| workload.activeDeadline.timeZone | Body | String | O | 予約終了基準時間<br>\* 例：Asia/Seoul、UTC |
| workload.activeDeadline.timeOffset | Body | String | O | 予約終了基準時間Offset |
| workload.activeDeadline.time | Body | String | O | 予約終了時間 |
| workload.autoScaler | Body | Object | X | AutoScaler設定情報 |
| workload.autoScaler.scaleOut | Body | Object | O | ScaleOut情報 |
| workload.autoScaler.scaleOut.enabled | Body | Boolean | O | ScaleOutの使用有無 |
| workload.autoScaler.scaleOut.maxReplicas | Body | Integer | X | オートスケーリングの最大作業数 |
| workload.autoScaler.scaleOut.coolDownMinute | Body | Integer | X | 増設後の待機時間 |
| workload.autoScaler.scaleOut.condition | Body | List | X | 増設条件 |
| workload.autoScaler.scaleOut.condition.resource | Body | String | X | 増設条件基準リソース<br>* cpu<br>* memory<br>* gpu<br>* gpu-memory |
| workload.autoScaler.scaleOut.condition.threshold | Body | Integer | X | 増設条件リソース使用量(1～100) |
| workload.autoScaler.scaleOut.condition.duration | Body | Integer | X | 増設条件リソース使用量の維持時間(分) |
| workload.autoScaler.scaleIn | Body | Object | X | ScaleIn情報 |
| workload.autoScaler.scaleIn.enabled | Body | Boolean | O | ScaleInの使用有無 |
| workload.autoScaler.scaleIn.minReplicas | Body | Integer | X | オートスケーリングの最小作業数 |
| workload.autoScaler.scaleIn.coolDownMinute | Body | Integer | X | 縮小後の待機時間 |
| workload.autoScaler.scaleIn.condition | Body | List | X | 縮小条件 |
| workload.autoScaler.scaleIn.condition.resource | Body | String | X | 縮小条件基準リソース<br>* cpu<br>* memory<br>* gpu<br>* gpu-memory |
| workload.autoScaler.scaleIn.condition.threshold | Body | Integer | X | 縮小条件リソース使用量(1～100) |
| workload.autoScaler.scaleIn.condition.duration | Body | Integer | X | 縮小条件リソース使用量の維持時間(分) |
| workload.securityGroups | Body | List | X | SecurityGroups情報 |
| workload.securityGroups.id | Body | String | O | SecurityGroups ID |

<details>
  <summary>例</summary>

```json
{
  "header": {
    "resultCode": 200,
    "resultMessage": "SUCCESS",
    "isSuccessful": true
  },
  "workload": {
    "createdAt": "2024-10-27T22:51:32.305Z",
    "updatedAt": "2024-10-27T22:57:00.663Z",
    "id": "6c6201b5-5b68-4035-adf7-40cd5f9402e5",
    "name": "ncs-workload",
    "namespace": "abd7f92e-3353-4b45-944c-510a97ef89c9",
    "description": "api workload",
    "templateId": "bf095f99-1547-48f4-b57d-1124e853f6e2",
    "templateName": "nginx-template",
    "templateVersion": "first",
    "desired": 1,
    "available": 0,
    "status": "",
    "networks": [
      {
        "vpcId": "aeeafe4e-287d-4dd4-b91a-294b87688457",
        "subnetId": "abd7f92e-3353-4b45-944c-510a97ef89c9"
      }
    ],
    "loadBalancing": {
      "enabled": true,
      "floatingIp": false
    },
    "type": "deployment"
  }
}
```

</details>

### ワークロード中止
ワークロードを中止します。

```bash
POST /ncs/v1.0/appkeys/{appKey}/workloads/{workloadId}/pause
x-nhn-authorization: {token}
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| workloadId | URL | String | O | テンプレートID |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|

#### レスポンス
このAPIは共通情報のみレスポンスします。

### ワークロード再起動
中止状態のワークロードを再起動します。

```bash
POST /ncs/v1.0/appkeys/{appKey}/workloads/{workloadId}/resume
x-nhn-authorization: {token}
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| workloadId | URL | String | O | ワークロードID |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|

#### レスポンス
このAPIは共通情報のみレスポンスします。

### ワークロード作業再起動
ワークロードの作業を再起動します。

```bash
POST /ncs/v1.0/appkeys/{appKey}/workloads/{workloadId}/tasks/{taskId}/restart
x-nhn-authorization: {token}
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| workloadId | URL | String | O | ワークロードID |
| taskId | URL | String | O | 作業ID |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|

#### レスポンス
このAPIは共通情報のみレスポンスします。

### ワークロードを削除する

ワークロードを削除します。

```bash
DELETE /ncs/v1.0/appkeys/{appKey}/workloads/{workloadId}
x-nhn-authorization: {token}
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| workloadId | URL | String | O | ワークロードID |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|

#### レスポンス

このAPIは共通情報のみレスポンスします。


## レスポンスコード
| resultCode | resultMessage | 説明 |
| --- | --- | --- |
| 200 | SUCCESS | リクエスト成功 |
| 10001 | Authentication error. | 有効ではないAppKeyです。 |
| 10002 | Bad Request. | 有効ではない値がリクエストされました。 |
| 10003 | An error occurred while parsing the request body. | リクエスト本文を構文分析する過程でエラーが発生しました。 |
| 10004 | Internal server error. | 内部サーバーエラーです。 |
| 10005 | No permissions. | 権限がありません。 |
| 10006 | You have exceeded the maximum number of {{.Resource}} that can be created. Please contact the Customer Center to increase the limit. | 作成可能な{{.Resource}}数を超えました。 |
| 10041 | Could not find the template. | テンプレートが見つかりません。 |
| 10042 | Could not use the ECR. | ECRのイメージは使用できません。 |
| 10043 | The network information does not exist. | ネットワーク情報が存在しません。 |
| 10044 | The template in use by the workload cannot be deleted. | ワークロードで使用中のテンプレートは削除できません。 |
| 10045 | Duplicate container port exists in the template. | テンプレートに同じコンテナポートが存在します。 |
| 10046 | Template with the same name already exists. | 同じ名前のテンプレートがすでに存在します。 |
| 10047 | Resource {{.gpuFlavor}} is not available. If you want to use, please contact the Customer Center. | {{.gpuFlavor}}リソースは使用できません。 |
| 10048 | Failed to download ConfigMaps. | ConfigMapのダウンロードに失敗しました。 |
| 10049 | ConfigMaps can only use the object storage from the same organization. | ConfigMapは同じ組織のObject Storageのみ使用できます。 |
| 10050 | The total size of the template's ConfigMap cannot exceed 1 MiB. | テンプレートのConfigMapの総サイズは1MiBを超過できません。 |
| 10051 | Failed to download secrets from Secure Key Manager. | Secure Key Managerでシークレットダウンロードに失敗しました。 |
| 10052 | Could not create a template that consists only of init containers. | 初期化コンテナのみで構成されたテンプレートは作成できません。 |
| 10053 | The {{.Resource}} of an init container must be less than the sum of the normal containers. | 初期化コンテナの{{.Resource}}は、一般コンテナの合計より小さくなければなりません。 |
| 10054 | Could not set the GPU type of an init container differently than a regular container. | 初期化コンテナのGPUタイプを一般コンテナと異なる設定にすることはできません。 |
| 10061 | Could not find the workload. | ワークロードが見つかりません。 |
| 10062 | Task does not exist. | 作業が存在しません。 |
| 10063 | You cannot use the load balancer because the container port is not specified in the template. | テンプレートにコンテナポートが指定されていないためロードバランサーを使用できません。 |
| 10064 | A workload with the same name already exists. | 同じ名前のワークロードがすでに存在します。 |
| 10065 | Invalid container specifications in the template. | テンプレートのコンテナ仕様が有効ではありません。 |
| 10066 | Could not create a workload due to insufficient resources. | リソースが不足しているためワークロードを作成できません。 |
| 10067 | You cannot change the workload name. | ワークロード名は変更できません。 |
| 10068 | You cannot change to the template that uses a different network. | 他のネットワークを使用するテンプレートに変更できません。 |
| 10069 | In the template, you must set the same container port as the port used by the load balancer. | ロードバランサーで使用中のポートと同じコンテナポートがテンプレートに設定されている必要があります。 |
|	10070 | You cannot use the load balancer in legacy network environments. | レガシーネットワーク環境ではロードバランサーを使用できません。 |
|	10071 | UDP protocol cannot use load balancer. | UDPプロトコルはロードバランサーを使用できません。 |
|	10072 | If the workload runs less than {{.Minutes}} minutes, you cannot use the load balancer. | ワークロード実行周期が{{.Minutes}}分以下の場合はロードバランサーを利用できません。 |
|	10073 | Invalid cron expression. | 無効なcron表現式です。 |
|	10074 | Invalid time zone. | 無効なTimezoneです。 |
|	10075 | You cannot change the load balancer to Use while the workload is stopped. | ワークロードが停止状態の時は、ロードバランサーを使用に変更することはできません。 |
|	10076 | The certificate and private key are invalid. To use TERMINATED_HTTPS, you must register a valid certificate and private key. | 証明書と秘密鍵が正しくありません。TERMINATED_HTTPSを使用するには有効な証明書と秘密鍵を登録する必要があります。 |
|	10077 | Only one security group can be used. | 1つのSecurity Groupsのみ使用できます。 |
|	10078 | The actions of all groups must be the same. | グループのactionはすべて同じである必要があります。 |
|	10079 | Invalid Private DNS zone. | 無効なPrivate DNS zoneです。 |
|	10080 | Could not change the load balancer to Enabled while the workload is terminated. | ワークロードが終了状態の時は、ロードバランサーを使用に変更することはできません。 |
|	10081 | The task is not in a restartable state. | 作業が再起動可能な状態ではありません。 |

