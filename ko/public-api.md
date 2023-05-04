## Container > NHN Container Service(NCS) > API 가이드

워크로드와 템플릿을 구성하기 위한 API를 기술합니다.

### API 요청 공통 정보

API를 사용하려면 서비스 AppKey가 필요합니다. 서비스 Appkey는 콘솔 상단 <strong>URL & AppKey</strong>메뉴에서 확인이 가능합니다.
API 도메인은 다음과 같습니다.

| 리전 | 도메인 |
| --- | --- |
| 한국(판교) 리전 | [http://kr1-ncs.api.nhncloudservice.com](http://kr1-ncs.api.nhncloudservice.com) |

### API 응답 공통 정보

모든 API 요청에 <strong>200 OK</strong>로 응답합니다. 자세한 응답 결과는 응답 본문 헤더를 참고합니다.

| 이름 | 종류 | 형식 | 설명 |
| --- | --- | --- | --- |
| header | Body | Object | API 응답 정보 |
| header.isSuccessful | Body | Boolean | true: 정상<br>false: 오류 |
| header.resultCode | Body | Integer | 200: 정상<br>10000 이상: 오류 |
| header.resultMessage | Body | String | "SUCCESS": 정상<br>그 외: 오류 원인 메시지 |

<details><summary>예시</summary>

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

> [주의]
> API 응답에 가이드에 명시되지 않은 필드가 나타날 수 있습니다. 이런 필드는 NHN Cloud 내부 용도로 사용되며 사전 공지 없이 변경될 수 있으므로 사용하지 않습니다.

## 템플릿

### 템플릿 목록 보기

템플릿 목록을 조회합니다.

```
GET /ncs/v1.0/appkeys/{appKey}/templates?page=1&size=30
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| page | Query | Integer | X | 조회할 페이지 번호 |
| size | Query | Integer | X | 조회할 페이지 크기 (default: 10) |

#### 응답

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| X-Total-Count | Header | Integer | O | Appkey에 생성되어 있는 템플릿 전체 수 |
| templates | Body | Array | O | 템플릿 목록 |
| templates.createdAt | Body | String | O | 생성 시간(UTC) |
| templates.id | Body | UUID | O | 템플릿 ID |
| templates.name | Body | String | O | 템플릿 이름 |
| templates.description | Body | String | X | 템플릿 설명 |
| templates.containers | Body | Array | O | 템플릿의 컨테이너 목록 |
| templates.containers.name | Body | String | O | 컨테이너 이름 |
| templates.containers.image | Body | String | O | 컨테이너 이미지 |
| templates.containers.cpus | Body | Float | O | 컨테이너에 할당하는 CPU 개수 |
| templates.containers.memoryLimit | Body | Object | O | 컨테이너에 할당하는 메모리 정보 |
| templates.containers.memoryLimit.hard | Body | Integer | O | 컨테이너에 할당하는 메모리 (MiB) |
| templates.containers.gpus | Body | Integer | X | 컨테이너의 GPU 사용 여부<br>0: 미사용<br>1: 사용 |
| templates.containers.gpuFlavor | Body | String | X | GPU Flavor 정보<br>\* ncs1.g1m5<br>\* ncs1.g2m10 |
| templates.containers.ports | Body | Array | X | 컨테이너에서 사용하는 포트 정보 |
| templates.containers.ports.containerPort | Body | Integer | O | 컨테이너 포트 |
| templates.containers.ports.protocol | Body | String | O | 컨테이너 프로토콜 |
| templates.containers.command | Body | String List | X | 컨테이너가 시작될 때 실행될 명령어 |
| templates.containers.env | Body | Array | X | 컨테이너 환경 변수 |
| templates.containers.env.name | Body | String | O | 컨테이너 환경 변수 이름 |
| templates.containers.env.value | Body | String | O | 컨테이너 환경 변수 값 |
| templates.containers.volumes | Body | Array | X | 컨테이너에서 사용하는 스토리지 정보 |
| templates.containers.volumes.name | Body | String | O | 스토리지 이름 |
| templates.containers.volumes.path | Body | String | O | NAS 스토리지 연결 경로 |
| templates.containers.volumes.mountPath | Body | String | X | 컨테이너 스토리지 연결 경로 |
| templates.containers.workDirectory | Body | String | X | 컨테이너의 작업 디렉터리 |
| templates.networks | Body | Array | O | 템플릿의 네트워크 정보 |
| templates.networks.vpcId | Body | String | O | 템플릿의 VPC ID |
| templates.networks.subnetId | Body | String | O | 템플릿의 Subnet ID |

<details><summary>예시</summary>

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

### 템플릿 보기

개별 템플릿 정보를 조회합니다.

```
GET /ncs/v1.0/appkeys/{appKey}/templates/{templateId}
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| templateId | URL | String | O | 템플릿 ID |

#### 응답

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| templates | Body | Object | O | 템플릿 정보 |
| templates.createdAt | Body | String | O | 생성 시간(UTC) |
| templates.id | Body | UUID | O | 템플릿 ID |
| templates.name | Body | String | O | 템플릿 이름 |
| templates.description | Body | String | X | 템플릿 설명 |
| templates.containers | Body | Array | O | 템플릿의 컨테이너 목록 |
| templates.containers.name | Body | String | O | 컨테이너 이름 |
| templates.containers.image | Body | String | O | 컨테이너 이미지 |
| templates.containers.cpus | Body | Float | O | 컨테이너에 할당하는 CPU 개수 |
| templates.containers.memoryLimit | Body | Object | O | 컨테이너에 할당하는 메모리 정보 |
| templates.containers.memoryLimit.hard | Body | Integer | O | 컨테이너에 할당하는 메모리 (MiB) |
| templates.containers.gpus | Body | Integer | X | 컨테이너의 GPU 사용 여부<br>0: 미사용<br>1: 사용 |
| templates.containers.gpuFlavor | Body | String | X | GPU Flavor 정보<br>\* ncs1.g1m5<br>\* ncs1.g2m10 |
| templates.containers.ports | Body | Array | X | 컨테이너에서 사용하는 포트 정보 |
| templates.containers.ports.containerPort | Body | Integer | O | 컨테이너 포트 |
| templates.containers.ports.protocol | Body | String | O | 컨테이너 프로토콜 |
| templates.containers.command | Body | String List | X | 컨테이너가 시작될 때 실행될 명령어 |
| templates.containers.env | Body | Array | X | 컨테이너 환경 변수 |
| templates.containers.env.name | Body | String | O | 컨테이너 환경 변수 이름 |
| templates.containers.env.value | Body | String | O | 컨테이너 환경 변수 값 |
| templates.containers.volumes | Body | Array | X | 컨테이너에서 사용하는 스토리지 정보 |
| templates.containers.volumes.name | Body | String | O | 스토리지 이름 |
| templates.containers.volumes.path | Body | String | O | NAS 스토리지 연결 경로 |
| templates.containers.volumes.mountPath | Body | String | X | 컨테이너 스토리지 연결 경로 |
| templates.containers.workDirectory | Body | String | X | 컨테이너의 작업 디렉터리 |
| templates.networks | Body | Array | O | 템플릿의 네트워크 정보 |
| templates.networks.vpcId | Body | String | O | 템플릿의 VPC ID |
| templates.networks.subnetId | Body | String | O | 템플릿의 Subnet ID |

<details><summary>예시</summary>

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

### 템플릿 생성하기

템플릿을 생성합니다.

```
POST /ncs/v1.0/appkeys/{appKey}/templates
Content-Type: application/json
```

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| templates | Body | Object | O | 템플릿 정보 |
| templates.name | Body | String | O | 템플릿 이름 |
| templates.description | Body | String | X | 템플릿 설명 |
| templates.containers | Body | Array | O | 템플릿의 컨테이너 목록 |
| templates.containers.name | Body | String | O | 컨테이너 이름 |
| templates.containers.image | Body | String | O | 컨테이너 이미지 |
| templates.containers.imageRegistryCredentials | Body | Object | X | Private 레지스트리에 접근 가능한 정보 |
| templates.containers.imageRegistryCredentials.username | Body | String | O | Private 레지스트리 아이디 |
| templates.containers.imageRegistryCredentials.password | Body | String | O | Private 레지스트리 비밀번호 |
| templates.containers.cpus | Body | Float | O | 컨테이너에 할당하는 CPU 개수 |
| templates.containers.memoryLimit | Body | Object | O | 컨테이너에 할당하는 메모리 정보 |
| templates.containers.memoryLimit.hard | Body | Integer | O | 컨테이너에 할당하는 메모리 (MiB) |
| templates.containers.gpus | Body | Integer | X | 컨테이너의 GPU 사용 여부<br>0: 미사용<br>1: 사용 |
| templates.containers.gpuFlavor | Body | String | X | GPU Flavor 정보<br>\* ncs1.g1m5<br>\* ncs1.g2m10 |
| templates.containers.ports | Body | Array | X | 컨테이너에서 사용하는 포트 정보 |
| templates.containers.ports.containerPort | Body | Integer | O | 컨테이너 포트 |
| templates.containers.ports.protocol | Body | String | O | 컨테이너 프로토콜 |
| templates.containers.command | Body | String List | X | 컨테이너가 시작될 때 실행될 명령어 |
| templates.containers.env | Body | Array | X | 컨테이너 환경 변수 |
| templates.containers.env.name | Body | String | O | 컨테이너 환경 변수 이름 |
| templates.containers.env.value | Body | String | O | 컨테이너 환경 변수 값 |
| templates.containers.volumes | Body | Array | X | 컨테이너에서 사용하는 스토리지 정보 |
| templates.containers.volumes.name | Body | String | O | 스토리지 이름 |
| templates.containers.volumes.path | Body | String | O | NAS 스토리지 연결 경로 |
| templates.containers.volumes.mountPath | Body | String | X | 컨테이너 스토리지 연결 경로 |
| templates.containers.workDirectory | Body | String | X | 컨테이너의 작업 디렉터리 |
| templates.networks | Body | Array | O | 템플릿의 네트워크 정보 |
| templates.networks.vpcId | Body | String | O | 템플릿의 VPC ID<br>VPC에 대한 자세한 내용은 [VPC 목록 보기](Network/VPC/ko/public-api/#vpc_1)를 참고하세요. |
| templates.networks.subnetId | Body | String | O | 템플릿의 Subnet ID<br>Subnet에 대한 자세한 내용은 [VPC 서브넷 목록 보기](/Network/VPC/ko/public-api/#vpc_7)를 참고하세요. |

<details><summary>예시</summary>

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

#### 응답

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| templates | Body | Object | O | 템플릿 정보 |
| templates.createdAt | Body | String | O | 생성 시간(UTC) |
| templates.id | Body | UUID | O | 템플릿 ID |
| templates.name | Body | String | O | 템플릿 이름 |
| templates.description | Body | String | X | 템플릿 설명 |
| templates.containers | Body | Array | O | 템플릿의 컨테이너 목록 |
| templates.containers.name | Body | String | O | 컨테이너 이름 |
| templates.containers.image | Body | String | O | 컨테이너 이미지 |
| templates.containers.cpus | Body | Float | O | 컨테이너에 할당하는 CPU 개수 |
| templates.containers.memoryLimit | Body | Object | O | 컨테이너에 할당하는 메모리 정보 |
| templates.containers.memoryLimit.hard | Body | Integer | O | 컨테이너에 할당하는 메모리 (MiB) |
| templates.containers.gpus | Body | Integer | X | 컨테이너의 GPU 사용 여부<br>0: 미사용<br>1: 사용 |
| templates.containers.gpuFlavor | Body | String | X | GPU Flavor 정보<br>\* ncs1.g1m5<br>\* ncs1.g2m10 |
| templates.containers.command | Body | String List | X | 컨테이너가 시작될 때 실행될 명령어 |
| templates.containers.env | Body | Array | X | 컨테이너 환경 변수 |
| templates.containers.env.name | Body | String | O | 컨테이너 환경 변수 이름 |
| templates.containers.env.value | Body | String | O | 컨테이너 환경 변수 값 |
| templates.containers.volumes | Body | Array | X | 컨테이너에서 사용하는 스토리지 정보 |
| templates.containers.volumes.name | Body | String | O | 스토리지 이름 |
| templates.containers.volumes.path | Body | String | O | NAS 스토리지 연결 경로 |
| templates.containers.volumes.mountPath | Body | String | X | 컨테이너 스토리지 연결 경로 |
| templates.containers.workDirectory | Body | String | X | 컨테이너의 작업 디렉터리 |
| templates.networks | Body | Array | O | 템플릿의 네트워크 정보 |
| templates.networks.vpcId | Body | String | O | 템플릿의 VPC ID |
| templates.networks.subnetId | Body | String | O | 템플릿의 Subnet ID |

<details><summary>예시</summary>

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

### 템플릿 삭제하기

템플릿을 삭제합니다.

```
DELETE /ncs/v1.0/appkeys/{appKey}/templates/{templateId}
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| templateId | URL | String | O | 템플릿 ID |

#### 응답

이 API는 공통 정보만 응답합니다.

## 워크로드

### 워크로드 목록 보기

워크로드 목록을 조회합니다.

```
GET /ncs/v1.0/appkeys/{appKey}/workloads?page=1&size=30
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| page | Query | Integer | X | 조회할 페이지 번호 |
| size | Query | Integer | X | 조회할 페이지 크기 (default: 10) |

#### 응답

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| X-Total-Count | Header | Integer | O | Appkey에 생성되어 있는 워크로드 전체 수 |
| workloads | Body | Array | O | 워크로드 목록 |
| workloads.createdAt | Body | String | O | 생성 시간(UTC) |
| workloads.id | Body | UUID | O | 워크로드 ID |
| workloads.name | Body | String | O | 워크로드 이름 |
| workloads.templateId | Body | String | O | 워크로드의 템플릿 ID |
| workloads.url | Body | String | X | 워크로드 로드밸런서 URL |
| workloads.desired | Body | Integer | O | 워크로드 작업 요청 수 |
| workloads.available | Body | Integer | O | 워크로드 작업 실행 수 |
| workloads.status | Body | Integer | O | 워크로드 상태 |
| workloads.loadBalancing | Body | Object | O | 워크로드 로드밸런서 정보 |
| workloads.loadBalancing.enabled | Body | Boolean | O | 워크로드 로드밸런서 사용 여부 |
| workloads.loadBalancing.floatingIp | Body | Boolean | O | 워크로드 로드밸런서 플로팅 IP 사용 여부 |

<details><summary>예시</summary>

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

### 워크로드 보기

개별 워크로드를 조회합니다.

```
GET /ncs/v1.0/appkeys/{appKey}/workloads/{workloadId}
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| workloadId | URL | String | O | 워크로드 ID |

#### 응답

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| X-Total-Count | Header | Integer | O | Appkey에 생성되어 있는 워크로드 전체 수 |
| workloads | Body | Array | O | 워크로드 목록 |
| workloads.createdAt | Body | String | O | 생성 시간(UTC) |
| workloads.id | Body | UUID | O | 워크로드 ID |
| workloads.name | Body | String | O | 워크로드 이름 |
| workloads.templateId | Body | String | O | 워크로드의 템플릿 ID |
| workloads.url | Body | String | X | 워크로드 로드밸런서 URL |
| workloads.desired | Body | Integer | O | 워크로드 작업 요청 수 |
| workloads.available | Body | Integer | O | 워크로드 작업 실행 수 |
| workloads.status | Body | String | O | 워크로드 상태 |
| workloads.tasks | Body | Array | O | 워크로드의 작업 목록 |
| workloads.tasks.id | Body | UUID | O | 작업 ID |
| workloads.tasks.containers | Body | Array | O | 작업의 컨테이너 목록 |
| workloads.tasks.containers.name | Body | String | O | 컨테이너 이름 |
| workloads.tasks.containers.image | Body | String | O | 컨테이너 이미지 |
| workloads.tasks.containers.ip | Body | String | X | 컨테이너 IP |
| workloads.tasks.containers.cpus | Body | Float | O | 컨테이너에 할당된 CPU 수 |
| workloads.tasks.containers.memoryLimit | Body | Object | O | 컨테이너에 할당된 메모리 정보 |
| workloads.tasks.containers.memoryLimit.hard | Body | Integer | O | 컨테이너에 할당된 메모리 (MiB) |
| workloads.tasks.containers.gpus | Body | Integer | X | 컨테이너의 GPU 사용 여부<br>0: 미사용<br>1: 사용 |
| workloads.tasks.containers.gpuFlavor | Body | String | X | 컨테이너에 할당된 GPU Flavor 정보 |
| workloads.tasks.containers.ports | Body | Array | X | 컨테이너의 포트 정보 |
| workloads.tasks.containers.ports.containerPort | Body | Integer | O | 컨테이너 포트 |
| workloads.tasks.containers.ports.protocol | Body | String | O | 컨테이너 프로토콜 |
| workloads.tasks.containers.command | Body | String List | X | 컨테이너가 시작될 때 실행될 명령어 |
| workloads.tasks.containers.env | Body | Array | X | 컨테이너 환경 변수 |
| workloads.tasks.containers.env.name | Body | String | O | 컨테이너 환경 변수 이름 |
| workloads.tasks.containers.env.value | Body | String | O | 컨테이너 환경 변수 값 |
| workloads.tasks.containers.volumes | Body | Array | X | 컨테이너에서 사용하는 스토리지 정보 |
| workloads.tasks.containers.volumes.name | Body | String | O | 스토리지 이름 |
| workloads.tasks.containers.volumes.path | Body | String | O | NAS 스토리지 연결 경로 |
| workloads.tasks.containers.volumes.mountPath | Body | String | X | 컨테이너 스토리지 연결 경로 |
| workloads.tasks.containers.workDirectory | Body | String | X | 컨테이너의 작업 디렉터리 |
| workloads.tasks.containers.state | Body | String | O | 컨테이너 상태 |
| workloads.tasks.containers.startedAt | Body | String | X | 컨테이너 시작 시간 |
| workloads.tasks.containers.restartCount | Body | String | O | 컨테이너 재시작 횟수 |
| workloads.loadBalancing | Body | Object | O | 워크로드 로드밸런서 정보 |
| workloads.loadBalancing.enabled | Body | Boolean | O | 워크로드 로드밸런서 사용 여부 |
| workloads.loadBalancing.floatingIp | Body | Boolean | O | 워크로드 로드밸런서 플로팅 IP 사용 여부 |
| workloads.loadBalancing.ipAddress | Body | String | X | 워크로드의 IP, 플로팅 IP |
| workloads.networks | Body | Array | O | 워크로드의 네트워크 정보 |
| workloads.networks.vpcId | Body | String | O | 워크로드의 VPC ID |
| workloads.networks.subnetId | Body | String | O | 워크로드의 Subnet ID |
| workloads.securityGroups | Body | String | X | 워크로드의 보안 그룹 정보 |
| workloads.securityGroups.id | Body | String | O | 워크로드의 보안 그룹 ID |

<details><summary>예시</summary>

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

### 워크로드 로그 보기

워크로드의 컨테이너 로그를 조회합니다.

```
GET /ncs/v1.0/appkeys/{appKey}/workloads/{workloadId}/tasks/{taskId}/logs?container={ContainerName}&from={YYYY-MM-DDThh:mm:ssZ}&to={YYYY-MM-DDThh:mm:ssZ}
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| container | Query | String | O | 컨테이너 이름 |
| limitBytes | Query | Integer | X | 조회하는 최대 로그 사이즈<br>default: 100 KB<br>최대 : 10 MB |
| from | Query | String | X | 로그 시작 시간<br>default: 현재로부터 5분전 |
| to | Query | String | X | 로그 종료 시간<br>default: 현재 시간 |

#### 응답

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| logs | Body | Array | O | 컨테이너 로그 목록 |
| log | Body | String | O | 로그 내용 |
| time | Body | String | O | 로그 발생 시간 (UTC) |

<details><summary>예시</summary>

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

### 워크로드 이벤트 보기

워크로드의 이벤트를 조회합니다.

```
GET /ncs/v1.0/appkeys/{appKey}/workloads/{workloadId}/tasks/{taskId}/events?page=1&size=30
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| type | Query | Integer | X | 이벤트 타입<br>Normal<br>Warning |
| q | Query | String | X | 이벤트 내용 필터링 |
| page | Query | String | X | 조회할 페이지 |
| size | Query | String | X | 조회할 페이지 크기 (default: 10) |

#### 응답

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| events | Body | Array | O | 이벤트 목록 |
| type | Body | String | O | 이벤트 타입<br>Normal<br>Warning |
| reason | Body | String | O | 이벤트 발생 원인 |
| message | Body | String | O | 이벤트 내용 |
| createTimestamp | Body | String | O | 이벤트 최초 발생 일시 (UTC) |
| lastTimestamp | Body | String | O | 이벤트 마지막 발생 일시 (UTC) |
| count | Body | Integer | O | 이벤트 발생 횟수 |

<details><summary>예시</summary>

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

### 워크로드 실행 히스토리 목록 보기

워크로드 실행 히스토리 목록을 조회합니다.

```
GET /ncs/v1.0/appkeys/{appKey}/workloads/{workloadId}/history?page=1&size=30
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| page | Query | Integer | X | 조회할 페이지 번호 |
| size | Query | Integer | X | 조회할 페이지 크기 (default: 10) |

#### 응답

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| history | Body | Array | O | 히스토리 목록 |
| history.id | Body | Integer | O | 히스토리 ID |
| history.createdAt | Body | String | O | 배포 시간 |
| history.deletedAt | Body | String | O | 종료 시간 |
| history.templateId | Body | UUID | O | 워크로드가 사용한 템플릿 ID |
| history.name | Body | String | O | 템플릿 이름 |
| history.status | Body | String | O | 상태<br>Succeeded<br>Terminated<br>Pending |

<details><summary>예시</summary>

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

### 워크로드 실행 히스토리 보기

개별 워크로드 실행 히스토리를 조회합니다.

```
GET /ncs/v1.0/appkeys/{appKey}/workloads/{workloadId}/history/{historyId}
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| historyId | URL | Integer | O | 히스토리 ID |

#### 응답

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| history | Body | Object | O | 히스토리 정보 |
| history.id | Body | Integer | O | 히스토리 ID |
| history.createdAt | Body | String | O | 실행 시간 |
| history.deletedAt | Body | String | O | 종료 시간 |
| history.templateId | Body | UUID | O | 워크로드가 사용한 템플릿 ID |
| history.name | Body | String | O | 워크로드가 사용한 템플릿 이름 |
| history.status | Body | String | O | 상태<br>Succeeded<br>Terminated<br>Pending |
| templates | Body | Object | O | 템플릿 정보 |
| templates.createdAt | Body | String | O | 템플릿 생성 시간(UTC) |
| templates.deletedAt | Body | String | O | 템플릿 삭제 시간(UTC) |
| templates.id | Body | UUID | O | 템플릿 ID |
| templates.name | Body | String | O | 템플릿 이름 |
| templates.description | Body | String | X | 템플릿 설명 |
| templates.containers | Body | Array | O | 템플릿의 컨테이너 목록 |
| templates.containers.name | Body | String | O | 컨테이너 이름 |
| templates.containers.image | Body | String | O | 컨테이너 이미지 |
| templates.containers.cpus | Body | Float | O | 컨테이너에 할당하는 CPU 개수 |
| templates.containers.memoryLimit | Body | Object | O | 컨테이너에 할당하는 메모리 정보 |
| templates.containers.memoryLimit.hard | Body | Integer | O | 컨테이너에 할당하는 메모리 (MiB) |
| templates.containers.gpus | Body | Integer | X | 컨테이너의 GPU 사용 여부<br>0: 미사용<br>1: 사용 |
| templates.containers.gpuFlavor | Body | String | X | 컨테이너에 할당된 GPU Flavor 정보 |
| templates.containers.ports | Body | Array | X | 컨테이너에서 사용하는 포트 정보 |
| templates.containers.ports.containerPort | Body | Integer | O | 컨테이너 포트 |
| templates.containers.ports.protocol | Body | String | O | 컨테이너 프로토콜 |
| templates.containers.command | Body | String List | X | 컨테이너가 시작될 때 실행될 명령어 |
| templates.containers.env | Body | Array | X | 컨테이너 환경 변수 |
| templates.containers.env.name | Body | String | O | 컨테이너 환경 변수 이름 |
| templates.containers.env.value | Body | String | O | 컨테이너 환경 변수 값 |
| templates.containers.volumes | Body | Array | X | 컨테이너에서 사용하는 스토리지 정보 |
| templates.containers.volumes.name | Body | String | O | 스토리지 이름 |
| templates.containers.volumes.path | Body | String | O | NAS 스토리지 연결 경로 |
| templates.containers.volumes.mountPath | Body | String | X | 컨테이너 스토리지 연결 경로 |
| templates.containers.workDirectory | Body | String | X | 컨테이너의 작업 디렉터리 |
| templates.networks | Body | Array | O | 템플릿의 네트워크 정보 |
| templates.networks.vpcId | Body | String | O | 템플릿의 VPC ID |
| templates.networks.subnetId | Body | String | O | 템플릿의 Subnet ID |

<details><summary>예시</summary>

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

### 워크로드 생성하기

워크로드를 생성합니다.

```
POST /ncs/v1.0/appkeys/{appKey}/workloads
Content-Type: application/json
```

#### 요청

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| workload | Body | Object | O | 워크로드 정보 |
| workload.desired | Body | Integer | O | 워크로드 작업 요청 수 |
| workload.loadBalancing | Body | Object | O | 워크로드 로드 밸런서 정보 |
| workload.loadBalancing.enabled | Body | Boolean | O | 워크로드 로드 밸런서 사용 여부 |
| workload.loadBalancing.floatingIp | Body | Boolean | O | 워크로드 로드 밸런서 플로팅 IP 사용 여부 |
| workload.name | Body | String | O | 워크로드 이름 |
| workload.templateId | Body | String | O | 워크로드의 템플릿 ID |

<details><summary>예시</summary>

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

#### 응답

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| workloads | Body | Object | O | 워크로드 정보 |
| workloads.createdAt | Body | String | O | 생성 시간(UTC) |
| workloads.id | Body | UUID | O | 워크로드 ID |
| workloads.name | Body | String | O | 워크로드 이름 |
| workloads.templateId | Body | String | O | 워크로드의 템플릿 ID |
| workloads.url | Body | String | X | 워크로드 로드밸런서 URL |
| workloads.desired | Body | Integer | O | 워크로드 작업 요청 수 |
| workloads.loadBalancing | Body | Object | O | 워크로드 로드밸런서 정보 |
| workloads.loadBalancing.enabled | Body | Boolean | O | 워크로드 로드밸런서 사용 여부 |
| workloads.loadBalancing.floatingIp | Body | Boolean | O | 워크로드 로드밸런서 플로팅 IP 사용 여부 |

<details><summary>예시</summary>

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

### 워크로드 변경하기

워크로드를 변경합니다.

```
PUT /ncs/v1.0/appkeys/{appKey}/workloads/{workloadId}
Content-Type: application/json
```

#### 요청

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| workloadId | URL | String | O | 워크로드 ID |
| workload.desired | Body | Integer | O | 워크로드 작업 요청 수 |
| workload.loadBalancing | Body | Object | O | 워크로드 로드 밸런서 정보 |
| workload.loadBalancing.enabled | Body | Boolean | O | 워크로드 로드 밸런서 사용 여부 |
| workload.loadBalancing.floatingIp | Body | Boolean | O | 워크로드 로드 밸런서 플로팅 IP 사용 여부 |
| workload.name | Body | String | O | 워크로드 이름 |
| workload.templateId | Body | String | O | 워크로드의 템플릿 ID |

<details><summary>예시</summary>

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

#### 응답

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| workloads | Body | Object | O | 워크로드 정보 |
| workloads.createdAt | Body | String | O | 생성 시간(UTC) |
| workloads.id | Body | UUID | O | 워크로드 ID |
| workloads.name | Body | String | O | 워크로드 이름 |
| workloads.templateId | Body | String | O | 워크로드의 템플릿 ID |
| workloads.url | Body | String | X | 워크로드 로드밸런서 URL |
| workloads.desired | Body | Integer | O | 워크로드 작업 요청 수 |
| workloads.loadBalancing | Body | Object | O | 워크로드 로드밸런서 정보 |
| workloads.loadBalancing.enabled | Body | Boolean | O | 워크로드 로드밸런서 사용 여부 |
| workloads.loadBalancing.floatingIp | Body | Boolean | O | 워크로드 로드밸런서 플로팅 IP 사용 여부 |

<details><summary>예시</summary>

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

### 워크로드 삭제하기

워크로드를 삭제합니다.

```
DELETE /ncs/v1.0/appkeys/{appKey}/workloads/{workloadId}
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| workloadId | URL | String | O | 워크로드 ID |

#### 응답

이 API는 공통 정보만 응답합니다.


## 응답 코드
| resultCode | resultMessage | 설명 |
| --- | --- | --- |
| 200 | SUCCESS | 요청 성공 |
| 10001 | Authentication error. | 유효하지 않은 AppKey 입니다. |
| 10002 | Bad Request. | 유효하지 않은 값이 요청 되었습니다. |
| 10003 | An error occurred while parsing the request body. | 요청 본문을 구문 분석하는 과정에서 오류가 발생했습니다. |
| 10004 | Internal server error. | 내부 서버 오류입니다. |
| 10006 | Quota exceeded for resource({{.Resource}}) | 생성 가능한 {{.Resource}} 개수를 초과하였습니다. |
| 10041 | Could not find the template. | 템플릿을 찾을 수 없습니다. |
| 10042 | Could not use the ECR. | ECR의 이미지는 사용할 수 없습니다. |
| 10043 | The network information does not exist. | 네트워크 정보가 존재하지 않습니다. |
| 10044 | The template in use by the workload cannot be deleted. | 워크로드에서 사용 중인 템플릿은 삭제할 수 없습니다. |
| 10045 | Duplicate container port exists in the template. | 템플릿에 동일한 컨테이너 포트가 존재합니다. |
| 10046 | Template with the same name already exists. | 동일한 이름의 템플릿이 이미 존재합니다. |
| 10047 | GPU Flavor({{.Flavor}}) Resources are not available. | {{.gpuFlavor}} 자원은 가용되지 않고 있습니다. |
| 10061 | Could not find the workload. | 워크로드를 찾을 수 없습니다. |
| 10062 | Pod does not exist. | Pod가 존재하지 않습니다. |
| 10063 | You cannot use the load balancer because the container port is not specified in the template. | 템플릿에 컨테이너 포트가 지정되지 않아 로드 밸런서를 사용할 수 없습니다. |
| 10064 | A workload with the same name already exists. | 동일한 이름의 워크로드가 이미 존재합니다. |
| 10065 | Invalid container specifications in the template. | 템플릿의 컨테이너 사양이 유효하지 않습니다. |
| 10066 | Could not create a workload due to insufficient resources. | 리소스가 부족하여 워크로드를 생성할 수 없습니다. |
| 10067 | You cannot change the workload name. | 워크로드 이름은 변경할 수 없습니다. |
| 10068 | You cannot change to the template that uses a different network. | 다른 네트워크를 사용하는 템플릿으로 변경할 수 없습니다. |
| 10069 | The container port being used by the load balancer is not specified in the template and cannot be changed. | 로드 밸런서에서 사용 중인 포트와 동일한 컨테이너 포트가 템플릿에 설정되어야 합니다. |