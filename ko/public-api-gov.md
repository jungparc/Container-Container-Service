## Container > NHN Container Service(NCS) > API 가이드

워크로드와 템플릿을 구성하기 위한 API를 기술합니다.
API를 사용하려면 API 엔드포인트와 토큰이 필요합니다. [API 호출 및 인증](/nhncloud/ko/public-api/api-authentication)을 참고하여 API 사용에 토큰을 준비합니다.

API 엔드포인트는 다음과 같습니다.

| 리전 | 도메인 |
| --- | --- |
| 한국(판교) 리전 | https://kr1-ncs.api.gov-nhncloudservice.com |

### API 응답 공통 정보

모든 API 요청에 <strong>200 OK</strong>로 응답합니다. 자세한 응답 결과는 응답 본문 헤더를 참고합니다.

| 이름 | 종류 | 형식 | 설명 |
| --- | --- | --- | --- |
| header | Body | Object | API 응답 정보 |
| header.isSuccessful | Body | Boolean | <li>true: 정상</li><li>false: 오류</li> |
| header.resultCode | Body | Integer | <li>200: 정상</li><li>10000 이상: 오류</li> |
| header.resultMessage | Body | String | <li>SUCCESS: 정상</li><li>그 외: 오류 원인 메시지</li> |

<details>
  <summary>예시</summary>

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

```bash
GET /ncs/v1.0/appkeys/{appKey}/templates
x-nhn-authorization: {token}
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|
| page | Query | Integer | X | 조회할 페이지 번호 |
| size | Query | Integer | X | 조회할 페이지 크기(default: 10) |
| disable\_containers | Query | Boolean | X | <li>true: 컨테이너는 제외하여 조회</li><li>false: 컨테이너도 포함하여 조회(default)</li> |

#### 응답

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| X-Total-Count | Header | Integer | O | Appkey에 생성되어 있는 템플릿 전체 수 |
| templates | Body | Array | O | 템플릿 목록 |
| templates.id | Body | UUID | O | 템플릿 ID |
| templates.version | Body | String | O | 템플릿 버전 |
| templates.name | Body | String | O | 템플릿 이름 |
| templates.createdAt | Body | String | O | 생성 시간(UTC) |
| templates.description | Body | String | X | 템플릿 설명 |
| templates.versionDescription | Body | String | O | 템플릿 버전 설명 |
| templates.networks | Body | Array | O | 템플릿의 네트워크 정보 |
| templates.networks.vpcId | Body | String | O | 템플릿의 VPC ID |
| templates.networks.subnetId | Body | String | O | 템플릿의 Subnet ID |
| templates.dnsConfig | Body | String List | X | 컨테이너에 설정된 DNS Server 정보 |
| templates.hostAliases | Body | List | X | 컨테이너 `/etc/hosts`에 설정된 정보 |
| templates.hostAliases.ip | Body | String | O | 컨테이너에 설정된 hostnames의 IP |
| templates.hostAliases.hostnames | Body | String List | O | 컨테이너에 설정된 IP의 hostnames |
| templates.versionCount | Body | Integer | O | 템플릿의 버전 개수 |
| templates.workloadCount | Body | Integer | O | 템플릿을 사용 중인 워크로드 개수 |
| templates.containers | Body | Array | O | 템플릿의 컨테이너 목록 |
| templates.containers.name | Body | String | O | 컨테이너 이름 |
| templates.containers.type | Body | String | O | 컨테이너 유형<ul><li>normal: 일반</li><li>init: 초기화</li></ul>|
| templates.containers.image | Body | String | O | 컨테이너 이미지 |
| templates.containers.cpus | Body | Float | O | 컨테이너에 할당하는 CPU 개수 |
| templates.containers.memoryLimit | Body | Object | O | 컨테이너에 할당하는 메모리 정보 |
| templates.containers.memoryLimit.hard | Body | Integer | O | 컨테이너에 할당하는 메모리(MiB) |
| templates.containers.ports | Body | Array | X | 컨테이너에서 사용하는 포트 정보 |
| templates.containers.ports.containerPort | Body | Integer | O | 컨테이너 포트 |
| templates.containers.ports.protocol | Body | String | O | 컨테이너 프로토콜<ul><li>TCP</li><li>UDP</li><li>HTTP</li><li>HTTPS</li><li>TERMINATED\_HTTPS</li></ul> |
| templates.containers.command | Body | String List | X | 컨테이너가 시작될 때 실행될 명령어 |
| templates.containers.args | Body | String List | X | 컨테이너가 시작될 때 사용되는 인자 |
| templates.containers.workDirectory | Body | String | X | 컨테이너의 작업 디렉터리 |
| templates.containers.env | Body | Array | X | 컨테이너 환경 변수 |
| templates.containers.env.name | Body | String | O | 컨테이너 환경 변수 이름 |
| templates.containers.env.value | Body | String | O | 컨테이너 환경 변수 값 |
| templates.containers.postStart | Body | String List | X | 컨테이너 생성 직후 실행되는 명령어 |
| templates.containers.preStop | Body | String List | X | 컨테이너 종료되기 직전 실행되는 명령어 |
| templates.containers.configs | Body | List | X | 컨테이너에서 사용하는 ConfigMap 정보 |
| templates.containers.configs.id | Body | Integer | O | ConfigMap ID |
| templates.containers.configs.type | Body | String | O | ConfigMap 정보를 가져오는 service type<ul><li>obs: Object Storage</li></ul> |
| templates.containers.configs.value | Body | String | O | 오브젝트 URL |
| templates.containers.configs.mountPath | Body | String | O | 컨테이너 마운트 경로 |
| templates.containers.secrets | Body | List | X | 컨테이너에서 사용하는 Secret 정보 |
| templates.containers.secrets.type | Body | String | O | Secret 정보를 가져오는 service type<ul><li>skm: Secure Key Manager</li></ul> |
| templates.containers.secrets.value | Body | String | O | 키 아이디 |
| templates.containers.secrets.mountPath | Body | String | O | 컨테이너 마운트 경로 |
| templates.containers.volumes | Body | Array | X | 컨테이너에서 사용하는 NAS 스토리지 정보 |
| templates.containers.volumes.name | Body | String | O | 스토리지 이름 |
| templates.containers.volumes.path | Body | String | O | NAS 스토리지 연결 경로 |
| templates.containers.volumes.mountPath | body | String | X | 컨테이너의 연결 경로 |
| templates.containers.probe | Body | List | X | 컨테이너 Probe 설정 |
| templates.containers.probe.type | Body | String | O | 컨테이너 Probe 타입<ul><li>startup</li><li>liveness</li></ul> |
| templates.containers.probe.failureThreshold | Body | Integer | O | Probe 실패 기준 |
| templates.containers.probe.initialDelaySeconds | Body | Integer | O | Probe 시작 대기 시간 |
| templates.containers.probe.periodSeconds | Body | Integer | O | Probe 실행 간격 |
| templates.containers.probe.timeoutSeconds | Body | Integer | O | Probe 실행 제한 시간 |
| templates.containers.probe.exec | Body | String List | O | Probe 실행 명령어 |
| templates.containers.stopTimeout | Body | Integer | X | 초기화 컨테이너 실행 제한 시간(초) |

<details>
  <summary>예시</summary>

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

### 템플릿 보기

개별 템플릿 정보를 조회합니다.

```bash
GET /ncs/v1.0/appkeys/{appKey}/templates/{templateId}
x-nhn-authorization: {token}
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| templateId | URL | String | O | 템플릿 ID |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|

#### 응답

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| template | Body | Object | O | 템플릿 정보 |
| template.id | Body | UUID | O | 템플릿 ID |
| template.version | Body | String | O | 템플릿 버전 |
| template.name | Body | String | O | 템플릿 이름 |
| template.createdAt | Body | String | O | 생성 시간(UTC) |
| template.description | Body | String | X | 템플릿 설명 |
| template.versionDescription | Body | String | X | 템플릿 버전 설명 |
| template.networks | Body | Array | O | 템플릿의 네트워크 정보 |
| template.networks.vpcId | Body | String | O | 템플릿의 VPC ID |
| template.networks.subnetId | Body | String | O | 템플릿의 Subnet ID |
| template.dnsConfig | Body | String List | X | 컨테이너에 설정된 DNS Server 정보 |
| template.hostAliases | Body | List | X | 컨테이너 `/etc/hosts`에 설정된 정보 |
| template.hostAliases.ip | Body | String | O | 컨테이너에 설정된 hostnames의 IP |
| template.hostAliases.hostnames | Body | String List | O | 컨테이너에 설정된 IP의 hostnames |
| template.versionCount | Body | Integer | O | 템플릿의 버전 개수 |
| template.workloadCount | Body | Integer | O | 템플릿을 사용 중인 워크로드 개수 |
| template.containers | Body | Array | O | 템플릿의 컨테이너 목록 |
| template.containers.name | Body | String | O | 컨테이너 이름 |
| template.containers.type | Body | String | O | 컨테이너 유형<ul><li>normal: 일반</li><li>init: 초기화</li></ul> |
| template.containers.image | Body | String | O | 컨테이너 이미지 |
| template.containers.cpus | Body | Float | O | 컨테이너에 할당하는 CPU 개수 |
| template.containers.memoryLimit | Body | Object | O | 컨테이너에 할당하는 메모리 정보 |
| template.containers.memoryLimit.hard | Body | Integer | O | 컨테이너에 할당하는 메모리(MiB) |
| template.containers.ports | Body | Array | X | 컨테이너에서 사용하는 포트 정보 |
| template.containers.ports.containerPort | Body | Integer | O | 컨테이너 포트 |
| template.containers.ports.protocol | Body | String | O | 컨테이너 프로토콜<ul><li>TCP</li><li>UDP</li><li>HTTP</li><li>HTTPS</li><li>TERMINATED\_HTTPS</li></ul> |
| template.containers.command | Body | String List | X | 컨테이너가 시작될 때 실행될 명령어 |
| template.containers.args | Body | String List | X | 컨테이너가 시작될 때 사용되는 인자 |
| template.containers.workDirectory | Body | String | X | 컨테이너의 작업 디렉터리 |
| template.containers.env | Body | Array | X | 컨테이너 환경 변수 |
| template.containers.env.name | Body | String | O | 컨테이너 환경 변수 이름 |
| template.containers.env.value | Body | String | O | 컨테이너 환경 변수 값 |
| template.containers.postStart | Body | String List | X | 컨테이너 생성 직후 실행되는 명령어 |
| template.containers.preStop | Body | String List | X | 컨테이너 종료 직전 실행되는 명령어 |
| template.containers.configs | Body | List | X | 컨테이너에서 사용하는 ConfigMap 정보 |
| template.containers.configs.id | Body | Integer | O | ConfigMap ID |
| template.containers.configs.type | Body | String | O | ConfigMap 정보를 가져오는 service type<ul><li>obs: Object Storage</li></ul> |
| template.containers.configs.value | Body | String | O | 오브젝트 URL |
| template.containers.configs.mountPath | Body | String | O | 컨테이너 마운트 경로 |
| template.containers.secrets | Body | List | X | 컨테이너에서 사용하는 Secret 정보 |
| template.containers.secrets.type | Body | String | O | Secret 정보를 가져오는 service type<ul><li>skm: Secure Key Manager</li></ul> |
| template.containers.secrets.value | Body | String | O | 키 아이디 |
| template.containers.secrets.mountPath | Body | String | O | 컨테이너 마운트 경로 |
| template.containers.volumes | Body | Array | X | 컨테이너에서 사용하는 NAS 스토리지 정보 |
| template.containers.volumes.name | Body | String | O | 스토리지 이름 |
| template.containers.volumes.path | Body | String | O | NAS 스토리지 연결 경로 |
| template.containers.volumes.mountPath | body | String | X | 컨테이너의 연결 경로 |
| template.containers.probe | Body | List | X | 컨테이너 Probe 설정 |
| template.containers.probe.type | Body | String | O | 컨테이너 Probe 타입<ul><li>startup</li><li>liveness</li></ul> |
| template.containers.probe.failureThreshold | Body | Integer | O | Probe 실패 기준 |
| template.containers.probe.initialDelaySeconds | Body | Integer | O | Probe 시작 대기 시간 |
| template.containers.probe.periodSeconds | Body | Integer | O | Probe 실행 간격 |
| template.containers.probe.timeoutSeconds | Body | Integer | O | Probe 실행 제한 시간 |
| template.containers.probe.exec | Body | String List | O | Probe 실행 명령어 |
| template.containers.stopTimeout | Body | Integer | X | 초기화 컨테이너 실행 제한 시간(초) |

<details>
  <summary>예시</summary>

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

### 템플릿 생성하기

템플릿을 생성합니다.

```bash
POST /ncs/v1.0/appkeys/{appKey}/templates
Content-Type: application/json
x-nhn-authorization: {token}
```

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|
| template | Body | Array | O | 템플릿 정보 |
| template.name | Body | String | O | 템플릿 이름 |
| template.version | Body | String | X | 템플릿 버전 |
| template.description | Body | String | X | 템플릿 설명 |
| template.versionDescription | Body | String | X | 템플릿 버전 설명 |
| template.networks | Body | Array | O | 템플릿의 네트워크 정보 |
| template.networks.vpcId | Body | String | O | 템플릿의 VPC ID |
| template.networks.subnetId | Body | String | O | 템플릿의 Subnet ID |
| template.dnsConfig | Body | String List | X | 컨테이너에서 사용하는 DNS Server 정보(최대 3개 설정 가능) |
| template.hostAliases | Body | List | X | 컨테이너 `/etc/hosts`에 설정하는 정보 |
| template.hostAliases.ip | Body | String | O | hostnames에서 사용하는 IP 정보 |
| template.hostAliases.hostnames | Body | String List | O | hostnames에서 사용하는 host 정보 |
| template.containers | Body | Array | O | 템플릿의 컨테이너 목록 |
| template.containers.name | Body | String | O | 컨테이너 이름 |
| template.containers.type | Body | String | X | 컨테이너 유형<ul><li>normal: 일반</li><li>init: 초기화</li></ul>|
| template.containers.image | Body | String | O | 컨테이너 이미지 |
| template.containers.imageRegistryCredentials | Body | Object | X | Private 레지스트리에 접근 가능한 정보 |
| template.containers.imageRegistryCredentials.username | Body | String | O | Private 레지스트리 아이디 |
| template.containers.imageRegistryCredentials.password | Body | String | O | Private 레지스트리 비밀번호 |
| template.containers.cpus | Body | Float | O | 컨테이너에 할당하는 CPU 개수 |
| template.containers.memoryLimit | Body | Object | O | 컨테이너에 할당하는 메모리 정보 |
| template.containers.memoryLimit.hard | Body | Integer | O | 컨테이너에 할당하는 메모리(MiB) |
| template.containers.ports | Body | Array | X | 컨테이너에서 사용하는 포트 정보 |
| template.containers.ports.containerPort | Body | Integer | O | 컨테이너 포트 |
| template.containers.ports.protocol | Body | String | O | 컨테이너 프로토콜<ul><li>TCP</li><li>UDP</li><li>HTTP</li><li>HTTPS</li><li>TERMINATED\_HTTPS</li></ul> |
| template.containers.command | Body | String List | X | 컨테이너가 시작될 때 실행될 명령어 |
| template.containers.args | Body | String List | X | 컨테이너가 시작될 때 사용되는 인자 |
| template.containers.workDirectory | Body | String | X | 컨테이너의 작업 디렉터리 |
| template.containers.env | Body | Array | X | 컨테이너 환경 변수 |
| template.containers.env.name | Body | String | O | 컨테이너 환경 변수 이름 |
| template.containers.env.value | Body | String | O | 컨테이너 환경 변수 값 |
| template.containers.postStart | Body | String List | X | 컨테이너 생성 직후 실행되는 명령어 |
| template.containers.preStop | Body | String List | X | 컨테이너 종료되기 직전 실행되는 명령어 |
| template.containers.configs | Body | List | X | 컨테이너에서 사용하는 ConfigMap 정보(최대 10개) |
| template.containers.configs.id | Body | Integer | O | ConfigMap ID |
| template.containers.configs.type | Body | String | O | ConfigMap 정보를 가져오는 service type<ul><li>obs: Object Storage</li></ul> |
| template.containers.configs.appKey | Body | String | O | Object Storage AppKey |
| template.containers.configs.userAccessKeyId | Body | String | O | Object Storage 서비스에 접근하는 사용자의 User Access Key |
| template.containers.configs.secretAccessKey | Body | String | O | Object Storage 서비스에 접근하는 사용자의 Secret Access Key |
| template.containers.configs.value | Body | String | O | 오브젝트 URL |
| template.containers.configs.mountPath | Body | String | O | 컨테이너 마운트 경로 |
| template.containers.secrets | Body | List | X | 컨테이너에서 사용하는 Secret 정보(최대 10개) |
| template.containers.secrets.type | Body | String | O | Secret 정보를 가져오는 service type<ul><li>skm: Secure Key Manager</li></ul> |
| template.containers.secrets.value | Body | String | O | 키 아이디 |
| template.containers.secrets.mountPath | Body | String | O | 컨테이너 마운트 경로 |
| template.containers.volumes | Body | Array | X | 컨테이너에서 사용하는 NAS 스토리지 정보 |
| template.containers.volumes.name | Body | String | O | 스토리지 이름 |
| template.containers.volumes.path | Body | String | O | NAS 스토리지 연결 경로 |
| template.containers.volumes.mountPath | body | String | X | 컨테이너의 연결 경로(default: /mnt) |
| template.containers.probe | Body | List | X | 컨테이너 Probe 설정 |
| template.containers.probe.type | Body | String | O | 컨테이너 Probe 타입<ul><li>startup</li><li>liveness</li></ul> |
| template.containers.probe.failureThreshold | Body | Integer | O | Probe 실패 기준 |
| template.containers.probe.initialDelaySeconds | Body | Integer | O | Probe 시작 대기 시간 |
| template.containers.probe.periodSeconds | Body | Integer | O | Probe 실행 간격<li>timeoutSeconds보다 작은 값이 설정되어야 합니다.</li> |
| template.containers.probe.timeoutSeconds | Body | Integer | O | Probe 실행 제한 시간<li>periodSeconds보다 큰 값이 설정되어야 합니다.</li> |
| template.containers.probe.exec | Body | String List | O | Probe 실행 명령어 |
| template.containers.stopTimeout | Body | Integer | X | 초기화 컨테이너 실행 제한 시간(초)<ul><li>30 ~ 120 (default: 30)</li></ul>|


<details>
  <summary>예시</summary>

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

#### 응답

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| template | Body | Object | O | 템플릿 정보 |
| template.id | Body | UUID | O | 템플릿 ID |
| template.name | Body | String | O | 템플릿 이름 |
| template.version | Body | String | O | 템플릿 버전 |
| template.createdAt | Body | String | O | 생성 시간(UTC) |
| template.description | Body | String | X | 템플릿 설명 |
| template.versionDescription | Body | String | X | 템플릿 버전 설명 |
| template.networks | Body | Array | O | 템플릿의 네트워크 정보 |
| template.networks.vpcId | Body | String | O | 템플릿의 VPC ID |
| template.networks.subnetId | Body | String | O | 템플릿의 Subnet ID |
| template.dnsConfig | Body | String List | X | 컨테이너에 설정된 DNS Server 정보 |
| template.hostAliases | Body | List | X | 컨테이너 `/etc/hosts`에 설정된 정보 |
| template.hostAliases.ip | Body | String | O | 컨테이너에 설정된 hostnames의 IP |
| template.hostAliases.hostnames | Body | String List | O | 컨테이너에 설정된 IP의 hostnames |
| template.containers | Body | Array | O | 템플릿의 컨테이너 목록 |
| template.containers.name | Body | String | O | 컨테이너 이름 |
| template.containers.type | Body | String | O | 컨테이너 유형<ul><li>normal: 일반</li><li>init: 초기화</li></ul>|
| template.containers.image | Body | String | O | 컨테이너 이미지 |
| template.containers.cpus | Body | Float | O | 컨테이너에 할당하는 CPU 개수 |
| template.containers.memoryLimit | Body | Object | O | 컨테이너에 할당하는 메모리 정보 |
| template.containers.memoryLimit.hard | Body | Integer | O | 컨테이너에 할당하는 메모리(MiB) |
| template.containers.ports | Body | Array | X | 컨테이너에서 사용하는 포트 정보 |
| template.containers.ports.containerPort | Body | Integer | O | 컨테이너 포트 |
| template.containers.ports.protocol | Body | String | O | 컨테이너 프로토콜<ul><li>TCP</li><li>UDP</li><li>HTTP</li><li>HTTPS</li><li>TERMINATED\_HTTPS</li></ul> |
| template.containers.command | Body | String List | X | 컨테이너가 시작될 때 실행될 명령어 |
| template.containers.args | Body | String List | X | 컨테이너가 시작될 때 사용되는 인자 |
| template.containers.workDirectory | Body | String | X | 컨테이너의 작업 디렉터리 |
| template.containers.env | Body | Array | X | 컨테이너 환경 변수 |
| template.containers.env.name | Body | String | O | 컨테이너 환경 변수 이름 |
| template.containers.env.value | Body | String | O | 컨테이너 환경 변수 값 |
| template.containers.postStart | Body | String List | X | 컨테이너 생성 직후 실행되는 명령어 |
| template.containers.preStop | Body | String List | X | 컨테이너 종료되기 직전 실행되는 명령어 |
| template.containers.configs | Body | List | X | 컨테이너에서 사용하는 ConfigMap 정보 |
| template.containers.configs.id | Body | Integer | O | ConfigMap ID |
| template.containers.configs.type | Body | String | O | ConfigMap 정보를 가져오는 service type<ul><li>obs: Object Storage</li></ul> |
| template.containers.configs.value | Body | String | O | 오브젝트 URL |
| template.containers.configs.mountPath | Body | String | O | 컨테이너 마운트 경로 |
| template.containers.secrets | Body | List | X | 컨테이너에서 사용하는 Secret 정보 |
| template.containers.secrets.type | Body | String | O | Secret 정보를 가져오는 service type<ul><li>skm: Secure Key Manager</li></ul> |
| template.containers.secrets.value | Body | String | O | 키 아이디 |
| template.containers.secrets.mountPath | Body | String | O | 컨테이너 마운트 경로 |
| template.containers.volumes | Body | Array | X | 컨테이너에서 사용하는 NAS 스토리지 정보 |
| template.containers.volumes.name | Body | String | O | 스토리지 이름 |
| template.containers.volumes.path | Body | String | O | NAS 스토리지 연결 경로 |
| template.containers.volumes.mountPath | body | String | X | 컨테이너의 연결 경로 |
| template.containers.probe | Body | List | X | 컨테이너 Probe 설정 |
| template.containers.probe.type | Body | String | O | 컨테이너 Probe 타입<ul><li>startup</li><li>liveness</li></ul> |
| template.containers.probe.failureThreshold | Body | Integer | O | Probe 실패 기준 |
| template.containers.probe.initialDelaySeconds | Body | Integer | O | Probe 시작 대기 시간 |
| template.containers.probe.periodSeconds | Body | Integer | O | Probe 실행 간격 |
| template.containers.probe.timeoutSeconds | Body | Integer | O | Probe 실행 제한 시간 |
| template.containers.probe.exec | Body | String List | O | Probe 실행 명령어 |
| template.containers.stopTimeout | Body | Integer | X | 초기화 컨테이너 실행 제한 시간(초) |

<details>
  <summary>예시</summary>

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

### 템플릿 삭제하기

템플릿을 삭제합니다.

```bash
DELETE /ncs/v1.0/appkeys/{appKey}/templates/{templateId}
x-nhn-authorization: {token}
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| templateId | URL | String | O | 템플릿 ID |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|

#### 응답

이 API는 공통 정보만 응답합니다.

### 템플릿 버전 목록 보기

```bash
GET /ncs/v1.0/appkeys/{appKey}/templates/{templateId}/versions
x-nhn-authorization: {token}
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| templateId | Path | String | O | 템플릿 ID |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|
| q | Query | String | X | 검색 매개변수 |
| page | Query | Integer | X | 조회할 페이지 번호 |
| size | Query | Integer | X | 조회할 페이지 크기(default: 10) |
| sort | Query | String | X | 정렬 기준이 될 필드명<br>역순 정렬일 경우 필드명 앞에 `-`를 붙임<br>예: `sort=-name` |

#### 응답

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| X-Total-Count | Header | Integer | O | 템플릿의 버전 개수 |
| templates | Body | Array | O | 템플릿 버전 목록 |
| templates.id | Body | UUID | O | 템플릿 ID |
| templates.version | Body | String | O | 템플릿 버전 |
| templates.sourceVersion | Body | String | O | 템플릿 기준 버전 |
| templates.name | Body | String | O | 템플릿 이름 |
| templates.createdAt | Body | String | O | 생성 시간(UTC) |
| templates.description | Body | String | X | 템플릿 설명 |
| templates.versionDescription | Body | String | X | 템플릿 버전 설명 |
| templates.networks | Body | Array | O | 템플릿의 네트워크 정보 |
| templates.networks.vpcId | Body | String | O | 템플릿의 VPC ID |
| templates.networks.subnetId | Body | String | O | 템플릿의 Subnet ID |
| templates.dnsConfig | Body | String List | X | 컨테이너에 설정된 DNS Server 정보 |
| templates.hostAliases | Body | List | X | 컨테이너 `/etc/hosts`에 설정된 정보 |
| templates.hostAliases.ip | Body | String | O | 컨테이너에 설정된 hostnames의 IP |
| templates.hostAliases.hostnames | Body | String List | O | 컨테이너에 설정된 IP의 hostnames |
| templates.versionCount | Body | Integer | O | 템플릿의 버전 개수 |
| templates.workloadCount | Body | Integer | O | 템플릿을 사용 중인 워크로드 개수 |
| templates.containers | Body | Array | O | 템플릿의 컨테이너 목록 |
| templates.containers.name | Body | String | O | 컨테이너 이름 |
| templates.containers.type | Body | String | O | 컨테이너 유형<ul><li>normal: 일반</li><li>init: 초기화</li></ul>|
| templates.containers.image | Body | String | O | 컨테이너 이미지 |
| templates.containers.cpus | Body | Float | O | 컨테이너에 할당하는 CPU 개수 |
| templates.containers.memoryLimit | Body | Object | O | 컨테이너에 할당하는 메모리 정보 |
| templates.containers.memoryLimit.hard | Body | Integer | O | 컨테이너에 할당하는 메모리(MiB) |
| templates.containers.ports | Body | Array | X | 컨테이너에서 사용하는 포트 정보 |
| templates.containers.ports.containerPort | Body | Integer | O | 컨테이너 포트 |
| template.containers.ports.protocol | Body | String | O | 컨테이너 프로토콜<ul><li>TCP</li><li>UDP</li><li>HTTP</li><li>HTTPS</li><li>TERMINATED\_HTTPS</li></ul> |
| templates.containers.command | Body | String List | X | 컨테이너가 시작될 때 실행될 명령어 |
| templates.containers.args | Body | String List | X | 컨테이너가 시작될 때 사용되는 인자 |
| templates.containers.workDirectory | Body | String | X | 컨테이너의 작업 디렉터리 |
| templates.containers.env | Body | Array | X | 컨테이너 환경 변수 |
| templates.containers.env.name | Body | String | O | 컨테이너 환경 변수 이름 |
| templates.containers.env.value | Body | String | O | 컨테이너 환경 변수 값 |
| templates.containers.postStart | Body | String List | X | 컨테이너 생성 직후 실행되는 명령어 |
| templates.containers.preStop | Body | String List | X | 컨테이너 종료되기 직전 실행되는 명령어 |
| templates.containers.configs | Body | List | X | 컨테이너에서 사용하는 ConfigMap 정보 |
| templates.containers.configs.id | Body | Integer | O | ConfigMap ID |
| templates.containers.configs.type | Body | String | O | ConfigMap 정보를 가져오는 service type<ul><li>obs: Object Storage</li></ul> |
| templates.containers.configs.value | Body | String | O | 오브젝트 URL |
| templates.containers.configs.mountPath | Body | String | O | 컨테이너 마운트 경로 |
| templates.containers.secrets | Body | List | X | 컨테이너에서 사용하는 Secret 정보 |
| templates.containers.secrets.type | Body | String | O | Secret 정보를 가져오는 service type<ul><li>skm: Secure Key Manager</li></ul> |
| templates.containers.secrets.value | Body | String | O | 키 아이디 |
| templates.containers.secrets.mountPath | Body | String | O | 컨테이너 마운트 경로 |
| templates.containers.volumes | Body | Array | X | 컨테이너에서 사용하는 NAS 스토리지 정보 |
| templates.containers.volumes.name | Body | String | O | 스토리지 이름 |
| templates.containers.volumes.path | Body | String | O | NAS 스토리지 연결 경로 |
| templates.containers.volumes.mountPath | body | String | X | 컨테이너의 연결 경로 |
| templates.containers.probe | Body | List | X | 컨테이너 Probe 설정 |
| templates.containers.probe.type | Body | String | O | 컨테이너 Probe 타입<ul><li>startup</li><li>liveness</li></ul> |
| templates.containers.probe.failureThreshold | Body | Integer | O | Probe 실패 기준 |
| templates.containers.probe.initialDelaySeconds | Body | Integer | O | Probe 시작 대기 시간 |
| templates.containers.probe.periodSeconds | Body | Integer | O | Probe 실행 간격 |
| templates.containers.probe.timeoutSeconds | Body | Integer | O | Probe 실행 제한 시간 |
| templates.containers.probe.exec | Body | String List | O | Probe 실행 명령어 |
| templates.containers.stopTimeout | Body | Integer | X | 초기화 컨테이너 실행 제한 시간(초) |

<details>
  <summary>예시</summary>

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

### 템플릿 버전 보기

개별 템플릿 버전 정보를 조회합니다.

```bash
GET /ncs/v1.0/appkeys/{appKey}/templates/{templateId}/versions/{version}
x-nhn-authorization: {token}
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| templateId | URL | String | O | 템플릿 ID |
| version | URL | String | O | 템플릿 버전 |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|

#### 응답

* 템플릿 상세 조회와 동일

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| template | Body | Object | O | 템플릿 버전 정보 |
| template.id | Body | UUID | O | 템플릿 ID |
| template.version | Body | String | O | 템플릿 버전 |
| template.sourceVersion | Body | String | O | 템플릿 기준 버전 |
| template.name | Body | String | O | 템플릿 이름 |
| template.createdAt | Body | String | O | 생성 시간(UTC) |
| template.description | Body | String | X | 템플릿 설명 |
| template.versionDescription | Body | String | X | 템플릿 버전 설명 |
| template.networks | Body | Array | O | 템플릿의 네트워크 정보 |
| template.networks.vpcId | Body | String | O | 템플릿의 VPC ID |
| template.networks.subnetId | Body | String | O | 템플릿의 Subnet ID |
| template.dnsConfig | Body | String List | X | 컨테이너에 설정된 DNS Server 정보 |
| template.hostAliases | Body | List | X | 컨테이너 `/etc/hosts`에 설정된 정보 |
| template.hostAliases.ip | Body | String | O | 컨테이너에 설정된 hostnames의 IP |
| template.hostAliases.hostnames | Body | String List | O | 컨테이너에 설정된 IP의 hostnames |
| template.versionCount | Body | Integer | O | 템플릿의 버전 개수 |
| template.workloadCount | Body | Integer | O | 템플릿을 사용 중인 워크로드 개수 |
| template.containers | Body | Array | O | 템플릿의 컨테이너 목록 |
| template.containers.name | Body | String | O | 컨테이너 이름 |
| template.containers.type | Body | String | O | 컨테이너 유형<ul><li>normal: 일반</li><li>init: 초기화</li></ul>|
| template.containers.image | Body | String | O | 컨테이너 이미지 |
| template.containers.cpus | Body | Float | O | 컨테이너에 할당하는 CPU 개수 |
| template.containers.memoryLimit | Body | Object | O | 컨테이너에 할당하는 메모리 정보 |
| template.containers.memoryLimit.hard | Body | Integer | O | 컨테이너에 할당하는 메모리(MiB) |
| template.containers.ports | Body | Array | X | 컨테이너에서 사용하는 포트 정보 |
| template.containers.ports.containerPort | Body | Integer | O | 컨테이너 포트 |
| template.containers.ports.protocol | Body | String | O | 컨테이너 프로토콜<ul><li>TCP</li><li>UDP</li><li>HTTP</li><li>HTTPS</li><li>TERMINATED\_HTTPS</li></ul> |
| template.containers.command | Body | String List | X | 컨테이너가 시작될 때 실행될 명령어 |
| template.containers.args | Body | String List | X | 컨테이너가 시작될 때 사용되는 인자 |
| template.containers.workDirectory | Body | String | X | 컨테이너의 작업 디렉터리 |
| template.containers.env | Body | Array | X | 컨테이너 환경 변수 |
| template.containers.env.name | Body | String | O | 컨테이너 환경 변수 이름 |
| template.containers.env.value | Body | String | O | 컨테이너 환경 변수 값 |
| template.containers.postStart | Body | String List | X | 컨테이너 생성 직후 실행되는 명령어 |
| template.containers.preStop | Body | String List | X | 컨테이너 종료되기 직전 실행되는 명령어 |
| template.containers.configs | Body | List | X | 컨테이너에서 사용하는 ConfigMap 정보 |
| template.containers.configs.id | Body | Integer | O | ConfigMap ID |
| template.containers.configs.type | Body | String | O | ConfigMap 정보를 가져오는 service type<ul><li>obs: Object Storage</li></ul> |
| template.containers.configs.value | Body | String | O | 오브젝트 URL |
| template.containers.configs.mountPath | Body | String | O | 컨테이너 마운트 경로 |
| template.containers.secrets | Body | List | X | 컨테이너에서 사용하는 Secret 정보 |
| template.containers.secrets.type | Body | String | O | Secret 정보를 가져오는 service type<ul><li>skm: Secure Key Manager</li></ul> |
| template.containers.secrets.value | Body | String | O | 키 아이디 |
| template.containers.secrets.mountPath | Body | String | O | 컨테이너 마운트 경로 |
| template.containers.volumes | Body | Array | X | 컨테이너에서 사용하는 NAS 스토리지 정보 |
| template.containers.volumes.name | Body | String | O | 스토리지 이름 |
| template.containers.volumes.path | Body | String | O | NAS 스토리지 연결 경로 |
| template.containers.volumes.mountPath | body | String | X | 컨테이너의 연결 경로 |
| template.containers.probe | Body | List | X | 컨테이너 Probe 설정 |
| template.containers.probe.type | Body | String | O | 컨테이너 Probe 타입<ul><li>startup</li><li>liveness</li></ul> |
| template.containers.probe.failureThreshold | Body | Integer | O | Probe 실패 기준 |
| template.containers.probe.initialDelaySeconds | Body | Integer | O | Probe 시작 대기 시간 |
| template.containers.probe.periodSeconds | Body | Integer | O | Probe 실행 간격 |
| template.containers.probe.timeoutSeconds | Body | Integer | O | Probe 실행 제한 시간 |
| template.containers.probe.exec | Body | String List | O | Probe 실행 명령어 |
| template.containers.stopTimeout | Body | Integer | X | 초기화 컨테이너 실행 제한 시간(초) |

<details>
  <summary>예시</summary>

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

### 템플릿 버전 생성

템플릿 버전을 생성합니다.

```bash
POST /ncs/v1.0/appkeys/{appKey}/templates/{templateId}/versions
x-nhn-authorization: {token}
```

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| templateId | URL | String | O | 템플릿 ID |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|
| template | Body | Object | O | 템플릿 버전 정보 |
| template.version | Body | String | O | 템플릿 버전 |
| template.sourceVersion | Body | String | O | 템플릿 기준 버전 |
| template.name | Body | String | O | 템플릿 이름 |
| template.versionDescription | Body | String | X | 템플릿 버전 설명 |
| template.dnsConfig | Body | String List | X | 컨테이너에 설정된 DNS Server 정보 |
| template.hostAliases | Body | List | X | 컨테이너 `/etc/hosts`에 설정된 정보 |
| template.hostAliases.ip | Body | String | O | 컨테이너에 설정된 hostnames의 IP |
| template.hostAliases.hostnames | Body | String List | O | 컨테이너에 설정된 IP의 hostnames |
| template.applyImmediately | Body | Boolean | X | true: 즉시 배포 사용, false: 즉시 배포 사용 안 함(default: false) |
| template.containers | Body | Array | O | 템플릿의 컨테이너 목록 |
| template.containers.name | Body | String | O | 컨테이너 이름 |
| template.containers.type | Body | String | O | 컨테이너 유형<ul><li>normal: 일반</li><li>init: 초기화</li></ul>|
| template.containers.image | Body | String | O | 컨테이너 이미지 |
| template.containers.imageRegistryCredentials | Body | Object | X | Private 레지스트리에 접근 가능한 정보 |
| template.containers.imageRegistryCredentials.changed | Body | Boolean | X | 기존 계정 사용 여부(default: false)<ul><li>false: 기존 계정 사용</li><li>true: 신규 계정 사용(username, password가 필수로 전달되어야 함)</li></ul> |
| template.containers.imageRegistryCredentials.username | Body | String | O | Private 레지스트리 아이디 |
| template.containers.imageRegistryCredentials.password | Body | String | O | Private 레지스트리 비밀번호 |
| template.containers.cpus | Body | Float | O | 컨테이너에 할당하는 CPU 개수 |
| template.containers.memoryLimit | Body | Object | O | 컨테이너에 할당하는 메모리 정보 |
| template.containers.memoryLimit.hard | Body | Integer | O | 컨테이너에 할당하는 메모리(MiB) |
| template.containers.ports | Body | Array | X | 컨테이너에서 사용하는 포트 정보 |
| template.containers.ports.containerPort | Body | Integer | O | 컨테이너 포트 |
| template.containers.ports.protocol | Body | String | O | 컨테이너 프로토콜<ul><li>TCP</li><li>UDP</li><li>HTTP</li><li>HTTPS</li><li>TERMINATED\_HTTPS</li></ul> |
| template.containers.command | Body | String List | X | 컨테이너가 시작될 때 실행될 명령어 |
| template.containers.args | Body | String List | X | 컨테이너가 시작될 때 사용되는 인자 |
| template.containers.workDirectory | Body | String | X | 컨테이너의 작업 디렉터리 |
| template.containers.env | Body | Array | X | 컨테이너 환경 변수 |
| template.containers.env.name | Body | String | O | 컨테이너 환경 변수 이름 |
| template.containers.env.value | Body | String | O | 컨테이너 환경 변수 값 |
| template.containers.postStart | Body | String List | X | 컨테이너 생성 직후 실행되는 명령어 |
| template.containers.preStop | Body | String List | X | 컨테이너 종료 직전 실행되는 명령어 |
| template.containers.configs | Body | List | X | 컨테이너에서 사용하는 ConfigMap 정보 |
| template.containers.configs.id | Body | Integer | O | ConfigMap ID |
| template.containers.configs.type | Body | String | O | ConfigMap 정보를 가져오는 service type<ul><li>obs: Object Storage</li></ul> |
| template.containers.configs.value | Body | String | O | 오브젝트 URL |
| template.containers.configs.mountPath | Body | String | O | 컨테이너 마운트 경로 |
| template.containers.configs.changedAppKey | Body | Boolean | X | 기존 AppKey 사용 여부(default: false)<ul><li>false: 기존 key 사용</li><li>true: 신규 key 사용(AppKey가 필수로 전달되어야 함)</li></ul> |
| template.containers.configs.appKey | Body | String | X | Object Storage AppKey |
| template.containers.configs.changedUserAccessKeyId | Body | Boolean | X | 기존 UserAccessKeyId 사용 여부(default: false)<ul><li>false: 기존 key 사용</li><li>true: 신규 key 사용(UserAccessKeyId가 필수로 전달되어야 함)</li></ul> |
| template.containers.configs.userAccessKeyId | Body | String | X | Object Storage 서비스에 접근하는 사용자의 User Access Key |
| template.containers.configs.changedSecretAccessKey | Body | Boolean | X | 기존 SecretAccessKey 사용 여부(default: false)<ul><li>false: 기존 key 사용</li><li>true: 신규 key 사용(SecretAccessKey가 필수로 전달되어야 함)</li></ul> |
| template.containers.configs.secretAccessKey | Body | String | X | Object Storage 서비스에 접근하는 사용자의 Secret Access Key |
| template.containers.secrets | Body | List | X | 컨테이너에서 사용하는 Secret 정보 |
| template.containers.secrets.type | Body | String | O | Secret 정보를 가져오는 service type<ul><li>skm: Secure Key Manager</li></ul> |
| template.containers.secrets.value | Body | String | O | 키 아이디 |
| template.containers.secrets.mountPath | Body | String | O | 컨테이너 마운트 경로 |
| template.containers.volumes | Body | Array | X | 컨테이너에서 사용하는 NAS 스토리지 정보 |
| template.containers.volumes.name | Body | String | O | 스토리지 이름 |
| template.containers.volumes.path | Body | String | O | NAS 스토리지 연결 경로 |
| template.containers.volumes.mountPath | body | String | X | 컨테이너의 연결 경로 |
| template.containers.probe | Body | List | X | 컨테이너 Probe 설정 |
| template.containers.probe.type | Body | String | O | 컨테이너 Probe 타입<ul><li>startup</li><li>liveness</li></ul> |
| template.containers.probe.failureThreshold | Body | Integer | O | Probe 실패 기준 |
| template.containers.probe.initialDelaySeconds | Body | Integer | O | Probe 시작 대기 시간 |
| template.containers.probe.periodSeconds | Body | Integer | O | Probe 실행 간격 |
| template.containers.probe.timeoutSeconds | Body | Integer | O | Probe 실행 제한 시간 |
| template.containers.probe.exec | Body | String List | O | Probe 실행 명령어 |
| template.containers.stopTimeout | Body | Integer | X | 초기화 컨테이너 실행 제한 시간(초) |

<details>
  <summary>예시</summary>

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

#### 응답

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| template | Body | Object | O | 템플릿 정보 |
| template.id | Body | UUID | O | 템플릿 ID |
| template.version | Body | String | O | 템플릿 버전 |
| template.sourceVersion | Body | String | O | 템플릿 기준 버전 |
| template.name | Body | String | O | 템플릿 이름 |
| template.createdAt | Body | String | O | 생성 시간(UTC) |
| template.description | Body | String | X | 템플릿 설명 |
| template.versionDescription | Body | String | X | 템플릿 버전 설명 |
| template.networks | Body | Array | O | 템플릿의 네트워크 정보 |
| template.networks.vpcId | Body | String | O | 템플릿의 VPC ID |
| template.networks.subnetId | Body | String | O | 템플릿의 Subnet ID |
| template.dnsConfig | Body | String List | X | 컨테이너에 설정된 DNS Server 정보 |
| template.hostAliases | Body | List | X | 컨테이너 `/etc/hosts`에 설정된 정보 |
| template.hostAliases.ip | Body | String | O | 컨테이너에 설정된 hostnames의 IP |
| template.hostAliases.hostnames | Body | String List | O | 컨테이너에 설정된 IP의 hostnames |
| template.containers | Body | Array | O | 템플릿의 컨테이너 목록 |
| template.containers.name | Body | String | O | 컨테이너 이름 |
| template.containers.type | Body | String | O | 컨테이너 유형<ul><li>normal: 일반</li><li>init: 초기화</li></ul>|
| template.containers.image | Body | String | O | 컨테이너 이미지 |
| template.containers.cpus | Body | Float | O | 컨테이너에 할당하는 CPU 개수 |
| template.containers.memoryLimit | Body | Object | O | 컨테이너에 할당하는 메모리 정보 |
| template.containers.memoryLimit.hard | Body | Integer | O | 컨테이너에 할당하는 메모리(MiB) |
| template.containers.ports | Body | Array | X | 컨테이너에서 사용하는 포트 정보 |
| template.containers.ports.containerPort | Body | Integer | O | 컨테이너 포트 |
| template.containers.ports.protocol | Body | String | O | 컨테이너 프로토콜<ul><li>TCP</li><li>UDP</li><li>HTTP</li><li>HTTPS</li><li>TERMINATED\_HTTPS</li></ul> |
| template.containers.command | Body | String List | X | 컨테이너가 시작될 때 실행될 명령어 |
| template.containers.args | Body | String List | X | 컨테이너가 시작될 때 사용되는 인자 |
| template.containers.workDirectory | Body | String | X | 컨테이너의 작업 디렉터리 |
| template.containers.env | Body | Array | X | 컨테이너 환경 변수 |
| template.containers.env.name | Body | String | O | 컨테이너 환경 변수 이름 |
| template.containers.env.value | Body | String | O | 컨테이너 환경 변수 값 |
| template.containers.postStart | Body | String List | X | 컨테이너 생성 직후 실행되는 명령어 |
| template.containers.preStop | Body | String List | X | 컨테이너 종료 직전 실행되는 명령어 |
| template.containers.configs | Body | List | X | 컨테이너에서 사용하는 ConfigMap 정보 |
| template.containers.configs.id | Body | Integer | O | ConfigMap ID |
| template.containers.configs.type | Body | String | O | ConfigMap 정보를 가져오는 service type<ul><li>obs: Object Storage</li></ul> |
| template.containers.configs.value | Body | String | O | 오브젝트 URL |
| template.containers.configs.mountPath | Body | String | O | 컨테이너 마운트 경로 |
| template.containers.secrets | Body | List | X | 컨테이너에서 사용하는 Secret 정보 |
| template.containers.secrets.type | Body | String | O | Secret 정보를 가져오는 service type<ul><li>skm: Secure Key Manager</li></ul> |
| template.containers.secrets.value | Body | String | O | 키 아이디 |
| template.containers.secrets.mountPath | Body | String | O | 컨테이너 마운트 경로 |
| template.containers.volumes | Body | Array | X | 컨테이너에서 사용하는 NAS 스토리지 정보 |
| template.containers.volumes.name | Body | String | O | 스토리지 이름 |
| template.containers.volumes.path | Body | String | O | NAS 스토리지 연결 경로 |
| template.containers.volumes.mountPath | body | String | X | 컨테이너의 연결 경로 |
| template.containers.probe | Body | List | X | 컨테이너 Probe 설정 |
| template.containers.probe.type | Body | String | O | 컨테이너 Probe 타입<ul><li>startup</li><li>liveness</li></ul> |
| template.containers.probe.failureThreshold | Body | Integer | O | Probe 실패 기준 |
| template.containers.probe.initialDelaySeconds | Body | Integer | O | Probe 시작 대기 시간 |
| template.containers.probe.periodSeconds | Body | Integer | O | Probe 실행 간격 |
| template.containers.probe.timeoutSeconds | Body | Integer | O | Probe 실행 제한 시간 |
| template.containers.probe.exec | Body | String List | O | Probe 실행 명령어 |
| template.containers.stopTimeout | Body | Integer | X | 초기화 컨테이너 실행 제한 시간(초) |

<details>
  <summary>예시</summary>

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

### 템플릿 버전 삭제

```bash
DELETE /ncs/v1.0/appkeys/{appkey}/templates/{templateId}/versions/{version}
x-nhn-authorization: {token}
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| templateId | URL | String | O | 템플릿 ID |
| version | URL | String | O | 템플릿 버전 |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|

#### 응답

이 API는 공통 정보만 응답합니다.

## 워크로드

### 워크로드 목록 보기

워크로드 목록을 조회합니다.

```bash
GET /ncs/v1.0/appkeys/{appKey}/workloads
x-nhn-authorization: {token}
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|
| q | Query | String | X | 워크로드 이름과 템플릿 ID, 템플릿 버전으로 필터링<ul>예:<li>q=templateId=${템플릿 ID}</li><li>q=${워크로드 이름)</li><li>q=templateId=${템플릿 ID}\&version=${템플릿 버전}</li></ul> |
| page | Query | Integer | X | 조회할 페이지 번호 |
| size | Query | Integer | X | 조회할 페이지 크기(default: 10) |

#### 응답

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| X-Total-Count | Header | Integer | O | Appkey에 생성되어 있는 워크로드 전체 수 |
| workloads | Body | Array | O | 워크로드 목록 |
| workloads.id | Body | UUID | O | 워크로드 ID |
| workloads.name | Body | String | O | 워크로드 이름 |
| workloads.type | Body | String | O | 배포 컨트롤러<ul><li>deployment</li><li>statefulset</li></ul> |
| workloads.templateId | Body | String | O | 워크로드의 템플릿 ID |
| workloads.templateVersion | Body | String | O | 워크로드의 템플릿 버전 |
| workloads.createdAt | Body | String | O | 생성 시간(UTC) |
| workloads.desired | Body | Integer | O | 워크로드 작업 요청 수 |
| workloads.available | Body | Integer | O | 워크로드 작업 실행 수 |
| workloads.status | Body | Integer | O | 워크로드 상태 |
| workloads.url | Body | String | X | 워크로드 로드 밸런서 URL |
| workloads.loadBalancing | Body | Object | O | 워크로드 로드 밸런서 정보 |
| workloads.loadBalancing.enabled | Body | Boolean | O | 워크로드 로드 밸런서 사용 여부 |
| workloads.loadBalancing.floatingIp | Body | Boolean | O | 워크로드 로드 밸런서 플로팅 IP 사용 여부 |
| workloads.loadBalancing.healthMonitor | Body | Object | X | 로드 밸런서의 상태 확인 정보 |
| workloads.loadBalancing.healthMonitor.delay | Body | Integer | O | 상태 확인 주기 |
| workloads.loadBalancing.healthMonitor.timeout | Body | Integer | O | 최대 응답 대기 시간 |
| workloads.loadBalancing.healthMonitor.maxRetries | Body | Integer | O | 최대 재시도 횟수 |
| workloads.loadBalancing.healthMonitor.httpMethod | Body | String | X | HTTP 메서드<ul><li>GET</li></ul> |
| workloads.loadBalancing.healthMonitor.expectedCodes | Body | String | X | HTTP 상태 코드<ul><li>200</li><li>200,202</li><li>200-204</li></ul> |
| workloads.loadBalancing.healthMonitor.urlPath | Body | String | X | HTTP URL |
| workloads.loadBalancing.certificate | Body | String | X | TERMINATED\_HTTPS 사용 시 로드 밸런서에서 사용하는 인증서 |
| workloads.loadBalancing.privateKey | Body | String | X | TERMINATED\_HTTPS 사용 시 로드 밸런서에서 사용하는 개인 키 |
| workloads.loadBalancing.tlsVersion | Body | String | X | TERMINATED\_HTTPS 사용 시 TLS 버전<ul><li>SSLv3</li><li>TLSv1.0</li><li>TLSv1.0\_2016</li><li>TLSv1.1</li><li>TLSv1.2</li><li>TLSv1.3</li></ul> |
| workloads.loadBalancing.containerHref | Body | String | X | 시크릿 컨테이너 ID<li>Load Balancer API를 이용하여 TERMINATED\_HTTPS에서 사용할 인증서,개인 키를 별도로 등록한 경우 사용</li> |
| workloads.loadBalancing.ipAclGroupsBinding | Body | List | X | 로드 밸런서에 적용할 IP 접근 제어 그룹 목록 |
| workloads.loadBalancing.ipAclGroupsBinding.ipAclGroupId | Body | String | O | IP 접근 제어 그룹 ID |
| workloads.schedule | Body | Object | X | 예약 실행 설정 정보 |
| workloads.schedule.timeZone | Body | String | O | 예약 실행 기준 시간<ul><li>예: Asia/Seoul, UTC</li><li>[List of tz database time zones](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)</li></ul> |
| workloads.schedule.cron | Body | String | O | 예약 실행 Cron 표현식 |
| workloads.schedule.jobsHistoryLimit | Body | Integer | O | 예약 실행 히스토리 보관 수 |
| workloads.schedule.concurrencyPolicy | Body | String | O | 동시 실행 정책<ul><li>Forbid</li><li>Replace</li></ul> |
| workloads.schedule.timeOffset | Body | String | O | 예약 실행 기준 시간 Offset |
| workloads.internalLoadBalancing | Body | Object | X | 내부 로드 밸런서 정보 |
| workloads.internalLoadBalancing.enalbed | Body | Boolean | O | 내부 로드 밸런서 사용 여부 |
| workloads.internalLoadBalancing.type | Body | String | X | 내부 로드 밸런서 IP 할당 방법<ul><li>dynamic: 자동 할당</li><li>static: IP 지정</li></ul> |
| workloads.internalLoadBalancing.ip | Body | String | X | 내부 로드 밸런서 지정 IP |
| workloads.privateDns | Body | Object | X | Private DNS에 워크로드 작업 IP 등록 여부 결정 |
| workloads.privateDns.ttl | Body | Integer | O | 레코드 세트의 TTL 값 |
| workloads.privateDns.zoneId | Body | String | O | 워크로드에서 사용하는 Private DNS Zone ID |
| workloads.privateDns.domain | Body | String | O | Private DNS에 등록된 도메인 정보 |
| workloads.activeDeadline | Body | Object | X | 워크로드 예약 종료 정보 |
| workloads.activeDeadline.timeZone | Body | String | O | 예약 종료 기준 시간<li>예: Asia/Seoul, UTC</li> |
| workloads.activeDeadline.timeOffset | Body | String | O | 예약 종료 기준 시간 Offset |
| workloads.activeDeadline.time | Body | String | O | 예약 종료 시간 |
| workloads.autoScaler | Body | Object | X | AutoScaler 설정 정보 |
| workloads.autoScaler.scaleOut | Body | Object | O | ScaleOut 정보 |
| workloads.autoScaler.scaleOut.enabled | Body | Boolean | O | ScaleOut 사용 여부 |
| workloads.autoScaler.scaleOut.maxReplicas | Body | Integer | X | 오토 스케일링 최대 작업 수 |
| workloads.autoScaler.scaleOut.coolDownMinute | Body | Integer | X | 증설 후 대기 시간 |
| workloads.autoScaler.scaleOut.condition | Body | List | X | 증설 조건 |
| workloads.autoScaler.scaleOut.condition.resource | Body | String | X | 증설 조건 기준 리소스<ul><li>cpu</li><li>memory</li></li> |
| workloads.autoScaler.scaleOut.condition.threshold | Body | Integer | X | 증설 조건 리소스 사용량(1~100) |
| workloads.autoScaler.scaleOut.condition.duration | Body | Integer | X | 증설 조건 리소스 사용량 유지 시간(분) |
| workloads.autoScaler.scaleIn | Body | Object | X | ScaleIn 정보 |
| workloads.autoScaler.scaleIn.enabled | Body | Boolean | O | ScaleIn 사용 여부 |
| workloads.autoScaler.scaleIn.minReplicas | Body | Integer | X | 오토 스케일링 최소 작업 수 |
| workloads.autoScaler.scaleIn.coolDownMinute | Body | Integer | X | 감축 후 대기 시간 |
| workloads.autoScaler.scaleIn.condition | Body | List | X | 감축 조건 |
| workloads.autoScaler.scaleIn.condition.resource | Body | String | X | 감축 조건 기준 리소스<ul><li>cpu</li><li>memory</li></li> |
| workloads.autoScaler.scaleIn.condition.threshold | Body | Integer | X | 감축 조건 리소스 사용량(1~100) |
| workloads.autoScaler.scaleIn.condition.duration | Body | Integer | X | 감축 조건 리소스 사용량 유지 시간(분) |
| workloads.securityGroups | Body | List | X | SecurityGroups 정보 |
| workloads.securityGroups.id | Body | String | O | SecurityGroups ID |

<details>
  <summary>예시</summary>

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

### 워크로드 보기

개별 워크로드를 조회합니다.

```bash
GET /ncs/v1.0/appkeys/{appKey}/workloads/{workloadId}
x-nhn-authorization: {token}
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| workloadId | URL | String | O | 워크로드 ID |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|

#### 응답

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| workload | Body | Array | O | 워크로드 정보 |
| workload.id | Body | UUID | O | 워크로드 ID |
| workload.name | Body | String | O | 워크로드 이름 |
| workload.type | Body | String | O | 배포 컨트롤러<ul><li>deployment</li><li>statefulset</li></ul> |
| workload.templateId | Body | String | O | 워크로드의 템플릿 ID |
| workload.templateVersion | Body | String | O | 워크로드의 템플릿 버전 |
| workload.createdAt | Body | String | O | 생성 시간(UTC) |
| workload.desired | Body | Integer | O | 워크로드 작업 요청 수 |
| workload.available | Body | Integer | O | 워크로드 작업 실행 수 |
| workload.status | Body | Integer | O | 워크로드 상태 |
| workload.url | Body | String | X | 워크로드 로드 밸런서 URL |
| workload.loadBalancing | Body | Object | O | 워크로드 로드 밸런서 정보 |
| workload.loadBalancing.enabled | Body | Boolean | O | 워크로드 로드 밸런서 사용 여부 |
| workload.loadBalancing.floatingIp | Body | Boolean | O | 워크로드 로드 밸런서 플로팅 IP 사용 여부 |
| workload.loadBalancing.healthMonitor | Body | Object | X | 로드 밸런서의 상태 확인 정보 |
| workload.loadBalancing.healthMonitor.delay | Body | Integer | O | 상태 확인 주기 |
| workload.loadBalancing.healthMonitor.timeout | Body | Integer | O | 최대 응답 대기 시간 |
| workload.loadBalancing.healthMonitor.maxRetries | Body | Integer | O | 최대 재시도 횟수 |
| workload.loadBalancing.healthMonitor.httpMethod | Body | String | X | HTTP 메서드<ul><li>GET</li></ul> |
| workload.loadBalancing.healthMonitor.expectedCodes | Body | String | X | HTTP 상태 코드<ul><li>200</li><li>200,202</li><li>200-204</li></ul> |
| workload.loadBalancing.healthMonitor.urlPath | Body | String | X | HTTP URL |
| workload.loadBalancing.certificate | Body | String | X | TERMINATED\_HTTPS 사용 시 로드 밸런서에서 사용하는 인증서 |
| workload.loadBalancing.privateKey | Body | String | X | TERMINATED\_HTTPS 사용 시 로드 밸런서에서 사용하는 개인 키 |
| workload.loadBalancing.tlsVersion | Body | String | X | TERMINATED\_HTTPS 사용 시 TLS 버전<ul><li>SSLv3</li><li>TLSv1.0</li><li>TLSv1.0\_2016</li><li>TLSv1.1</li><li>TLSv1.2</li><li>TLSv1.3</li></ul> |
| workload.loadBalancing.containerHref | Body | String | X | 시크릿 컨테이너 ID<li>Load Balancer API를 이용하여 TERMINATED\_HTTPS에서 사용할 인증서,개인 키를 별도로 등록한 경우 사용</li> |
| workload.loadBalancing.ipAclGroupsBinding | Body | List | X | 로드 밸런서에 적용할 IP 접근 제어 그룹 목록 |
| workload.loadBalancing.ipAclGroupsBinding.ipAclGroupId | Body | String | O | IP 접근 제어 그룹 ID |
| workload.schedule | Body | Object | X | 예약 실행 설정 정보 |
| workload.schedule.timeZone | Body | String | O | 예약 실행 기준 시간<ul><li>예: Asia/Seoul, UTC</li><li>[List of tz database time zones](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)</li></ul> |
| workload.schedule.cron | Body | String | O | 예약 실행 Cron 표현식 |
| workload.schedule.jobsHistoryLimit | Body | Integer | O | 예약 실행 히스토리 보관 수 |
| workload.schedule.concurrencyPolicy | Body | String | O | 동시 실행 정책<ul><li>Forbid</li><li>Replace</li></ul> |
| workload.schedule.timeOffset | Body | String | O | 예약 실행 기준 시간 Offset |
| workload.internalLoadBalancing | Body | Object | X | 내부 로드 밸런서 정보 |
| workload.internalLoadBalancing.enalbed | Body | Boolean | O | 내부 로드 밸런서 사용 여부 |
| workload.internalLoadBalancing.type | Body | String | X | 내부 로드 밸런서 IP 할당 방법<ul><li>dynamic: 자동 할당</li><li>static: IP 지정</li></ul> |
| workload.internalLoadBalancing.ip | Body | String | X | 내부 로드 밸런서 지정 IP |
| workload.privateDns | Body | Object | X | Private DNS에 워크로드 작업 IP 등록 여부 결정 |
| workload.privateDns.ttl | Body | Integer | O | 레코드 세트의 TTL 값 |
| workload.privateDns.zoneId | Body | String | O | 워크로드에서 사용하는 Private DNS Zone ID |
| workload.privateDns.domain | Body | String | O | Private DNS에 등록된 도메인 정보 |
| workload.activeDeadline | Body | Object | X | 워크로드 예약 종료 정보 |
| workload.activeDeadline.timeZone | Body | String | O | 예약 종료 기준 시간<li>예: Asia/Seoul, UTC</li> |
| workload.activeDeadline.timeOffset | Body | String | O | 예약 종료 기준 시간 Offset |
| workload.activeDeadline.time | Body | String | O | 예약 종료 시간 |
| workload.autoScaler | Body | Object | X | AutoScaler 설정 정보 |
| workload.autoScaler.scaleOut | Body | Object | O | ScaleOut 정보 |
| workload.autoScaler.scaleOut.enabled | Body | Boolean | O | ScaleOut 사용 여부 |
| workload.autoScaler.scaleOut.maxReplicas | Body | Integer | X | 오토 스케일링 최대 작업 수 |
| workload.autoScaler.scaleOut.coolDownMinute | Body | Integer | X | 증설 후 대기 시간 |
| workload.autoScaler.scaleOut.condition | Body | List | X | 증설 조건 |
| workload.autoScaler.scaleOut.condition.resource | Body | String | X | 증설 조건 기준 리소스<ul><li>cpu</li><li>memory</li></ul> |
| workload.autoScaler.scaleOut.condition.threshold | Body | Integer | X | 증설 조건 리소스 사용량(1~100) |
| workload.autoScaler.scaleOut.condition.duration | Body | Integer | X | 증설 조건 리소스 사용량 유지 시간(분) |
| workload.autoScaler.scaleIn | Body | Object | X | ScaleIn 정보 |
| workload.autoScaler.scaleIn.enabled | Body | Boolean | O | ScaleIn 사용 여부 |
| workload.autoScaler.scaleIn.minReplicas | Body | Integer | X | 오토 스케일링 최소 작업 수 |
| workload.autoScaler.scaleIn.coolDownMinute | Body | Integer | X | 감축 후 대기 시간 |
| workload.autoScaler.scaleIn.condition | Body | List | X | 감축 조건 |
| workload.autoScaler.scaleIn.condition.resource | Body | String | X | 감축 조건 기준 리소스<ul><li>cpu</li><li>memory</li></ul> |
| workload.autoScaler.scaleIn.condition.threshold | Body | Integer | X | 감축 조건 리소스 사용량(1~100) |
| workload.autoScaler.scaleIn.condition.duration | Body | Integer | X | 감축 조건 리소스 사용량 유지 시간(분) |
| workload.securityGroups | Body | List | X | SecurityGroups 정보 |
| workload.securityGroups.id | Body | String | O | SecurityGroups ID |
| workload.tasks | Body | Array | O | 워크로드의 작업 목록 |
| workload.tasks.id | Body | UUID | O | 작업 ID |
| workload.tasks.containers | Body | Array | O | 작업의 컨테이너 목록 |
| workload.tasks.containers.name | Body | String | O | 컨테이너 이름 |
| workload.tasks.containers.type | Body | String | O | 컨테이너 유형<ul><li>normal: 일반</li><li>init: 초기화</li></ul>|
| workload.tasks.containers.image | Body | String | O | 컨테이너 이미지 |
| workload.tasks.containers.ip | Body | String | X | 컨테이너 IP |
| workload.tasks.containers.cpus | Body | Float | O | 컨테이너에 할당된 CPU 수 |
| workload.tasks.containers.memoryLimit | Body | Object | O | 컨테이너에 할당된 메모리 정보 |
| workload.tasks.containers.memoryLimit.hard | Body | Integer | O | 컨테이너에 할당된 메모리(MiB) |
| workload.tasks.containers.ports | Body | Array | X | 컨테이너의 포트 정보 |
| workload.tasks.containers.ports.containerPort | Body | Integer | O | 컨테이너 포트 |
| workload.tasks.containers.ports.protocol | Body | String | O | 컨테이너 프로토콜<ul><li>TCP</li><li>UDP</li><li>HTTP</li><li>HTTPS</li><li>TERMINATED\_HTTPS</li></ul> |
| workload.tasks.containers.command | Body | String List | X | 컨테이너가 시작될 때 실행될 명령어 |
| workload.tasks.containers.args | Body | String List | X | 컨테이너가 시작될 때 사용되는 인자 |
| workload.tasks.containers.workDirectory | Body | String | X | 컨테이너의 작업 디렉터리 |
| workload.tasks.containers.env | Body | Array | X | 컨테이너 환경 변수 |
| workload.tasks.containers.env.name | Body | String | O | 컨테이너 환경 변수 이름 |
| workload.tasks.containers.env.value | Body | String | O | 컨테이너 환경 변수 값 |
| workload.tasks.containers.postStart | Body | String List | X | 컨테이너 생성 직후 실행되는 명령어 |
| workload.tasks.containers.preStop | Body | String List | X | 컨테이너 종료 직전 실행되는 명령어 |
| workload.tasks.containers.configs | Body | List | X | 컨테이너에서 사용하는 ConfigMap 정보 |
| workload.tasks.containers.configs.id | Body | Integer | O | ConfigMap ID |
| workload.tasks.containers.configs.type | Body | String | O | ConfigMap 정보를 가져오는 service type<ul><li>obs: Object Storage</li></ul> |
| workload.tasks.containers.configs.value | Body | String | O | 오브젝트 URL |
| workload.tasks.containers.configs.mountPath | Body | String | O | 컨테이너 마운트 경로 |
| workload.tasks.containers.secrets | Body | List | X | 컨테이너에서 사용하는 Secret 정보 |
| workload.tasks.containers.secrets.type | Body | String | O | Secret 정보를 가져오는 service type<ul><li>skm: Secure Key Manager</li></ul> |
| workload.tasks.containers.secrets.value | Body | String | O | 키 아이디 |
| workload.tasks.containers.secrets.mountPath | Body | String | O | 컨테이너 마운트 경로 |
| workload.tasks.containers.volumes | Body | Array | X | 컨테이너에서 사용하는 스토리지 정보 |
| workload.tasks.containers.volumes.name | Body | String | O | 스토리지 이름 |
| workload.tasks.containers.volumes.path | Body | String | O | NAS 스토리지 연결 경로 |
| workload.tasks.containers.volumes.mountPath | body | String | X | 컨테이너의 연결 경로(default: /mnt) |
| workload.tasks.containers.probe | Body | List | X | 컨테이너 Probe 설정 |
| workload.tasks.containers.probe.type | Body | String | O | 컨테이너 Probe 타입<ul><li>startup</li><li>liveness</li></ul> |
| workload.tasks.containers.probe.failureThreshold | Body | Integer | O | Probe 실패 기준 |
| workload.tasks.containers.probe.initialDelaySeconds | Body | Integer | O | Probe 시작 대기 시간 |
| workload.tasks.containers.probe.periodSeconds | Body | String | O | Probe 실행 간격 |
| workload.tasks.containers.probe.timeoutSeconds | Body | String | O | Probe 실행 제한 시간 |
| workload.tasks.containers.probe.exec | Body | String List | O | Probe 실행 명령어 |
| workload.tasks.containers.stopTimeout | Body | Integer | X | 초기화 컨테이너 실행 제한 시간(초) |
| workload.tasks.containers.state | Body | String | O | 컨테이너 상태 |
| workload.tasks.containers.startedAt | Body | String | O | 컨테이너 시작 시간 |
| workload.tasks.containers.finishedAt | Body | String | X | 초기화 컨테이너 완료 시간 |
| workload.tasks.containers.restartCount | Body | String | O | 컨테이너 재시작 횟수 |

<details>
  <summary>예시</summary>

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

### 워크로드 로그 보기

워크로드의 컨테이너 로그를 조회합니다.

```bash
GET /ncs/v1.0/appkeys/{appKey}/workloads/{workloadId}/tasks/{taskId}/logs?container={ContainerName}&from={YYYY-MM-DDThh:mm:ssZ}&to={YYYY-MM-DDThh:mm:ssZ}
x-nhn-authorization: {token}
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| workloadId | URL | String | O | 워크로드 ID |
| taskId | URL | String | O | 작업 ID |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|
| containerName | Query | String | O | 컨테이너 이름 |
| from | Query | String | X | 로그 시작 시간(default: 현재로부터 5분 전) |
| to | Query | String | X | 로그 종료 시간(default: 현재 시간) |
| page | Query | String | X | 조회할 페이지 |
| size | Query | String | X | 조회할 페이지 크기(default: 100) |

#### 응답

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| logs | Body | Array | O | 컨테이너 로그 목록 |
| log | Body | String | O | 로그 내용 |
| time | Body | String | O | 로그 발생 시간(UTC) |

<details>
  <summary>예시</summary>

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

### 워크로드 이벤트 보기

워크로드의 이벤트를 조회합니다.

```bash
GET /ncs/v1.0/appkeys/{appKey}/workloads/{workloadId}/tasks/{taskId}/events
x-nhn-authorization: {token}
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| workloadId | URL | String | O | 워크로드 ID |
| taskId | URL | String | O | 작업 ID |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|
| type | Query | Integer | X | 이벤트 타입<ul><li>Normal</li><li>Warning</li></ul> |
| q | Query | String | X | 이벤트 내용 필터링 |
| page | Query | String | X | 조회할 페이지 |
| size | Query | String | X | 조회할 페이지 크기(default: 10) |
| from | Query | String | X | 이벤트 마지막 발생 일시 시작 시간(default: 현재로부터 1시간 전) |
| to | Query | String | X | 이벤트 마지막 발생 일시 종료 시간(default: 현재 시간) |

#### 응답

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| events | Body | Array | O | 이벤트 목록 |
| type | Body | String | O | 이벤트 타입<ul><li>Normal</li><li>Warning</li></ul> |
| reason | Body | String | O | 이벤트 발생 원인 |
| message | Body | String | O | 이벤트 내용 |
| createTimestamp | Body | String | O | 이벤트 최초 발생 일시(UTC) |
| lastTimestamp | Body | String | O | 이벤트 마지막 발생 일시(UTC) |
| count | Body | Integer | O | 이벤트 발생 횟수 |

<details>
  <summary>예시</summary>

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

### 워크로드 실행 히스토리 목록 보기

워크로드 실행 히스토리 목록을 조회합니다.

```bash
GET /ncs/v1.0/appkeys/{appKey}/workloads/{workloadId}/history
x-nhn-authorization: {token}
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| workloadId | URL | String | O | 워크로드 ID |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|
| page | Query | Integer | X | 조회할 페이지 번호 |
| size | Query | Integer | X | 조회할 페이지 크기(default: 10) |
| sort | Query | String | X | 정렬 기준이 될 필드명<br>역순 정렬일 경우 필드명 앞에 `-`를 붙임<br>예: `sort=-id` |

#### 응답

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| history | Body | Array | O | 히스토리 목록 |
| history.id | Body | Integer | O | 히스토리 ID |
| history.createdAt | Body | String | O | 배포 시간 |
| history.deletedAt | Body | String | O | 종료 시간 |
| history.templateId | Body | UUID | O | 워크로드가 사용한 템플릿 ID |
| history.templateVersion | Body | UUID | O | 워크로드가 사용한 템플릿 버전 |
| history.name | Body | String | O | 템플릿 이름 |
| history.status | Body | String | O | 상태<ul><li>Succeeded</li><li>Terminated</li><liPending</li></ul> |

<details>
  <summary>예시</summary>

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

### 워크로드 실행 히스토리 보기

개별 워크로드 실행 히스토리를 조회합니다.

```bash
GET /ncs/v1.0/appkeys/{appKey}/workloads/{workloadId}/history/{historyId}
x-nhn-authorization: {token}
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| workloadId | URL | String | O | 워크로드 ID |
| historyId | URL | Integer | O | 히스토리 ID |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|

#### 응답

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| history | Body | Object | O | 히스토리 정보 |
| history.id | Body | Integer | O | 히스토리 ID |
| history.createdAt | Body | String | O | 실행 시간 |
| history.deletedAt | Body | String | O | 종료 시간 |
| history.templateId | Body | UUID | O | 워크로드가 사용한 템플릿 ID |
| history.name | Body | String | O | 워크로드가 사용한 템플릿 이름 |
| history.status | Body | String | O | 상태<ul><li>Succeeded</li><li>Terminated</li><li>Pending</li></ul> |
| template | Body | Object | O | 템플릿 정보 |
| template.id | Body | UUID | O | 템플릿 ID |
| template.version | Body | String | O | 템플릿 버전 |
| template.name | Body | String | O | 템플릿 이름 |
| template.createdAt | Body | String | O | 생성 시간(UTC) |
| template.description | Body | String | X | 템플릿 설명 |
| template.versionDescription | Body | String | X | 템플릿 버전 설명 |
| template.networks | Body | Array | O | 템플릿의 네트워크 정보 |
| template.networks.vpcId | Body | String | O | 템플릿의 VPC ID |
| template.networks.subnetId | Body | String | O | 템플릿의 Subnet ID |
| template.dnsConfig | Body | String List | X | 컨테이너에 설정된 DNS Server 정보 |
| template.hostAliases | Body | List | X | 컨테이너 `/etc/hosts`에 설정된 정보 |
| template.hostAliases.ip | Body | String | O | 컨테이너에 설정된 hostnames의 IP |
| template.hostAliases.hostnames | Body | String List | O | 컨테이너에 설정된 IP의 hostnames |
| template.containers | Body | Array | O | 템플릿의 컨테이너 목록 |
| template.containers.name | Body | String | O | 컨테이너 이름 |
| template.containers.type | Body | String | O | 컨테이너 유형<ul><li>normal: 일반</li><li>init: 초기화</li></ul>|
| template.containers.image | Body | String | O | 컨테이너 이미지 |
| template.containers.cpus | Body | Float | O | 컨테이너에 할당하는 CPU 개수 |
| template.containers.memoryLimit | Body | Object | O | 컨테이너에 할당하는 메모리 정보 |
| template.containers.memoryLimit.hard | Body | Integer | O | 컨테이너에 할당하는 메모리(MiB) |
| template.containers.ports | Body | Array | X | 컨테이너에서 사용하는 포트 정보 |
| template.containers.ports.containerPort | Body | Integer | O | 컨테이너 포트 |
| template.containers.ports.protocol | Body | String | O | 컨테이너 프로토콜<ul><li>TCP</li><li>UDP</li><li>HTTP</li><li>HTTPS</li><li>TERMINATED\_HTTPS</li></ul> |
| template.containers.command | Body | String List | X | 컨테이너가 시작될 때 실행될 명령어 |
| template.containers.args | Body | String List | X | 컨테이너가 시작될 때 사용되는 인자 |
| template.containers.workDirectory | Body | String | X | 컨테이너의 작업 디렉터리 |
| template.containers.env | Body | Array | X | 컨테이너 환경 변수 |
| template.containers.env.name | Body | String | O | 컨테이너 환경 변수 이름 |
| template.containers.env.value | Body | String | O | 컨테이너 환경 변수 값 |
| template.containers.postStart | Body | String List | X | 컨테이너 생성 직후 실행되는 명령어 |
| template.containers.preStop | Body | String List | X | 컨테이너 종료 직전 실행되는 명령어 |
| template.containers.configs | Body | List | X | 컨테이너에서 사용하는 ConfigMap 정보 |
| template.containers.configs.id | Body | Integer | O | ConfigMap ID |
| template.containers.configs.type | Body | String | O | ConfigMap 정보를 가져오는 service type<ul><li>obs: Object Storage</li></ul> |
| template.containers.configs.value | Body | String | O | 오브젝트 URL |
| template.containers.configs.mountPath | Body | String | O | 컨테이너 마운트 경로 |
| template.containers.secrets | Body | List | X | 컨테이너에서 사용하는 Secret 정보 |
| template.containers.secrets.type | Body | String | O | Secret 정보를 가져오는 service type<ul><li>skm: Secure Key Manager</li></ul> |
| template.containers.secrets.value | Body | String | O | 키 아이디 |
| template.containers.secrets.mountPath | Body | String | O | 컨테이너 마운트 경로 |
| template.containers.volumes | Body | Array | X | 컨테이너에서 사용하는 NAS 스토리지 정보 |
| template.containers.volumes.name | Body | String | O | 스토리지 이름 |
| template.containers.volumes.path | Body | String | O | NAS 스토리지 연결 경로 |
| template.containers.volumes.mountPath | body | String | X | 컨테이너의 연결 경로 |
| template.containers.probe | Body | List | X | 컨테이너 Probe 설정 |
| template.containers.probe.type | Body | String | O | 컨테이너 Probe 타입<ul><li>startup</li><li>liveness</li></ul> |
| template.containers.probe.failureThreshold | Body | Integer | O | Probe 실패 기준 |
| template.containers.probe.initialDelaySeconds | Body | Integer | O | Probe 시작 대기 시간 |
| template.containers.probe.periodSeconds | Body | Integer | O | Probe 실행 간격 |
| template.containers.probe.timeoutSeconds | Body | Integer | O | Probe 실행 제한 시간 |
| template.containers.probe.exec | Body | String List | O | Probe 실행 명령어 |
| template.containers.stopTimeout | Body | Integer | X | 초기화 컨테이너 실행 제한 시간(초) |

<details>
  <summary>예시</summary>

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

### 워크로드 예약 실행 히스토리 보기

예약 실행 히스토리를 조회합니다.

```bash
GET /ncs/v1.0/appkeys/{appKey}/workloads/{workloadId}/schedulehistory
x-nhn-authorization: {token}
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| workloadId | URL | String | O | 워크로드 ID |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|
| page | Query | Integer | X | 조회할 페이지 번호 |
| size | Query | Integer | X | 조회할 페이지 크기(default: 10) |

#### 응답

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| schedulehistory | Body | List | O | 예약 실행 히스토리 목록 |
| schedulehistory.id | Body | String | O | 작업 ID |
| schedulehistory.createdAt | Body | String | O | 시작 시간 |
| schedulehistory.finishedAt | Body | Object | O | 종료 시간 |
| schedulehistory.status | Body | String | O | 예약 작업 상태<ul><li>Error</li><li>Completed</li><li>Waiting</li><li>Running</li></ul> |

<details>
  <summary>예시</summary>

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

### 워크로드 생성하기

워크로드를 생성합니다.

```bash
POST /ncs/v1.0/appkeys/{appKey}/workloads
Content-Type: application/json
x-nhn-authorization: {token}
```

#### 요청

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|
| workload | Body | Object | O | 워크로드 정보 |
| workload.name | Body | String | O | 워크로드 이름 |
| workload.type | Body | String | X | 배포 컨트롤러(default:deployment)<ul><li>deployment</li><li>statefulset</li></ul> |
| workload.templateId | Body | String | O | 워크로드의 템플릿 ID |
| workload.templateVersion | Body | String | X | 워크로드의 템플릿 버전(default: 최신 버전) |
| workload.desired | Body | Integer | O | 워크로드 작업 요청 수 |
| workload.loadBalancing | Body | Object | O | 워크로드 로드 밸런서 정보 |
| workload.loadBalancing.enabled | Body | Boolean | O | 워크로드 로드 밸런서 사용 여부 |
| workload.loadBalancing.floatingIp | Body | Boolean | O | 워크로드 로드 밸런서 플로팅 IP 사용 여부 |
| workload.loadBalancing.healthMonitor | Body | Object | X | 로드 밸런서의 상태 확인 정보 |
| workload.loadBalancing.healthMonitor.delay | Body | Integer | O | 상태 확인 주기 |
| workload.loadBalancing.healthMonitor.timeout | Body | Integer | O | 최대 응답 대기 시간 |
| workload.loadBalancing.healthMonitor.maxRetries | Body | Integer | O | 최대 재시도 횟수 |
| workload.loadBalancing.healthMonitor.httpMethod | Body | String | X | HTTP 메서드<ul><li>GET</li></ul> |
| workload.loadBalancing.healthMonitor.expectedCodes | Body | String | X | HTTP 상태 코드<ul><li>200</li><li>200,202</li><li>200-204</li></ul> |
| workload.loadBalancing.healthMonitor.urlPath | Body | String | X | HTTP URL |
| workload.loadBalancing.certificate | Body | String | X | TERMINATED\_HTTPS 사용 시 로드 밸런서에서 사용하는 인증서 |
| workload.loadBalancing.privateKey | Body | String | X | TERMINATED\_HTTPS 사용 시 로드 밸런서에서 사용하는 개인 키 |
| workload.loadBalancing.tlsVersion | Body | String | X | TERMINATED\_HTTPS 사용 시 TLS 버전<ul><li>SSLv3</li><li>TLSv1.0</li><li>TLSv1.0\_2016</li><li>TLSv1.1</li><li>TLSv1.2</li><li>TLSv1.3</li></ul> |
| workload.loadBalancing.containerHref | Body | String | X | 시크릿 컨테이너 ID<li>Load Balancer API를 이용하여 TERMINATED\_HTTPS에서 사용할 인증서,개인 키를 별도로 등록한 경우 사용</li> |
| workload.loadBalancing.ipAclGroupsBinding | Body | List | X | 로드 밸런서에 적용할 IP 접근 제어 그룹 목록 |
| workload.loadBalancing.ipAclGroupsBinding.ipAclGroupId | Body | String | O | IP 접근 제어 그룹 ID |
| workload.schedule | Body | Object | X | 예약 실행 설정 정보 |
| workload.schedule.timeZone | Body | String | O | 예약 실행 기준 시간<ul><li>예: Asia/Seoul, UTC</li><li>[List of tz database time zones](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)</li></ul> |
| workload.schedule.cron | Body | String | O | 예약 실행 Cron 표현식 |
| workload.schedule.jobsHistoryLimit | Body | Integer | O | 예약 실행 히스토리 보관 수 |
| workload.schedule.concurrencyPolicy | Body | String | O | 동시 실행 정책<ul><li>Forbid</li><li>Replace</li></ul> |
| workload.internalLoadBalancing | Body | Object | X | 내부 로드 밸런서 정보 |
| workload.internalLoadBalancing.enalbed | Body | Boolean | O | 내부 로드 밸런서 사용 여부 |
| workload.internalLoadBalancing.type | Body | String | X | 내부 로드 밸런서 IP 할당 방법<ul><li>dynamic: 자동 할당</li><li>static: IP 지정</li></ul> |
| workload.internalLoadBalancing.ip | Body | String | X | 내부 로드 밸런서 지정 IP |
| workload.privateDns | Body | Object | X | Private DNS에 워크로드 작업 IP 등록 여부 결정 |
| workload.privateDns.ttl | Body | Integer | O | 레코드 세트의 TTL 값 |
| workload.privateDns.zoneId | Body | String | O | 워크로드에서 사용하는 Private DNS Zone ID |
| workload.privateDns.domain | Body | String | O | Private DNS에 등록된 도메인 정보 |
| workload.activeDeadline | Body | Object | X | 워크로드 예약 종료 정보 |
| workload.activeDeadline.timeZone | Body | String | O | 예약 종료 기준 시간<li>예: Asia/Seoul, UTC</li> |
| workload.activeDeadline.timeOffset | Body | String | O | 예약 종료 기준 시간 Offset |
| workload.activeDeadline.time | Body | String | O | 예약 종료 시간 |
| workload.autoScaler | Body | Object | X | AutoScaler 설정 정보 |
| workload.autoScaler.scaleOut | Body | Object | O | ScaleOut 정보 |
| workload.autoScaler.scaleOut.enabled | Body | Boolean | O | ScaleOut 사용 여부 |
| workload.autoScaler.scaleOut.maxReplicas | Body | Integer | X | 오토 스케일링 최대 작업 수 |
| workload.autoScaler.scaleOut.coolDownMinute | Body | Integer | X | 증설 후 대기 시간 |
| workload.autoScaler.scaleOut.condition | Body | List | X | 증설 조건 |
| workload.autoScaler.scaleOut.condition.resource | Body | String | X | 증설 조건 기준 리소스<ul><li>cpu</li><li>memory</li></ul> |
| workload.autoScaler.scaleOut.condition.threshold | Body | Integer | X | 증설 조건 리소스 사용량(1~100) |
| workload.autoScaler.scaleOut.condition.duration | Body | Integer | X | 증설 조건 리소스 사용량 유지 시간(분) |
| workload.autoScaler.scaleIn | Body | Object | X | ScaleIn 정보 |
| workload.autoScaler.scaleIn.enabled | Body | Boolean | O | ScaleIn 사용 여부 |
| workload.autoScaler.scaleIn.minReplicas | Body | Integer | X | 오토 스케일링 최소 작업 수 |
| workload.autoScaler.scaleIn.coolDownMinute | Body | Integer | X | 감축 후 대기 시간 |
| workload.autoScaler.scaleIn.condition | Body | List | X | 감축 조건 |
| workload.autoScaler.scaleIn.condition.resource | Body | String | X | 감축 조건 기준 리소스<ul><li>cpu</li><li>memory</li></ul> |
| workload.autoScaler.scaleIn.condition.threshold | Body | Integer | X | 감축 조건 리소스 사용량(1~100) |
| workload.autoScaler.scaleIn.condition.duration | Body | Integer | X | 감축 조건 리소스 사용량 유지 시간(분) |
| workload.securityGroups | Body | List | X | SecurityGroups 정보 |
| workload.securityGroups.id | Body | String | O | SecurityGroups ID |

<details>
  <summary>예시</summary>

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

#### 응답

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| workload | Body | Object | O | 워크로드 정보 |
| workload.id | Body | UUID | O | 워크로드 ID |
| workload.name | Body | String | O | 워크로드 이름 |
| workload.type | Body | String | O | 배포 컨트롤러<ul><li>deployment</li><li>statefulset</li></ul> |
| workload.templateId | Body | String | O | 워크로드의 템플릿 ID |
| workload.templateVersion | Body | String | O | 워크로드의 템플릿 버전 |
| workload.createdAt | Body | String | O | 생성 시간(UTC) |
| workload.desired | Body | Integer | O | 워크로드 작업 요청 수 |
| workload.loadBalancing | Body | Object | O | 워크로드 로드 밸런서 정보 |
| workload.loadBalancing.enabled | Body | Boolean | O | 워크로드 로드 밸런서 사용 여부 |
| workload.loadBalancing.floatingIp | Body | Boolean | O | 워크로드 로드 밸런서 플로팅 IP 사용 여부 |
| workload.loadBalancing.healthMonitor | Body | Object | X | 로드 밸런서의 상태 확인 정보 |
| workload.loadBalancing.healthMonitor.delay | Body | Integer | O | 상태 확인 주기 |
| workload.loadBalancing.healthMonitor.timeout | Body | Integer | O | 최대 응답 대기 시간 |
| workload.loadBalancing.healthMonitor.maxRetries | Body | Integer | O | 최대 재시도 횟수 |
| workload.loadBalancing.healthMonitor.httpMethod | Body | String | X | HTTP 메서드<ul><li>GET</li></ul> |
| workload.loadBalancing.healthMonitor.expectedCodes | Body | String | X | HTTP 상태 코드<ul><li>200</li><li>200,202</li><li>200-204</li></ul> |
| workload.loadBalancing.healthMonitor.urlPath | Body | String | X | HTTP URL |
| workload.loadBalancing.certificate | Body | String | X | TERMINATED\_HTTPS 사용 시 로드 밸런서에서 사용하는 인증서 |
| workload.loadBalancing.privateKey | Body | String | X | TERMINATED\_HTTPS 사용 시 로드 밸런서에서 사용하는 개인 키 |
| workload.loadBalancing.tlsVersion | Body | String | X | TERMINATED\_HTTPS 사용 시 TLS 버전<ul><li>SSLv3</li><li>TLSv1.0</li><li>TLSv1.0\_2016</li><li>TLSv1.1</li><li>TLSv1.2</li><li>TLSv1.3</li></ul> |
| workload.loadBalancing.containerHref | Body | String | X | 시크릿 컨테이너 ID<li>Load Balancer API를 이용하여 TERMINATED\_HTTPS에서 사용할 인증서,개인 키를 별도로 등록한 경우 사용</li> |
| workload.loadBalancing.ipAclGroupsBinding | Body | List | X | 로드 밸런서에 적용할 IP 접근 제어 그룹 목록 |
| workload.loadBalancing.ipAclGroupsBinding.ipAclGroupId | Body | String | O | IP 접근 제어 그룹 ID |
| workload.schedule | Body | Object | X | 예약 실행 설정 정보 |
| workload.schedule.timeZone | Body | String | O | 예약 실행 기준 시간<ul><li>예: Asia/Seoul, UTC</li><li>[List of tz database time zones](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)</li></ul> |
| workload.schedule.cron | Body | String | O | 예약 실행 Cron 표현식 |
| workload.schedule.jobsHistoryLimit | Body | Integer | O | 예약 실행 히스토리 보관 수 |
| workload.schedule.concurrencyPolicy | Body | String | O | 동시 실행 정책<ul><li>Forbid</li><li>Replace</li></ul> |
| workload.schedule.timeOffset | Body | String | O | 예약 실행 기준 시간 Offset |
| workload.internalLoadBalancing | Body | Object | X | 내부 로드 밸런서 정보 |
| workload.internalLoadBalancing.enalbed | Body | Boolean | O | 내부 로드 밸런서 사용 여부 |
| workload.internalLoadBalancing.type | Body | String | X | 내부 로드 밸런서 IP 할당 방법<ul><li>dynamic: 자동 할당</li><li>static: IP 지정</li></ul> |
| workload.internalLoadBalancing.ip | Body | String | X | 내부 로드 밸런서 지정 IP |
| workload.privateDns | Body | Object | X | Private DNS에 워크로드 작업 IP 등록 여부 결정 |
| workload.privateDns.ttl | Body | Integer | O | 레코드 세트의 TTL 값 |
| workload.privateDns.zoneId | Body | String | O | 워크로드에서 사용하는 Private DNS Zone ID |
| workload.privateDns.domain | Body | String | O | Private DNS에 등록된 도메인 정보 |
| workload.activeDeadline | Body | Object | X | 워크로드 예약 종료 정보 |
| workload.activeDeadline.timeZone | Body | String | O | 예약 종료 기준 시간<li>예: Asia/Seoul, UTC</li> |
| workload.activeDeadline.timeOffset | Body | String | O | 예약 종료 기준 시간 Offset |
| workload.activeDeadline.time | Body | String | O | 예약 종료 시간 |
| workload.autoScaler | Body | Object | X | AutoScaler 설정 정보 |
| workload.autoScaler.scaleOut | Body | Object | O | ScaleOut 정보 |
| workload.autoScaler.scaleOut.enabled | Body | Boolean | O | ScaleOut 사용 여부 |
| workload.autoScaler.scaleOut.maxReplicas | Body | Integer | X | 오토 스케일링 최대 작업 수 |
| workload.autoScaler.scaleOut.coolDownMinute | Body | Integer | X | 증설 후 대기 시간 |
| workload.autoScaler.scaleOut.condition | Body | List | X | 증설 조건 |
| workload.autoScaler.scaleOut.condition.resource | Body | String | X | 증설 조건 기준 리소스<ul><li>cpu</li><li>memory</li></ul> |
| workload.autoScaler.scaleOut.condition.threshold | Body | Integer | X | 증설 조건 리소스 사용량(1~100) |
| workload.autoScaler.scaleOut.condition.duration | Body | Integer | X | 증설 조건 리소스 사용량 유지 시간(분) |
| workload.autoScaler.scaleIn | Body | Object | X | ScaleIn 정보 |
| workload.autoScaler.scaleIn.enabled | Body | Boolean | O | ScaleIn 사용 여부 |
| workload.autoScaler.scaleIn.minReplicas | Body | Integer | X | 오토 스케일링 최소 작업 수 |
| workload.autoScaler.scaleIn.coolDownMinute | Body | Integer | X | 감축 후 대기 시간 |
| workload.autoScaler.scaleIn.condition | Body | List | X | 감축 조건 |
| workload.autoScaler.scaleIn.condition.resource | Body | String | X | 감축 조건 기준 리소스<ul><li>cpu</li><li>memory</li></ul> |
| workload.autoScaler.scaleIn.condition.threshold | Body | Integer | X | 감축 조건 리소스 사용량(1~100) |
| workload.autoScaler.scaleIn.condition.duration | Body | Integer | X | 감축 조건 리소스 사용량 유지 시간(분) |
| workload.securityGroups | Body | List | X | SecurityGroups 정보 |
| workload.securityGroups.id | Body | String | O | SecurityGroups ID |

<details>
  <summary>예시</summary>

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

### 워크로드 변경하기

워크로드를 변경합니다.

```bash
PUT /ncs/v1.0/appkeys/{appKey}/workloads/{workloadId}
Content-Type: application/json
x-nhn-authorization: {token}
```

#### 요청

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| workloadId | URL | String | O | 워크로드 ID |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|
| workload | Body | Object | O | 워크로드 정보 |
| workload.name | Body | String | O | 워크로드 이름 |
| workload.templateId | Body | String | O | 워크로드의 템플릿 ID |
| workload.templateVersion | Body | String | O | 워크로드의 템플릿 버전 |
| workload.desired | Body | Integer | O | 워크로드 작업 요청 수 |
| workload.loadBalancing | Body | Object | O | 워크로드 로드 밸런서 정보 |
| workload.loadBalancing.enabled | Body | Boolean | O | 워크로드 로드 밸런서 사용 여부 |
| workload.loadBalancing.floatingIp | Body | Boolean | O | 워크로드 로드 밸런서 플로팅 IP 사용 여부 |
| workload.loadBalancing.healthMonitor | Body | Object | X | 로드 밸런서의 상태 확인 정보 |
| workload.loadBalancing.healthMonitor.delay | Body | Integer | O | 상태 확인 주기 |
| workload.loadBalancing.healthMonitor.timeout | Body | Integer | O | 최대 응답 대기 시간 |
| workload.loadBalancing.healthMonitor.maxRetries | Body | Integer | O | 최대 재시도 횟수 |
| workload.loadBalancing.healthMonitor.httpMethod | Body | String | X | HTTP 메서드<ul><li>GET</li></ul> |
| workload.loadBalancing.healthMonitor.expectedCodes | Body | String | X | HTTP 상태 코드<ul><li>200</li><li>200,202</li><li>200-204</li></ul> |
| workload.loadBalancing.healthMonitor.urlPath | Body | String | X | HTTP URL |
| workload.loadBalancing.certificate | Body | String | X | TERMINATED\_HTTPS 사용 시 로드 밸런서에서 사용하는 인증서 |
| workload.loadBalancing.privateKey | Body | String | X | TERMINATED\_HTTPS 사용 시 로드 밸런서에서 사용하는 개인 키 |
| workload.loadBalancing.tlsVersion | Body | String | X | TERMINATED\_HTTPS 사용 시 TLS 버전<ul><li>SSLv3</li><li>TLSv1.0</li><li>TLSv1.0\_2016</li><li>TLSv1.1</li><li>TLSv1.2</li><li>TLSv1.3</li></ul> |
| workload.loadBalancing.containerHref | Body | String | X | 시크릿 컨테이너 ID<li>Load Balancer API를 이용하여 TERMINATED\_HTTPS에서 사용할 인증서,개인 키를 별도로 등록한 경우 사용</li> |
| workload.loadBalancing.ipAclGroupsBinding | Body | List | X | 로드 밸런서에 적용할 IP 접근 제어 그룹 목록 |
| workload.loadBalancing.ipAclGroupsBinding.ipAclGroupId | Body | String | O | IP 접근 제어 그룹 ID |
| workload.schedule | Body | Object | X | 예약 실행 설정 정보 |
| workload.schedule.timeZone | Body | String | O | 예약 실행 기준 시간<ul><li>예: Asia/Seoul, UTC</li><li>[List of tz database time zones](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)</li></ul> |
| workload.schedule.cron | Body | String | O | 예약 실행 Cron 표현식 |
| workload.schedule.jobsHistoryLimit | Body | Integer | O | 예약 실행 히스토리 보관 수 |
| workload.schedule.concurrencyPolicy | Body | String | O | 동시 실행 정책<ul><li>Forbid</li><li>Replace</li></ul> |
| workload.internalLoadBalancing | Body | Object | X | 내부 로드 밸런서 정보 |
| workload.internalLoadBalancing.enalbed | Body | Boolean | O | 내부 로드 밸런서 사용 여부 |
| workload.internalLoadBalancing.type | Body | String | X | 내부 로드 밸런서 IP 할당 방법<ul><li>dynamic: 자동 할당</li><li>static: IP 지정</li></ul> |
| workload.internalLoadBalancing.ip | Body | String | X | 내부 로드 밸런서 지정 IP |
| workload.activeDeadline | Body | Object | X | 워크로드 예약 종료 정보 |
| workload.activeDeadline.timeZone | Body | String | O | 예약 종료 기준 시간<li>예: Asia/Seoul, UTC</li> |
| workload.activeDeadline.timeOffset | Body | String | O | 예약 종료 기준 시간 Offset |
| workload.activeDeadline.time | Body | String | O | 예약 종료 시간 |
| workload.autoScaler | Body | Object | X | AutoScaler 설정 정보 |
| workload.autoScaler.scaleOut | Body | Object | O | ScaleOut 정보 |
| workload.autoScaler.scaleOut.enabled | Body | Boolean | O | ScaleOut 사용 여부 |
| workload.autoScaler.scaleOut.maxReplicas | Body | Integer | X | 오토 스케일링 최대 작업 수 |
| workload.autoScaler.scaleOut.coolDownMinute | Body | Integer | X | 증설 후 대기 시간 |
| workload.autoScaler.scaleOut.condition | Body | List | X | 증설 조건 |
| workload.autoScaler.scaleOut.condition.resource | Body | String | X | 증설 조건 기준 리소스<ul><li>cpu</li><li>memory</li></ul> |
| workload.autoScaler.scaleOut.condition.threshold | Body | Integer | X | 증설 조건 리소스 사용량(1~100) |
| workload.autoScaler.scaleOut.condition.duration | Body | Integer | X | 증설 조건 리소스 사용량 유지 시간(분) |
| workload.autoScaler.scaleIn | Body | Object | X | ScaleIn 정보 |
| workload.autoScaler.scaleIn.enabled | Body | Boolean | O | ScaleIn 사용 여부 |
| workload.autoScaler.scaleIn.minReplicas | Body | Integer | X | 오토 스케일링 최소 작업 수 |
| workload.autoScaler.scaleIn.coolDownMinute | Body | Integer | X | 감축 후 대기 시간 |
| workload.autoScaler.scaleIn.condition | Body | List | X | 감축 조건 |
| workload.autoScaler.scaleIn.condition.resource | Body | String | X | 감축 조건 기준 리소스<ul><li>cpu</li><li>memory</li></ul> |
| workload.autoScaler.scaleIn.condition.threshold | Body | Integer | X | 감축 조건 리소스 사용량(1~100) |
| workload.autoScaler.scaleIn.condition.duration | Body | Integer | X | 감축 조건 리소스 사용량 유지 시간(분) |
| workload.securityGroups | Body | List | X | SecurityGroups 정보 |
| workload.securityGroups.id | Body | String | O | SecurityGroups ID |

<details>
  <summary>예시</summary>

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

#### 응답

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| workloads | Body | Object | O | 워크로드 정보 |
| workload.id | Body | UUID | O | 워크로드 ID |
| workload.name | Body | String | O | 워크로드 이름 |
| workload.type | Body | String | O | 배포 컨트롤러<ul><li>deployment</li><li>statefulset</li></ul> |
| workload.templateId | Body | String | O | 워크로드의 템플릿 ID |
| workload.templateVersion | Body | String | O | 워크로드의 템플릿 버전 |
| workload.createdAt | Body | String | O | 생성 시간(UTC) |
| workload.desired | Body | Integer | O | 워크로드 작업 요청 수 |
| workload.loadBalancing | Body | Object | O | 워크로드 로드 밸런서 정보 |
| workload.loadBalancing.enabled | Body | Boolean | O | 워크로드 로드 밸런서 사용 여부 |
| workload.loadBalancing.floatingIp | Body | Boolean | O | 워크로드 로드 밸런서 플로팅 IP 사용 여부 |
| workload.loadBalancing.healthMonitor | Body | Object | X | 로드 밸런서의 상태 확인 정보 |
| workload.loadBalancing.healthMonitor.delay | Body | Integer | O | 상태 확인 주기 |
| workload.loadBalancing.healthMonitor.timeout | Body | Integer | O | 최대 응답 대기 시간 |
| workload.loadBalancing.healthMonitor.maxRetries | Body | Integer | O | 최대 재시도 횟수 |
| workload.loadBalancing.healthMonitor.httpMethod | Body | String | X | HTTP 메서드<ul><li>GET</li></ul> |
| workload.loadBalancing.healthMonitor.expectedCodes | Body | String | X | HTTP 상태 코드<ul><li>200</li><li>200,202</li><li>200-204</li></ul> |
| workload.loadBalancing.healthMonitor.urlPath | Body | String | X | HTTP URL |
| workload.loadBalancing.certificate | Body | String | X | TERMINATED\_HTTPS 사용 시 로드 밸런서에서 사용하는 인증서 |
| workload.loadBalancing.privateKey | Body | String | X | TERMINATED\_HTTPS 사용 시 로드 밸런서에서 사용하는 개인 키 |
| workload.loadBalancing.tlsVersion | Body | String | X | TERMINATED\_HTTPS 사용 시 TLS 버전<ul><li>SSLv3</li><li>TLSv1.0</li><li>TLSv1.0\_2016</li><li>TLSv1.1</li><li>TLSv1.2</li><li>TLSv1.3</li></ul> |
| workload.loadBalancing.containerHref | Body | String | X | 시크릿 컨테이너 ID<li>Load Balancer API를 이용하여 TERMINATED\_HTTPS에서 사용할 인증서,개인 키를 별도로 등록한 경우 사용</li> |
| workload.loadBalancing.ipAclGroupsBinding | Body | List | X | 로드 밸런서에 적용할 IP 접근 제어 그룹 목록 |
| workload.loadBalancing.ipAclGroupsBinding.ipAclGroupId | Body | String | O | IP 접근 제어 그룹 ID |
| workload.schedule | Body | Object | X | 예약 실행 설정 정보 |
| workload.schedule.timeZone | Body | String | O | 예약 실행 기준 시간<ul><li>예: Asia/Seoul, UTC</li><li>[List of tz database time zones](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)</li></ul> |
| workload.schedule.cron | Body | String | O | 예약 실행 Cron 표현식 |
| workload.schedule.jobsHistoryLimit | Body | Integer | O | 예약 실행 히스토리 보관 수 |
| workload.schedule.concurrencyPolicy | Body | String | O | 동시 실행 정책<ul><li>Forbid</li><li>Replace</li></ul> |
| workload.schedule.timeOffset | Body | String | O | 예약 실행 기준 시간 Offset |
| workload.internalLoadBalancing | Body | Object | X | 내부 로드 밸런서 정보 |
| workload.internalLoadBalancing.enalbed | Body | Boolean | O | 내부 로드 밸런서 사용 여부 |
| workload.internalLoadBalancing.type | Body | String | X | 내부 로드 밸런서 IP 할당 방법<ul><li>dynamic: 자동 할당</li><li>static: IP 지정</li></ul> |
| workload.internalLoadBalancing.ip | Body | String | X | 내부 로드 밸런서 지정 IP |
| workload.privateDns | Body | Object | X | Private DNS에 워크로드 작업 IP 등록 여부 결정 |
| workload.privateDns.ttl | Body | Integer | O | 레코드 세트의 TTL 값 |
| workload.privateDns.zoneId | Body | String | O | 워크로드에서 사용하는 Private DNS Zone ID |
| workload.privateDns.domain | Body | String | O | Private DNS에 등록된 도메인 정보 |
| workload.activeDeadline | Body | Object | X | 워크로드 예약 종료 정보 |
| workload.activeDeadline.timeZone | Body | String | O | 예약 종료 기준 시간<li>예: Asia/Seoul, UTC</li> |
| workload.activeDeadline.timeOffset | Body | String | O | 예약 종료 기준 시간 Offset |
| workload.activeDeadline.time | Body | String | O | 예약 종료 시간 |
| workload.autoScaler | Body | Object | X | AutoScaler 설정 정보 |
| workload.autoScaler.scaleOut | Body | Object | O | ScaleOut 정보 |
| workload.autoScaler.scaleOut.enabled | Body | Boolean | O | ScaleOut 사용 여부 |
| workload.autoScaler.scaleOut.maxReplicas | Body | Integer | X | 오토 스케일링 최대 작업 수 |
| workload.autoScaler.scaleOut.coolDownMinute | Body | Integer | X | 증설 후 대기 시간 |
| workload.autoScaler.scaleOut.condition | Body | List | X | 증설 조건 |
| workload.autoScaler.scaleOut.condition.resource | Body | String | X | 증설 조건 기준 리소스<ul><li>cpu</li><li>memory</li></ul> |
| workload.autoScaler.scaleOut.condition.threshold | Body | Integer | X | 증설 조건 리소스 사용량(1~100) |
| workload.autoScaler.scaleOut.condition.duration | Body | Integer | X | 증설 조건 리소스 사용량 유지 시간(분) |
| workload.autoScaler.scaleIn | Body | Object | X | ScaleIn 정보 |
| workload.autoScaler.scaleIn.enabled | Body | Boolean | O | ScaleIn 사용 여부 |
| workload.autoScaler.scaleIn.minReplicas | Body | Integer | X | 오토 스케일링 최소 작업 수 |
| workload.autoScaler.scaleIn.coolDownMinute | Body | Integer | X | 감축 후 대기 시간 |
| workload.autoScaler.scaleIn.condition | Body | List | X | 감축 조건 |
| workload.autoScaler.scaleIn.condition.resource | Body | String | X | 감축 조건 기준 리소스<ul><li>cpu</li><li>memory</li></ul> |
| workload.autoScaler.scaleIn.condition.threshold | Body | Integer | X | 감축 조건 리소스 사용량(1~100) |
| workload.autoScaler.scaleIn.condition.duration | Body | Integer | X | 감축 조건 리소스 사용량 유지 시간(분) |
| workload.securityGroups | Body | List | X | SecurityGroups 정보 |
| workload.securityGroups.id | Body | String | O | SecurityGroups ID |

<details>
  <summary>예시</summary>

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

### 워크로드 부분 변경하기

워크로드의 일부만 수정할 수 있습니다.

#### 요청

* 이 API를 사용하는 경우 Content-Type은 application/json-patch+json으로 설정해야 합니다.

```bash
PATCH /ncs/v1.0/appkeys/{appKey}/workloads/{workloadId}
Content-Type: application/json-patch+json
x-nhn-authorization: {token}
```

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| workloadId | URL | String | O | 워크로드 ID |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|
| op | Body | String | O | Operation<ul><li>Add</li><li>Remove</li><li>Replace</li><li>Copy</li><li>Move</li><li>Test</li></ul> |
| path | Body | String | O | 변경하는 데이터 경로 |
| value | Body | String | X | 변경 값 |
| from | Body | String | X | 기존 데이터 경로 |

<details>
   <summary>예시</summary>

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

#### 응답

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| workloads | Body | Object | O | 워크로드 정보 |
| workload.id | Body | UUID | O | 워크로드 ID |
| workload.name | Body | String | O | 워크로드 이름 |
| workload.type | Body | String | O | 배포 컨트롤러<ul><li>deployment</li><li>statefulset</li></ul> |
| workload.templateId | Body | String | O | 워크로드의 템플릿 ID |
| workload.templateVersion | Body | String | O | 워크로드의 템플릿 버전 |
| workload.createdAt | Body | String | O | 생성 시간(UTC) |
| workload.desired | Body | Integer | O | 워크로드 작업 요청 수 |
| workload.loadBalancing | Body | Object | O | 워크로드 로드 밸런서 정보 |
| workload.loadBalancing.enabled | Body | Boolean | O | 워크로드 로드 밸런서 사용 여부 |
| workload.loadBalancing.floatingIp | Body | Boolean | O | 워크로드 로드 밸런서 플로팅 IP 사용 여부 |
| workload.loadBalancing.healthMonitor | Body | Object | X | 로드 밸런서의 상태 확인 정보 |
| workload.loadBalancing.healthMonitor.delay | Body | Integer | X | 상태 확인 주기 |
| workload.loadBalancing.healthMonitor.timeout | Body | Integer | X | 최대 응답 대기 시간 |
| workload.loadBalancing.healthMonitor.maxRetries | Body | Integer | X | 최대 재시도 횟수 |
| workload.loadBalancing.healthMonitor.httpMethod | Body | String | X | HTTP 메서드<ul><li>GET</li></ul> |
| workload.loadBalancing.healthMonitor.expectedCodes | Body | String | X | HTTP 상태 코드<ul><li>200</li><li>200,202</li><li>200-204</li></ul> |
| workload.loadBalancing.healthMonitor.urlPath | Body | String | X | HTTP URL |
| workload.loadBalancing.certificate | Body | String | X | TERMINATED\_HTTPS 사용 시 로드 밸런서에서 사용하는 인증서 |
| workload.loadBalancing.privateKey | Body | String | X | TERMINATED\_HTTPS 사용 시 로드 밸런서에서 사용하는 개인 키 |
| workload.loadBalancing.tlsVersion | Body | String | X | TERMINATED\_HTTPS 사용 시 TLS 버전<ul><li>SSLv3</li><li>TLSv1.0</li><li>TLSv1.0\_2016</li><li>TLSv1.1</li><li>TLSv1.2</li><li>TLSv1.3</li></ul> |
| workload.loadBalancing.containerHref | Body | String | X | 시크릿 컨테이너 ID<li>Load Balancer API를 이용하여 TERMINATED\_HTTPS에서 사용할 인증서,개인 키를 별도로 등록한 경우 사용</li> |
| workload.loadBalancing.ipAclGroupsBinding | Body | List | X | 로드 밸런서에 적용할 IP 접근 제어 그룹 목록 |
| workload.loadBalancing.ipAclGroupsBinding.ipAclGroupId | Body | String | O | IP 접근 제어 그룹 ID |
| workload.schedule | Body | Object | X | 예약 실행 설정 정보 |
| workload.schedule.timeZone | Body | String | O | 예약 실행 기준 시간<ul><li>예: Asia/Seoul, UTC</li><li>[List of tz database time zones](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)</li></ul> |
| workload.schedule.cron | Body | String | O | 예약 실행 Cron 표현식 |
| workload.schedule.jobsHistoryLimit | Body | Integer | O | 예약 실행 히스토리 보관 수 |
| workload.schedule.concurrencyPolicy | Body | String | O | 동시 실행 정책<ul><li>Forbid</li><li>Replace</li></ul> |
| workload.schedule.timeOffset | Body | String | O | 예약 실행 기준 시간 Offset |
| workload.internalLoadBalancing | Body | Object | X | 내부 로드 밸런서 정보 |
| workload.internalLoadBalancing.enalbed | Body | Boolean | O | 내부 로드 밸런서 사용 여부 |
| workload.internalLoadBalancing.type | Body | String | X | 내부 로드 밸런서 IP 할당 방법<ul><li>dynamic: 자동 할당</li><li>static: IP 지정</li></ul> |
| workload.internalLoadBalancing.ip | Body | String | X | 내부 로드 밸런서 지정 IP |
| workload.privateDns | Body | Object | X | Private DNS에 워크로드 작업 IP 등록 여부 결정 |
| workload.privateDns.ttl | Body | Integer | O | 레코드 세트의 TTL 값 |
| workload.privateDns.zoneId | Body | String | O | 워크로드에서 사용하는 Private DNS Zone ID |
| workload.privateDns.domain | Body | String | O | Private DNS에 등록된 도메인 정보 |
| workload.activeDeadline | Body | Object | X | 워크로드 예약 종료 정보 |
| workload.activeDeadline.timeZone | Body | String | O | 예약 종료 기준 시간<li>예: Asia/Seoul, UTC</li> |
| workload.activeDeadline.timeOffset | Body | String | O | 예약 종료 기준 시간 Offset |
| workload.activeDeadline.time | Body | String | O | 예약 종료 시간 |
| workload.autoScaler | Body | Object | X | AutoScaler 설정 정보 |
| workload.autoScaler.scaleOut | Body | Object | O | ScaleOut 정보 |
| workload.autoScaler.scaleOut.enabled | Body | Boolean | O | ScaleOut 사용 여부 |
| workload.autoScaler.scaleOut.maxReplicas | Body | Integer | X | 오토 스케일링 최대 작업 수 |
| workload.autoScaler.scaleOut.coolDownMinute | Body | Integer | X | 증설 후 대기 시간 |
| workload.autoScaler.scaleOut.condition | Body | List | X | 증설 조건 |
| workload.autoScaler.scaleOut.condition.resource | Body | String | X | 증설 조건 기준 리소스<ul><li>cpu</li><li>memory</li></ul> |
| workload.autoScaler.scaleOut.condition.threshold | Body | Integer | X | 증설 조건 리소스 사용량(1~100) |
| workload.autoScaler.scaleOut.condition.duration | Body | Integer | X | 증설 조건 리소스 사용량 유지 시간(분) |
| workload.autoScaler.scaleIn | Body | Object | X | ScaleIn 정보 |
| workload.autoScaler.scaleIn.enabled | Body | Boolean | O | ScaleIn 사용 여부 |
| workload.autoScaler.scaleIn.minReplicas | Body | Integer | X | 오토 스케일링 최소 작업 수 |
| workload.autoScaler.scaleIn.coolDownMinute | Body | Integer | X | 감축 후 대기 시간 |
| workload.autoScaler.scaleIn.condition | Body | List | X | 감축 조건 |
| workload.autoScaler.scaleIn.condition.resource | Body | String | X | 감축 조건 기준 리소스<ul><li>cpu</li><li>memory</li></ul> |
| workload.autoScaler.scaleIn.condition.threshold | Body | Integer | X | 감축 조건 리소스 사용량(1~100) |
| workload.autoScaler.scaleIn.condition.duration | Body | Integer | X | 감축 조건 리소스 사용량 유지 시간(분) |
| workload.securityGroups | Body | List | X | SecurityGroups 정보 |
| workload.securityGroups.id | Body | String | O | SecurityGroups ID |

<details>
  <summary>예시</summary>

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

### 워크로드 중지
워크로드를 중지합니다.

```bash
POST /ncs/v1.0/appkeys/{appKey}/workloads/{workloadId}/pause
x-nhn-authorization: {token}
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| workloadId | URL | String | O | 템플릿 ID |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|

#### 응답
이 API는 공통 정보만 응답합니다.

### 워크로드 재시작
중지 상태의 워크로드를 재시작합니다.

```bash
POST /ncs/v1.0/appkeys/{appKey}/workloads/{workloadId}/resume
x-nhn-authorization: {token}
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| workloadId | URL | String | O | 워크로드 ID |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|

#### 응답
이 API는 공통 정보만 응답합니다.

### 워크로드 작업 재시작
워크로드의 작업을 재시작합니다.

```bash
POST /ncs/v1.0/appkeys/{appKey}/workloads/{workloadId}/tasks/{taskId}/restart
x-nhn-authorization: {token}
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| workloadId | URL | String | O | 워크로드 ID |
| taskId | URL | String | O | 작업 ID |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|

#### 응답
이 API는 공통 정보만 응답합니다.

### 워크로드 삭제하기

워크로드를 삭제합니다.

```bash
DELETE /ncs/v1.0/appkeys/{appKey}/workloads/{workloadId}
x-nhn-authorization: {token}
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| workloadId | URL | String | O | 워크로드 ID |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|

#### 응답

이 API는 공통 정보만 응답합니다.


## 응답 코드
| resultCode | resultMessage | 설명 |
| --- | --- | --- |
| 200 | SUCCESS | 요청 성공 |
| 10001 | Authentication error. | 유효하지 않은 AppKey입니다. |
| 10002 | Bad Request. | 유효하지 않은 값이 요청되었습니다. |
| 10003 | An error occurred while parsing the request body. | 요청 본문을 구문 분석하는 과정에서 오류가 발생했습니다. |
| 10004 | Internal server error. | 내부 서버 오류입니다. |
| 10005 | No permissions. | 권한이 없습니다. |
| 10006 | You have exceeded the maximum number of {{.Resource}} that can be created. Please contact the Customer Center to increase the limit. | 생성 가능한 {{.Resource}} 수를 초과하였습니다. |
| 10041 | Could not find the template. | 템플릿을 찾을 수 없습니다. |
| 10042 | Could not use the ECR. | ECR의 이미지는 사용할 수 없습니다. |
| 10043 | The network information does not exist. | 네트워크 정보가 존재하지 않습니다. |
| 10044 | The template in use by the workload cannot be deleted. | 워크로드에서 사용 중인 템플릿은 삭제할 수 없습니다. |
| 10045 | Duplicate container port exists in the template. | 템플릿에 동일한 컨테이너 포트가 존재합니다. |
| 10046 | Template with the same name already exists. | 동일한 이름의 템플릿이 이미 존재합니다. |
| 10048 | Failed to download ConfigMaps. | 컨피그맵 다운로드에 실패하였습니다. |
| 10049 | ConfigMaps can only use the object storage from the same organization. | 컨피그맵은 동일한 조직의 Object Storage만 사용할 수 있습니다. |
| 10050 | The total size of the template's ConfigMap cannot exceed 1 MiB. | 템플릿의 컨피그맵 총 크기는 1MiB를 초과할 수 없습니다. |
| 10051 | Failed to download secrets from Secure Key Manager. | Secure Key Manager에서 시크릿 다운로드에 실패하였습니다. |
| 10052 | Could not create a template that consists only of init containers. | 초기화 컨테이너로만 구성된 템플릿은 생성할 수 없습니다. |
| 10053 | The {{.Resource}} of an init container must be less than the sum of the normal containers. | 초기화 컨테이너의 {{.Resource}}는 일반 컨테이너의 합보다 작아야 합니다. |
| 10061 | Could not find the workload. | 워크로드를 찾을 수 없습니다. |
| 10062 | Task does not exist. | 작업이 존재하지 않습니다. |
| 10063 | You cannot use the load balancer because the container port is not specified in the template. | 템플릿에 컨테이너 포트가 지정되지 않아 로드 밸런서를 사용할 수 없습니다. |
| 10064 | A workload with the same name already exists. | 동일한 이름의 워크로드가 이미 존재합니다. |
| 10065 | Invalid container specifications in the template. | 템플릿의 컨테이너 사양이 유효하지 않습니다. |
| 10066 | Could not create a workload due to insufficient resources. | 리소스가 부족하여 워크로드를 생성할 수 없습니다. |
| 10067 | You cannot change the workload name. | 워크로드 이름은 변경할 수 없습니다. |
| 10068 | You cannot change to the template that uses a different network. | 다른 네트워크를 사용하는 템플릿으로 변경할 수 없습니다. |
| 10069 | In the template, you must set the same container port as the port used by the load balancer. | 로드 밸런서에서 사용 중인 포트와 동일한 컨테이너 포트가 템플릿에 설정되어야 합니다. |
|	10070 | You cannot use the load balancer in legacy network environments. | 레거시 네트워크 환경에서는 로드 밸런서를 사용할 수 없습니다. |
|	10071 | UDP protocol cannot use load balancer. | UDP 프로토콜은 로드 밸런서를 사용할 수 없습니다. |
|	10072 | If the workload runs less than {{.Minutes}} minutes, you cannot use the load balancer. | 워크로드 실행 주기가 {{.Minutes}}분 이하인 경우 로드 밸런서를 이용할 수 없습니다. |
|	10073 | Invalid cron expression. | 유효하지 않은 cron 표현식입니다. |
|	10074 | Invalid time zone. | 유효하지 않은 Timezone입니다. |
|	10075 | You cannot change the load balancer to Use while the workload is stopped. | 워크로드 중지 상태에서는 로드 밸런서를 사용으로 변경할 수 없습니다. |
|	10076 | The certificate and private key are invalid. To use TERMINATED_HTTPS, you must register a valid certificate and private key. | 인증서와 개인 키가 올바르지 않습니다. TERMINATED_HTTPS를 사용하려면 유효한 인증서와 개인 키를 등록해야 합니다. |
|	10077 | Only one security group can be used. | 하나의 Security Groups만 사용할 수 있습니다. |
|	10078 | The actions of all groups must be the same. | 그룹들의 action은 모두 동일해야 합니다. |
|	10079 | Invalid Private DNS zone. | 유효하지 않은 Private DNS zone입니다. |
|	10080 | Could not change the load balancer to Enabled while the workload is terminated. | 워크로드 종료 상태에서는 로드 밸런서를 사용으로 변경할 수 없습니다.  |
|	10081 | The task is not in a restartable state. | 작업이 재시작 가능한 상태가 아닙니다. |