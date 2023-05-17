## Container > NHN Container Service(NCS) > APIガイド

ワークロードとテンプレートを構成するためのAPIを記述します。

### APIリクエスト共通情報

APIを使用するにはサービスAppKeyが必要です。サービスAppkeyはコンソール上部の<strong>URL & AppKey</strong>メニューで確認が可能です。
APIドメインは次のとおりです。

| リージョン | ドメイン |
| --- | --- |
| 韓国(パンギョ)リージョン | [http://kr1-ncs.api.nhncloudservice.com](http://kr1-ncs.api.nhncloudservice.com) |

### APIレスポンス共通情報

すべてのAPIリクエストに <strong>200 OK</strong>でレスポンスします。詳細なレスポンス結果はレスポンス本文ヘッダを参照してください。

| 名前 | 種類 | 形式 | 説明 |
| --- | --- | --- | --- |
| header | Body | Object | APIレスポンス情報 |
| header.isSuccessful | Body | Boolean | true:正常<br>false:エラー |
| header.resultCode | Body | Integer | 200:正常<br>10000以上:エラー |
| header.resultMessage | Body | String | "SUCCESS":正常<br>その他:エラー原因メッセージ |

<details><summary>例</summary>

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

```
GET /ncs/v1.0/appkeys/{appKey}/templates?page=1&size=30
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| page | Query | Integer | X | 照会するページ番号 |
| size | Query | Integer | X | 照会するページサイズ(default: 10) |

#### レスポンス

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| X-Total-Count | Header | Integer | O | Appkeyに作成されているテンプレートの全体数 |
| templates | Body | Array | O | テンプレートリスト |
| templates.createdAt | Body | String | O | 作成時間(UTC) |
| templates.id | Body | UUID | O | テンプレートID |
| templates.name | Body | String | O | テンプレート名 |
| templates.description | Body | String | X | テンプレートの説明 |
| templates.containers | Body | Array | O | テンプレートのコンテナリスト |
| templates.containers.name | Body | String | O | コンテナ名 |
| templates.containers.image | Body | String | O | コンテナイメージ |
| templates.containers.cpus | Body | Float | O | コンテナに割り当てるCPUの数 |
| templates.containers.memoryLimit | Body | Object | O | コンテナに割り当てるメモリ情報 |
| templates.containers.memoryLimit.hard | Body | Integer | O | コンテナに割り当てるメモリ(MiB) |
| templates.containers.gpus | Body | Integer | X | コンテナのGPU使用有無<br>0:未使用<br>1:使用 |
| templates.containers.gpuFlavor | Body | String | X | GPU Flavor情報<br>\* ncs1.g1m5<br>\* ncs1.g2m10 |
| templates.containers.ports | Body | Array | X | コンテナで使用するポート情報 |
| templates.containers.ports.containerPort | Body | Integer | O | コンテナポート |
| templates.containers.ports.protocol | Body | String | O | コンテナプロトコル |
| templates.containers.command | Body | String List | X | コンテナが起動する時に実行されるコマンド |
| templates.containers.env | Body | Array | X | コンテナ環境変数 |
| templates.containers.env.name | Body | String | O | コンテナ環境変数名 |
| templates.containers.env.value | Body | String | O | コンテナ環境変数の値 |
| templates.containers.volumes | Body | Array | X | コンテナで使用するストレージ情報 |
| templates.containers.volumes.name | Body | String | O | ストレージ名 |
| templates.containers.volumes.path | Body | String | O | NASストレージ接続パス |
| templates.containers.volumes.mountPath | Body | String | X | コンテナストレージ接続パス |
| templates.containers.workDirectory | Body | String | X | コンテナの作業ディレクトリ |
| templates.networks | Body | Array | O | テンプレートのネットワーク情報 |
| templates.networks.vpcId | Body | String | O | テンプレートのVPC ID |
| templates.networks.subnetId | Body | String | O | テンプレートのSubnet ID |

<details><summary>例</summary>

```json
{
  "header": {
    "resultCode": 200,
    "resultMessage": "SUCCESS",
    "isSuccessful": true
  },
  "templates": [
    {
      "createdAt": "2023-03-27T04:53:37.378Z",
      "id": "f4aa3715-a2ce-415f-97e0-787e723fcba6",
      "name": "ncs-template",
      "description": "NCS Template",
      "containers": [
        {
          "name": "public",
          "image": "nginx:latest",
          "cpus": 1,
          "memoryLimit": {
            "hard": 1024
          },
          "ports": [
            {
              "containerPort": 80,
              "protocol": "TCP"
            }
          ],
          "workDirectory": null
        }
      ],
      "networks": [
        {
          "vpcId": "5890813e-0e40-4867-acbc-c41b8bf5ba1e",
          "subnetId": "190a0825-0518-4fe8-8c76-e24f16e78af3"
        }
      ]
    }
  ]
}
```
</details>

### テンプレートの表示

個別テンプレート情報を照会します。

```
GET /ncs/v1.0/appkeys/{appKey}/templates/{templateId}
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| templateId | URL | String | O | テンプレートID |

#### レスポンス

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| templates | Body | Object | O | テンプレート情報 |
| templates.createdAt | Body | String | O | 作成時間(UTC) |
| templates.id | Body | UUID | O | テンプレートID |
| templates.name | Body | String | O | テンプレート名 |
| templates.description | Body | String | X | テンプレートの説明 |
| templates.containers | Body | Array | O | テンプレートのコンテナリスト |
| templates.containers.name | Body | String | O | コンテナ名 |
| templates.containers.image | Body | String | O | コンテナイメージ |
| templates.containers.cpus | Body | Float | O | コンテナに割り当てるCPUの数 |
| templates.containers.memoryLimit | Body | Object | O | コンテナに割り当てるメモリ情報 |
| templates.containers.memoryLimit.hard | Body | Integer | O | コンテナに割り当てるメモリ(MiB) |
| templates.containers.gpus | Body | Integer | X | コンテナのGPU使用有無<br>0:未使用<br>1:使用 |
| templates.containers.gpuFlavor | Body | String | X | GPU Flavor情報<br>\* ncs1.g1m5<br>\* ncs1.g2m10 |
| templates.containers.ports | Body | Array | X | コンテナで使用するポート情報 |
| templates.containers.ports.containerPort | Body | Integer | O | コンテナポート |
| templates.containers.ports.protocol | Body | String | O | コンテナプロトコル |
| templates.containers.command | Body | String List | X | コンテナが起動する時に実行されるコマンド |
| templates.containers.env | Body | Array | X | コンテナ環境変数 |
| templates.containers.env.name | Body | String | O | コンテナ環境変数名 |
| templates.containers.env.value | Body | String | O | コンテナ環境変数の値 |
| templates.containers.volumes | Body | Array | X | コンテナで使用するストレージ情報 |
| templates.containers.volumes.name | Body | String | O | ストレージ名 |
| templates.containers.volumes.path | Body | String | O | NASストレージ接続パス |
| templates.containers.volumes.mountPath | Body | String | X | コンテナストレージ接続パス |
| templates.containers.workDirectory | Body | String | X | コンテナの作業ディレクトリ |
| templates.networks | Body | Array | O | テンプレートのネットワーク情報 |
| templates.networks.vpcId | Body | String | O | テンプレートのVPC ID |
| templates.networks.subnetId | Body | String | O | テンプレートのSubnet ID |

<details><summary>例</summary>

```json
{
  "header": {
    "resultCode": 200,
    "resultMessage": "SUCCESS",
    "isSuccessful": true
  },
  "template": {
    "createdAt": "2023-03-27T04:53:37.378Z",
    "id": "f4aa3715-a2ce-415f-97e0-787e723fcba6",
    "name": "ncs-template",
    "description": "NCS Template",
    "containers": [
      {
        "name": "public",
        "image": "nginx:latest",
        "cpus": 1,
        "memoryLimit": {
          "hard": 1024
        },
        "ports": [
          {
            "containerPort": 80,
            "protocol": "TCP"
          }
        ],
        "workDirectory": null
      }
    ],
    "networks": [
      {
        "vpcId": "5890813e-0e40-4867-acbc-c41b8bf5ba1e",
        "subnetId": "190a0825-0518-4fe8-8c76-e24f16e78af3"
      }
    ]
  }
}
```
</details>

### テンプレートを作成する

テンプレートを作成します。

```
POST /ncs/v1.0/appkeys/{appKey}/templates
Content-Type: application/json
```

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| templates | Body | Object | O | テンプレート情報 |
| templates.name | Body | String | O | テンプレート名 |
| templates.description | Body | String | X | テンプレートの説明 |
| templates.containers | Body | Array | O | テンプレートのコンテナリスト |
| templates.containers.name | Body | String | O | コンテナ名 |
| templates.containers.image | Body | String | O | コンテナイメージ |
| templates.containers.imageRegistryCredentials | Body | Object | X | Privateレジストリにアクセス可能な情報 |
| templates.containers.imageRegistryCredentials.username | Body | String | O | PrivateレジストリID |
| templates.containers.imageRegistryCredentials.password | Body | String | O | Privateレジストリパスワード |
| templates.containers.cpus | Body | Float | O | コンテナに割り当てるCPUの数 |
| templates.containers.memoryLimit | Body | Object | O | コンテナに割り当てるメモリ情報 |
| templates.containers.memoryLimit.hard | Body | Integer | O | コンテナに割り当てるメモリ(MiB) |
| templates.containers.gpus | Body | Integer | X | コンテナのGPU使用有無<br>0:未使用<br>1:使用 |
| templates.containers.gpuFlavor | Body | String | X | GPU Flavor情報<br>\* ncs1.g1m5<br>\* ncs1.g2m10 |
| templates.containers.ports | Body | Array | X | コンテナで使用するポート情報 |
| templates.containers.ports.containerPort | Body | Integer | O | コンテナポート |
| templates.containers.ports.protocol | Body | String | O | コンテナプロトコル |
| templates.containers.command | Body | String List | X | コンテナが起動する時に実行されるコマンド |
| templates.containers.env | Body | Array | X | コンテナ環境変数 |
| templates.containers.env.name | Body | String | O | コンテナ環境変数名 |
| templates.containers.env.value | Body | String | O | コンテナ環境変数の値 |
| templates.containers.volumes | Body | Array | X | コンテナで使用するストレージ情報 |
| templates.containers.volumes.name | Body | String | O | ストレージ名 |
| templates.containers.volumes.path | Body | String | O | NASストレージ接続パス |
| templates.containers.volumes.mountPath | Body | String | X | コンテナストレージ接続パス |
| templates.containers.workDirectory | Body | String | X | コンテナの作業ディレクトリ |
| templates.networks | Body | Array | O | テンプレートのネットワーク情報 |
| templates.networks.vpcId | Body | String | O | テンプレートのVPC ID<br>VPCの詳細については[VPCリスト表示](Network/VPC/ko/public-api/#vpc_1)を参照してください。 |
| templates.networks.subnetId | Body | String | O | テンプレートのSubnet ID<br>Subnetの詳細については[VPCサブネットリスト表示](/Network/VPC/ko/public-api/#vpc_7)を参照してください。 |

<details><summary>例</summary>

```json
{
  "template": {
    "containers": [
      {
        "cpus": 1,
        "image": "nginx:latest",
        "memoryLimit": {
          "hard": 1024
        },
        "name": "public",
        "ports": [
          {
            "containerPort": 80,
            "protocol": "TCP"
          }
        ]
      }
    ],
    "description": "NCS Template",
    "name": "ncs-template",
    "networks": [
      {
        "subnetId": "190a0825-0518-4fe8-8c76-e24f16e78af3",
        "vpcId": "5890813e-0e40-4867-acbc-c41b8bf5ba1e"
      }
    ]
  }
}
```
</details>

#### レスポンス

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| templates | Body | Object | O | テンプレート情報 |
| templates.createdAt | Body | String | O | 作成時間(UTC) |
| templates.id | Body | UUID | O | テンプレートID |
| templates.name | Body | String | O | テンプレート名 |
| templates.description | Body | String | X | テンプレートの説明 |
| templates.containers | Body | Array | O | テンプレートのコンテナリスト |
| templates.containers.name | Body | String | O | コンテナ名 |
| templates.containers.image | Body | String | O | コンテナイメージ |
| templates.containers.cpus | Body | Float | O | コンテナに割り当てるCPUの数 |
| templates.containers.memoryLimit | Body | Object | O | コンテナに割り当てるメモリ情報 |
| templates.containers.memoryLimit.hard | Body | Integer | O | コンテナに割り当てるメモリ(MiB) |
| templates.containers.gpus | Body | Integer | X | コンテナのGPU使用有無<br>0:未使用<br>1:使用 |
| templates.containers.gpuFlavor | Body | String | X | GPU Flavor情報<br>\* ncs1.g1m5<br>\* ncs1.g2m10 |
| templates.containers.command | Body | String List | X | コンテナが起動する時に実行されるコマンド |
| templates.containers.env | Body | Array | X | コンテナ環境変数 |
| templates.containers.env.name | Body | String | O | コンテナ環境変数名 |
| templates.containers.env.value | Body | String | O | コンテナ環境変数の値 |
| templates.containers.volumes | Body | Array | X | コンテナで使用するストレージ情報 |
| templates.containers.volumes.name | Body | String | O | ストレージ名 |
| templates.containers.volumes.path | Body | String | O | NASストレージ接続パス |
| templates.containers.volumes.mountPath | Body | String | X | コンテナストレージ接続パス |
| templates.containers.workDirectory | Body | String | X | コンテナの作業ディレクトリ |
| templates.networks | Body | Array | O | テンプレートのネットワーク情報 |
| templates.networks.vpcId | Body | String | O | テンプレートのVPC ID |
| templates.networks.subnetId | Body | String | O | テンプレートのSubnet ID |

<details><summary>例</summary>

```json
{
   "header":{
      "resultCode":200,
      "resultMessage":"SUCCESS",
      "isSuccessful":true
   },
   "template":{
      "createdAt":"2023-03-27T04:53:37.378Z",
      "id":"f4aa3715-a2ce-415f-97e0-787e723fcba6",
      "name":"ncs-template",
      "description":"NCS Template",
      "containers":[
         {
            "name":"public",
            "image":"nginx:latest",
            "cpus":1,
            "memoryLimit":{
               "hard":1024
            },
            "ports":[
               {
                  "containerPort":80,
                  "protocol":"TCP"
               }
            ],
            "workDirectory":null
         }
      ],
      "networks":[
         {
            "vpcId":"5890813e-0e40-4867-acbc-c41b8bf5ba1e",
            "subnetId":"190a0825-0518-4fe8-8c76-e24f16e78af3"
         }
      ]
   }
}
```
</details>

### テンプレートを削除する

テンプレートを削除します。

```
DELETE /ncs/v1.0/appkeys/{appKey}/templates/{templateId}
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| templateId | URL | String | O | テンプレートID |

#### レスポンス

このAPIは共通情報のみレスポンスします。

## ワークロード

### ワークロードリスト表示

ワークロードリストを照会します。

```
GET /ncs/v1.0/appkeys/{appKey}/workloads?page=1&size=30
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| page | Query | Integer | X | 照会するページ番号 |
| size | Query | Integer | X | 照会するページサイズ(default: 10) |

#### レスポンス

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| X-Total-Count | Header | Integer | O | Appkeyに作成されているワークロード全体数 |
| workloads | Body | Array | O | ワークロードリスト |
| workloads.createdAt | Body | String | O | 作成時間(UTC) |
| workloads.id | Body | UUID | O | ワークロードID |
| workloads.name | Body | String | O | ワークロード名 |
| workloads.templateId | Body | String | O | ワークロードのテンプレートID |
| workloads.url | Body | String | X | ワークロードロードバランサーURL |
| workloads.desired | Body | Integer | O | ワークロード作業リクエスト数 |
| workloads.available | Body | Integer | O | ワークロード作業実行数 |
| workloads.status | Body | Integer | O | ワークロード状態 |
| workloads.loadBalancing | Body | Object | O | ワークロードロードバランサー情報 |
| workloads.loadBalancing.enabled | Body | Boolean | O | ワークロードロードバランサー使用有無 |
| workloads.loadBalancing.floatingIp | Body | Boolean | O | ワークロードロードバランサーFloating IP使用有無 |

<details><summary>例</summary>

```json
{
  "header": {
    "resultCode": 200,
    "resultMessage": "SUCCESS",
    "isSuccessful": true
  },
  "workload": {
    "createdAt": "2023-03-27T05:07:51.479Z",
    "id": "74671f9a-fd9b-457f-be01-bbab9ad831a1",
    "name": "ncs-workload",
    "url": "74671f9a-alp-kr1-ncs.container.nhncloud.com.",
    "desired": 1,
    "available": 0,
    "status": "",
    "loadBalancing": {
      "enabled": true,
      "floatingIp": true
    }
  }
}
```
</details>

### ワークロード表示

個別ワークロードを照会します。

```
GET /ncs/v1.0/appkeys/{appKey}/workloads/{workloadId}
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| workloadId | URL | String | O | ワークロードID |

#### レスポンス

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| X-Total-Count | Header | Integer | O | Appkeyに作成されているワークロード全体数 |
| workloads | Body | Array | O | ワークロードリスト |
| workloads.createdAt | Body | String | O | 作成時間(UTC) |
| workloads.id | Body | UUID | O | ワークロードID |
| workloads.name | Body | String | O | ワークロード名 |
| workloads.templateId | Body | String | O | ワークロードのテンプレートID |
| workloads.url | Body | String | X | ワークロードロードバランサーURL |
| workloads.desired | Body | Integer | O | ワークロード作業リクエスト数 |
| workloads.available | Body | Integer | O | ワークロード作業実行数 |
| workloads.status | Body | String | O | ワークロード状態 |
| workloads.tasks | Body | Array | O | ワークロードの作業リスト |
| workloads.tasks.id | Body | UUID | O | 作業ID |
| workloads.tasks.containers | Body | Array | O | 作業のコンテナリスト |
| workloads.tasks.containers.name | Body | String | O | コンテナ名 |
| workloads.tasks.containers.image | Body | String | O | コンテナイメージ |
| workloads.tasks.containers.ip | Body | String | X | コンテナIP |
| workloads.tasks.containers.cpus | Body | Float | O | コンテナに割り当てられたCPUの数 |
| workloads.tasks.containers.memoryLimit | Body | Object | O | コンテナに割り当てられたメモリ情報 |
| workloads.tasks.containers.memoryLimit.hard | Body | Integer | O | コンテナに割り当てられたメモリ(MiB) |
| workloads.tasks.containers.gpus | Body | Integer | X | コンテナのGPU使用有無<br>0:未使用<br>1:使用 |
| workloads.tasks.containers.gpuFlavor | Body | String | X | コンテナに割り当てられたGPU Flavor情報 |
| workloads.tasks.containers.ports | Body | Array | X | コンテナのポート情報 |
| workloads.tasks.containers.ports.containerPort | Body | Integer | O | コンテナポート |
| workloads.tasks.containers.ports.protocol | Body | String | O | コンテナプロトコル |
| workloads.tasks.containers.command | Body | String List | X | コンテナが削除される時に実行されるコマンド |
| workloads.tasks.containers.env | Body | Array | X | コンテナ環境変数 |
| workloads.tasks.containers.env.name | Body | String | O | コンテナ環境変数の名前 |
| workloads.tasks.containers.env.value | Body | String | O | コンテナ環境変数の値 |
| workloads.tasks.containers.volumes | Body | Array | X | コンテナで使用するストレージ情報 |
| workloads.tasks.containers.volumes.name | Body | String | O | ストレージ名 |
| workloads.tasks.containers.volumes.path | Body | String | O | NASストレージ接続パス |
| workloads.tasks.containers.volumes.mountPath | Body | String | X | コンテナストレージ接続パス |
| workloads.tasks.containers.workDirectory | Body | String | X | コンテナの作業ディレクトリ |
| workloads.tasks.containers.state | Body | String | O | コンテナ状態 |
| workloads.tasks.containers.startedAt | Body | String | X | コンテナ開始時間 |
| workloads.tasks.containers.restartCount | Body | String | O | コンテナ再起動回数 |
| workloads.loadBalancing | Body | Object | O | ワークロードロードバランサー情報 |
| workloads.loadBalancing.enabled | Body | Boolean | O | ワークロードロードバランサー使用有無 |
| workloads.loadBalancing.floatingIp | Body | Boolean | O | ワークロードロードバランサーFloating IP使用有無 |
| workloads.loadBalancing.ipAddress | Body | String | X | ワークロードのIP、Floating IP |
| workloads.networks | Body | Array | O | ワークロードのネットワーク情報 |
| workloads.networks.vpcId | Body | String | O | ワークロードのVPC ID |
| workloads.networks.subnetId | Body | String | O | ワークロードのSubnet ID |
| workloads.securityGroups | Body | String | X | ワークロードのセキュリティグループ情報 |
| workloads.securityGroups.id | Body | String | O | ワークロードのセキュリティグループID |

<details><summary>例</summary>

```json
{
  "header": {
    "resultCode": 200,
    "resultMessage": "SUCCESS",
    "isSuccessful": true
  },
  "workload": {
    "createdAt": "2023-03-27T05:07:51.479Z",
    "id": "74671f9a-fd9b-457f-be01-bbab9ad831a1",
    "name": "ncs-workload",
    "url": "74671f9a-alp-kr1-ncs.container.nhncloud.com",
    "desired": 1,
    "available": 1,
    "status": "Running",
    "tasks": [
      {
        "id": "2f7e2a88-520a-4807-a6ad-331950b1b318",
        "containers": [
          {
            "name": "public",
            "image": "nginx:latest",
            "ip": "192.168.0.71",
            "cpus": 1,
            "memoryLimit": {
              "hard": 1024
            },
            "ports": [
              {
                "containerPort": 80,
                "protocol": "TCP"
              }
            ],
            "workDirectory": null,
            "state": "Running",
            "startedAt": "2023-03-27T04:58:42Z",
            "restartCount": 0
          }
        ]
      }
    ],
    "networks": [
      {
        "vpcId": "5890813e-0e40-4867-acbc-c41b8bf5ba1e",
        "subnetId": "190a0825-0518-4fe8-8c76-e24f16e78af3"
      }
    ],
    "securityGroups": [
      {
        "id": "9288125a-a7e0-4f89-8c95-7878acb3f3b2"
      }
    ],
    "loadBalancing": {
      "enabled": true,
      "floatingIp": true,
      "ipAddress": "192.168.0.57, 133.186.163.71"
    }
  }
}
```
</details>

### ワークロードログ表示

ワークロードのコンテナログを照会します。

```
GET /ncs/v1.0/appkeys/{appKey}/workloads/{workloadId}/tasks/{taskId}/logs?container={ContainerName}&from={YYYY-MM-DDThh:mm:ssZ}&to={YYYY-MM-DDThh:mm:ssZ}
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| container | Query | String | O | コンテナ名 |
| limitBytes | Query | Integer | X | 照会する最大ログサイズ<br>default: 100 KB<br>最大: 10MB |
| from | Query | String | X | ログ開始時間<br>default:現在から5分前 |
| to | Query | String | X | ログ終了時間<br>default:現在時間 |

#### レスポンス

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| logs | Body | Array | O | コンテナログリスト |
| log | Body | String | O | ログ内容 |
| time | Body | String | O | ログ発生時間(UTC) |

<details><summary>例</summary>

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
      "time": "2023-03-27T04:58:42.683833139Z"
    }
  ]
}
```
</details>

### ワークロードイベント表示

ワークロードのイベントを照会します。

```
GET /ncs/v1.0/appkeys/{appKey}/workloads/{workloadId}/tasks/{taskId}/events?page=1&size=30
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| type | Query | Integer | X | イベントタイプ<br>Normal<br>Warning |
| q | Query | String | X | イベント内容フィルタリング |
| page | Query | String | X | 照会するページ |
| size | Query | String | X | 照会するページサイズ(default: 10) |

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

<details><summary>例</summary>

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
      "reason": "Started",
      "message": "Started container public",
      "createTimestamp": "2023-03-27T04:58:42Z",
      "lastTimestamp": "2023-03-27T04:58:42Z",
      "count": 1
    }
  ]
}
```
</details>

### ワークロード実行ヒストリーリスト表示

ワークロード実行ヒストリーリストを照会します。

```
GET /ncs/v1.0/appkeys/{appKey}/workloads/{workloadId}/history?page=1&size=30
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| page | Query | Integer | X | 照会するページ番号 |
| size | Query | Integer | X | 照会するページサイズ(default: 10) |

#### レスポンス

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| history | Body | Array | O | ヒストリーリスト |
| history.id | Body | Integer | O | ヒストリーID |
| history.createdAt | Body | String | O | 配布時間 |
| history.deletedAt | Body | String | O | 終了時間 |
| history.templateId | Body | UUID | O | ワークロードが使用したテンプレートID |
| history.name | Body | String | O | テンプレート名 |
| history.status | Body | String | O | 状態<br>Succeeded<br>Terminated<br>Pending |

<details><summary>例</summary>

```json
{
  "header": {
    "resultCode": 200,
    "resultMessage": "SUCCESS",
    "isSuccessful": true
  },
  "history": [
    {
      "id": 1,
      "createdAt": "2023-03-27T04:58:23.294Z",
      "deletedAt": null,
      "templateId": "f4aa3715-a2ce-415f-97e0-787e723fcba6",
      "name": "ncs-template",
      "status": "Succeeded"
    }
  ]
}
```
</details>

### ワークロード実行ヒストリー表示

個別ワークロード実行ヒストリーを照会します。

```
GET /ncs/v1.0/appkeys/{appKey}/workloads/{workloadId}/history/{historyId}
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| historyId | URL | Integer | O | ヒストリーID |

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
| templates | Body | Object | O | テンプレート情報 |
| templates.createdAt | Body | String | O | テンプレート作成時間(UTC) |
| templates.deletedAt | Body | String | O | テンプレート削除時間(UTC) |
| templates.id | Body | UUID | O | テンプレートID |
| templates.name | Body | String | O | テンプレート名 |
| templates.description | Body | String | X | テンプレートの説明 |
| templates.containers | Body | Array | O | テンプレートのコンテナリスト |
| templates.containers.name | Body | String | O | コンテナ名 |
| templates.containers.image | Body | String | O | コンテナイメージ |
| templates.containers.cpus | Body | Float | O | コンテナに割り当てるCPUの数 |
| templates.containers.memoryLimit | Body | Object | O | コンテナに割り当てるメモリ情報 |
| templates.containers.memoryLimit.hard | Body | Integer | O | コンテナに割り当てるメモリ(MiB) |
| templates.containers.gpus | Body | Integer | X | コンテナのGPU使用有無<br>0:未使用<br>1:使用 |
| templates.containers.gpuFlavor | Body | String | X | コンテナに割り当てられたGPU Flavor情報 |
| templates.containers.ports | Body | Array | X | コンテナで使用するポート情報 |
| templates.containers.ports.containerPort | Body | Integer | O | コンテナポート |
| templates.containers.ports.protocol | Body | String | O | コンテナプロトコル |
| templates.containers.command | Body | String List | X | コンテナが起動する時に実行されるコマンド |
| templates.containers.env | Body | Array | X | コンテナ環境変数 |
| templates.containers.env.name | Body | String | O | コンテナ環境変数名 |
| templates.containers.env.value | Body | String | O | コンテナ環境変数の値 |
| templates.containers.volumes | Body | Array | X | コンテナで使用するストレージ情報 |
| templates.containers.volumes.name | Body | String | O | ストレージ名 |
| templates.containers.volumes.path | Body | String | O | NASストレージ接続パス |
| templates.containers.volumes.mountPath | Body | String | X | コンテナストレージ接続パス |
| templates.containers.workDirectory | Body | String | X | コンテナの作業ディレクトリ |
| templates.networks | Body | Array | O | テンプレートのネットワーク情報 |
| templates.networks.vpcId | Body | String | O | テンプレートのVPC ID |
| templates.networks.subnetId | Body | String | O | テンプレートのSubnet ID |

<details><summary>例</summary>

```json
{
  "header": {
    "resultCode": 200,
    "resultMessage": "SUCCESS",
    "isSuccessful": true
  },
  "history": {
    "id": 1,
    "createdAt": "2023-03-27T04:58:23.294Z",
    "deletedAt": null,
    "templateId": "f4aa3715-a2ce-415f-97e0-787e723fcba6",
    "name": "ncs-template",
    "status": "Succeeded"
  },
  "template": {
    "createdAt": "2023-03-27T04:53:37.378Z",
    "updatedAt": "2023-03-27T04:53:37.378Z",
    "id": "f4aa3715-a2ce-415f-97e0-787e723fcba6",
    "name": "ncs-template",
    "description": "NCS Template",
    "containers": [
      {
        "name": "public",
        "image": "nginx:latest",
        "cpus": 1,
        "memoryLimit": {
          "hard": 1024
        },
        "ports": [
          {
            "containerPort": 80,
            "protocol": "TCP"
          }
        ],
        "workDirectory": null
      }
    ],
    "networks": [
      {
        "vpcId": "5890813e-0e40-4867-acbc-c41b8bf5ba1e",
        "subnetId": "190a0825-0518-4fe8-8c76-e24f16e78af3"
      }
    ]
  }
}
```
</details>

### ワークロードを作成する

ワークロードを作成します。

```
POST /ncs/v1.0/appkeys/{appKey}/workloads
Content-Type: application/json
```

#### リクエスト

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| workload | Body | Object | O | ワークロード情報 |
| workload.desired | Body | Integer | O | ワークロード作業リクエスト数 |
| workload.loadBalancing | Body | Object | O | ワークロードロードバランサー情報 |
| workload.loadBalancing.enabled | Body | Boolean | O | ワークロードロードバランサーの使用有無 |
| workload.loadBalancing.floatingIp | Body | Boolean | O | ワークロードロードバランサーFloating IP使用有無 |
| workload.name | Body | String | O | ワークロード名 |
| workload.templateId | Body | String | O | ワークロードのテンプレートID |

<details><summary>例</summary>

```json
{
  "workload": {
    "desired": 1,
    "loadBalancing": {
      "enabled": false,
      "floatingIp": false
    },
    "name": "ncs-workload",
    "templateId": "f4aa3715-a2ce-415f-97e0-787e723fcba6"
  }
}
```
</details>

#### レスポンス

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| workloads | Body | Object | O | ワークロード情報 |
| workloads.createdAt | Body | String | O | 作成時間(UTC) |
| workloads.id | Body | UUID | O | ワークロードID |
| workloads.name | Body | String | O | ワークロード名 |
| workloads.templateId | Body | String | O | ワークロードのテンプレートID |
| workloads.url | Body | String | X | ワークロードロードバランサーURL |
| workloads.desired | Body | Integer | O | ワークロード作業リクエスト数 |
| workloads.loadBalancing | Body | Object | O | ワークロードロードバランサー情報 |
| workloads.loadBalancing.enabled | Body | Boolean | O | ワークロードロードバランサー使用有無 |
| workloads.loadBalancing.floatingIp | Body | Boolean | O | ワークロードロードバランサーFloating IP使用有無 |

<details><summary>例</summary>

```json
{
   "header":{
      "resultCode":200,
      "resultMessage":"SUCCESS",
      "isSuccessful":true
   },
   "workload":{
      "createdAt":"2023-03-27T04:58:23.295Z",
      "id":"74671f9a-fd9b-457f-be01-bbab9ad831a1",
      "name":"ncs-workload",
      "namespace":"2297a20f-8fa8-49c2-a75a-86cdd257e945",
      "templateId":"f4aa3715-a2ce-415f-97e0-787e723fcba6",
      "desired":1,
      "loadBalancing":{
         "enabled":false,
         "floatingIp":false
      }
   }
}
```
</details>

### ワークロードを変更する

ワークロードを変更します。

```
PUT /ncs/v1.0/appkeys/{appKey}/workloads/{workloadId}
Content-Type: application/json
```

#### リクエスト

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| workloadId | URL | String | O | ワークロードID |
| workload.desired | Body | Integer | O | ワークロード作業リクエスト数 |
| workload.loadBalancing | Body | Object | O | ワークロードロードバランサー情報 |
| workload.loadBalancing.enabled | Body | Boolean | O | ワークロードロードバランサーの使用有無 |
| workload.loadBalancing.floatingIp | Body | Boolean | O | ワークロードロードバランサーFloating IP使用有無 |
| workload.name | Body | String | O | ワークロード名 |
| workload.templateId | Body | String | O | ワークロードのテンプレートID |

<details><summary>例</summary>

```json
{
  "workload": {
    "desired": 1,
    "loadBalancing": {
      "enabled": true,
      "floatingIp": true
    },
    "name": "ncs-workload",
    "templateId": "f4aa3715-a2ce-415f-97e0-787e723fcba6"
  }
}
```
</details>

#### レスポンス

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| workloads | Body | Object | O | ワークロード情報 |
| workloads.createdAt | Body | String | O | 作成時間(UTC) |
| workloads.id | Body | UUID | O | ワークロードID |
| workloads.name | Body | String | O | ワークロード名 |
| workloads.templateId | Body | String | O | ワークロードのテンプレートID |
| workloads.url | Body | String | X | ワークロードロードバランサーURL |
| workloads.desired | Body | Integer | O | ワークロード作業リクエスト数 |
| workloads.loadBalancing | Body | Object | O | ワークロードロードバランサー情報 |
| workloads.loadBalancing.enabled | Body | Boolean | O | ワークロードロードバランサー使用有無 |
| workloads.loadBalancing.floatingIp | Body | Boolean | O | ワークロードロードバランサーFloating IP使用有無 |

<details><summary>例</summary>

```json
{
  "header": {
    "resultCode": 200,
    "resultMessage": "SUCCESS",
    "isSuccessful": true
  },
  "workload": {
    "createdAt": "2023-03-27T05:01:46.771Z",
    "updatedAt": "2023-03-27T05:01:46.771Z",
    "id": "74671f9a-fd9b-457f-be01-bbab9ad831a1",
    "name": "ncs-workload",
    "namespace": "2297a20f-8fa8-49c2-a75a-86cdd257e945",
    "appKey": "zxKhNwt9eMUwTDEa",
    "templateId": "f4aa3715-a2ce-415f-97e0-787e723fcba6",
    "desired": 1,
    "available": 0,
    "status": "",
    "loadBalancing": {
      "enabled": true,
      "floatingIp": true
    }
  }
}
```
</details>

### ワークロードを削除する

ワークロードを削除します。

```
DELETE /ncs/v1.0/appkeys/{appKey}/workloads/{workloadId}
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| workloadId | URL | String | O | ワークロードID |

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
| 10006 | You have exceeded the maximum number of {{.Resource}} that can be created. Please contact the Customer Center to increase the limit. | 作成可能な{{.Resource}}数を超えました。 |
| 10041 | Could not find the template. | テンプレートが見つかりません。 |
| 10042 | Could not use the ECR. | ECRのイメージは使用できません。 |
| 10043 | The network information does not exist. | ネットワーク情報が存在しません。 |
| 10044 | The template in use by the workload cannot be deleted. | ワークロードで使用中のテンプレートは削除できません。 |
| 10045 | Duplicate container port exists in the template. | テンプレートに同じコンテナポートが存在します。 |
| 10046 | Template with the same name already exists. | 同じ名前のテンプレートがすでに存在します。 |
| 10047 | Resource {{.gpuFlavor}} is not available. If you want to use, please contact the Customer Center. | {{.gpuFlavor}}リソースは使用できません。 |
| 10061 | Could not find the workload. | ワークロードが見つかりません。 |
| 10062 | Pod does not exist. | Podが存在しません。 |
| 10063 | You cannot use the load balancer because the container port is not specified in the template. | テンプレートにコンテナポートが指定されていないためロードバランサーを使用できません。 |
| 10064 | A workload with the same name already exists. | 同じ名前のワークロードがすでに存在します。 |
| 10065 | Invalid container specifications in the template. | テンプレートのコンテナ仕様が有効ではありません。 |
| 10066 | Could not create a workload due to insufficient resources. | リソースが不足しているためワークロードを作成できません。 |
| 10067 | You cannot change the workload name. | ワークロード名は変更できません。 |
| 10068 | You cannot change to the template that uses a different network. | 他のネットワークを使用するテンプレートに変更できません。 |
| 10069 | In the template, you must set the same container port as the port used by the load balancer. | ロードバランサーで使用中のポートと同じコンテナポートがテンプレートに設定されている必要があります。 |
