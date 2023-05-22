## Container > NHN Container Service(NCS) > API Guide

This document explains the APIs required for configuring workloads and templates.

### API Common Request Information

To use the APIs, the service Appkey is required.The service Appkey is located in the <strong>URL & Appkey<strong> menu on the top of the console.
The API domain is as follows.

| Region | Domain |
| --- | --- |
| Korea (Pangyo) region | [http://kr1-ncs.api.nhncloudservice.com](http://kr1-ncs.api.nhncloudservice.com) |

### API Common Response Information

Returns <strong>200 OK<strong> for all API requests. For more information on the response results, see Response Body Header.

| Name | Type | Format | Description |
| --- | --- | --- | --- |
| header | Body | Object | API response information |
| header.isSuccessful | Body | Boolean | true: normal<br>false: error |
| header.resultCode | Body | Integer | 200: normal<br>10000 or higher: error |
| header.resultMessage | Body | String | "SUCCESS": normal<br>others: error cause message |

<details><summary>Example</summary>

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

> [Caution]
In each API response, you may find fields that are not specified within this guide. Those fields are for NHN Cloud internal usage, and as such refrain from using them since they may be changed without prior notice.

## Template

### View Template List

Retrieves the list of templates.

```
GET /ncs/v1.0/appkeys/{appKey}/templates?page=1&size=30
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| page | Query | Integer | X | Page number to retrieve |
| size | Query | Integer | X | Page size to retrieve (default: 10) |

#### Response

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| X-Total-Count | Header | Integer | O | Number of templates created in Appkey |
| templates | Body | Array | O | Template list |
| templates.createdAt | Body | String | O | Created time (UTC) |
| templates.id | Body | UUID | O | Template ID |
| templates.name | Body | String | O | Template name |
| templates.description | Body | String | X | Template description |
| templates.containers | Body | Array | O | List of containers in a template |
| templates.containers.name | Body | String | O | Container name |
| templates.containers.image | Body | String | O | Container image |
| templates.containers.cpus | Body | Float | O | Number of CPU assigned to container |
| templates.containers.memoryLimit | Body | Object | O | Information on memory assigned to container |
| templates.containers.memoryLimit.hard | Body | Integer | O | Memory assigned to container (MiB) |
| templates.containers.gpus | Body | Integer | X | Whether the container uses a GPU<br>0: Not used<br>1: Used |
| templates.containers.gpuFlavor | Body | String | X | Information on GPU Flavor<br>\* ncs1.g1m5<br>\* ncs1.g2m10 |
| templates.containers.ports | Body | Array | X | Information on ports used in containers |
| templates.containers.ports.containerPort | Body | Integer | O | Container port |
| templates.containers.ports.protocol | Body | String | O | Container protocol |
| templates.containers.command | Body | String List | X | Command to run when container starts |
| templates.containers.env | Body | Array | X | Container environment variable |
| templates.containers.env.name | Body | String | O | Container environment variable name |
| templates.containers.env.value | Body | String | O | Container environment variable value |
| templates.containers.volumes | Body | Array | X | Information on storage used in containers |
| templates.containers.volumes.name | Body | String | O | Storage Name |
| templates.containers.volumes.path | Body | String | O | NAS Storage Connection Path |
| templates.containers.volumes.mountPath | Body | String | X | Container storage connection path |
| templates.containers.workDirectory | Body | String | X | Task Directory of Container |
| templates.networks | Body | Array | O | Template network information |
| templates.networks.vpcId | Body | String | O | VPC ID of template |
| templates.networks.subnetId | Body | String | O | Subnet ID of template |

<details><summary>Example</summary>

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

### View Template

Retrieves information of an individual template.

```
GET /ncs/v1.0/appkeys/{appKey}/templates/{templateId}
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| templateId | URL | String | O | Template ID |

#### Response

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| templates | Body | Object | O | Template information |
| templates.createdAt | Body | String | O | Created time (UTC) |
| templates.id | Body | UUID | O | Template ID |
| templates.name | Body | String | O | Template name |
| templates.description | Body | String | X | Template description |
| templates.containers | Body | Array | O | List of containers in a template |
| templates.containers.name | Body | String | O | Container name |
| templates.containers.image | Body | String | O | Container image |
| templates.containers.cpus | Body | Float | O | Number of CPU assigned to container |
| templates.containers.memoryLimit | Body | Object | O | Information on memory assigned to container |
| templates.containers.memoryLimit.hard | Body | Integer | O | Memory assigned to container (MiB) |
| templates.containers.gpus | Body | Integer | X | Whether the container uses a GPU<br>0: Not used<br>1: Used |
| templates.containers.gpuFlavor | Body | String | X | Information on GPU Flavor<br>\* ncs1.g1m5<br>\* ncs1.g2m10 |
| templates.containers.ports | Body | Array | X | Information on ports used in containers |
| templates.containers.ports.containerPort | Body | Integer | O | Container port |
| templates.containers.ports.protocol | Body | String | O | Container protocol |
| templates.containers.command | Body | String List | X | Command to run when container starts |
| templates.containers.env | Body | Array | X | Container environment variable |
| templates.containers.env.name | Body | String | O | Container environment variable name |
| templates.containers.env.value | Body | String | O | Container environment variable value |
| templates.containers.volumes | Body | Array | X | Information on storage used in containers |
| templates.containers.volumes.name | Body | String | O | Storage Name |
| templates.containers.volumes.path | Body | String | O | NAS Storage Connection Path |
| templates.containers.volumes.mountPath | Body | String | X | Container storage connection path |
| templates.containers.workDirectory | Body | String | X | Task Directory of Container |
| templates.networks | Body | Array | O | Template network information |
| templates.networks.vpcId | Body | String | O | VPC ID of template |
| templates.networks.subnetId | Body | String | O | Subnet ID of template |

<details><summary>Example</summary>

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

### Create Template

Creates a template.

```
POST /ncs/v1.0/appkeys/{appKey}/templates
Content-Type: application/json
```

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| templates | Body | Object | O | Template information |
| templates.name | Body | String | O | Template name |
| templates.description | Body | String | X | Template description |
| templates.containers | Body | Array | O | List of containers in a template |
| templates.containers.name | Body | String | O | Container name |
| templates.containers.image | Body | String | O | Container image |
| templates.containers.imageRegistryCredentials | Body | Object | X | Information accessible to private registries |
| templates.containers.imageRegistryCredentials.username | Body | String | O | Private registry ID |
| templates.containers.imageRegistryCredentials.password | Body | String | O | Private registry password |
| templates.containers.cpus | Body | Float | O | Number of CPU assigned to container |
| templates.containers.memoryLimit | Body | Object | O | Information on memory assigned to container |
| templates.containers.memoryLimit.hard | Body | Integer | O | Memory assigned to container (MiB) |
| templates.containers.gpus | Body | Integer | X | Whether the container uses a GPU<br>0: Not used<br>1: Used |
| templates.containers.gpuFlavor | Body | String | X | Information on GPU Flavor<br>\* ncs1.g1m5<br>\* ncs1.g2m10 |
| templates.containers.ports | Body | Array | X | Information on ports used in containers |
| templates.containers.ports.containerPort | Body | Integer | O | Container port |
| templates.containers.ports.protocol | Body | String | O | Container protocol |
| templates.containers.command | Body | String List | X | Command to run when container starts |
| templates.containers.env | Body | Array | X | Container environment variable |
| templates.containers.env.name | Body | String | O | Container environment variable name |
| templates.containers.env.value | Body | String | O | Container environment variable value |
| templates.containers.volumes | Body | Array | X | Information on storage used in containers |
| templates.containers.volumes.name | Body | String | O | Storage Name |
| templates.containers.volumes.path | Body | String | O | NAS Storage Connection Path |
| templates.containers.volumes.mountPath | Body | String | X | Container storage connection path |
| templates.containers.workDirectory | Body | String | X | Task Directory of Container |
| templates.networks | Body | Array | O | Template network information |
| templates.networks.vpcId | Body | String | O | VPC ID of template<br>For more information on VPC, see[View VPC List](Network/VPC/ko/public-api/#vpc_1). |
| templates.networks.subnetId | Body | String | O | Subnet ID of template<br>For more information on Subnet, see [View VPC Subnets](/Network/VPC/ko/public-api/#vpc_7). |

<details><summary>Example</summary>

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

#### Response

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| templates | Body | Object | O | Template information |
| templates.createdAt | Body | String | O | Created time (UTC) |
| templates.id | Body | UUID | O | Template ID |
| templates.name | Body | String | O | Template name |
| templates.description | Body | String | X | Template description |
| templates.containers | Body | Array | O | List of containers in a template |
| templates.containers.name | Body | String | O | Container name |
| templates.containers.image | Body | String | O | Container image |
| templates.containers.cpus | Body | Float | O | Number of CPU assigned to container |
| templates.containers.memoryLimit | Body | Object | O | Information on memory assigned to container |
| templates.containers.memoryLimit.hard | Body | Integer | O | Memory assigned to container (MiB) |
| templates.containers.gpus | Body | Integer | X | Whether the container uses a GPU<br>0: Not used<br>1: Used |
| templates.containers.gpuFlavor | Body | String | X | Information on GPU Flavor<br>\* ncs1.g1m5<br>\* ncs1.g2m10 |
| templates.containers.command | Body | String List | X | Command to run when container starts |
| templates.containers.env | Body | Array | X | Container environment variable |
| templates.containers.env.name | Body | String | O | Container environment variable name |
| templates.containers.env.value | Body | String | O | Container environment variable value |
| templates.containers.volumes | Body | Array | X | Information on storage used in containers |
| templates.containers.volumes.name | Body | String | O | Storage Name |
| templates.containers.volumes.path | Body | String | O | NAS Storage Connection Path |
| templates.containers.volumes.mountPath | Body | String | X | Container storage connection path |
| templates.containers.workDirectory | Body | String | X | Task Directory of Container |
| templates.networks | Body | Array | O | Template network information |
| templates.networks.vpcId | Body | String | O | VPC ID of template |
| templates.networks.subnetId | Body | String | O | Subnet ID of template |

<details><summary>Example</summary>

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

### Delete Template

Deletes a template.

```
DELETE /ncs/v1.0/appkeys/{appKey}/templates/{templateId}
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| templateId | URL | String | O | Template ID |

#### Response

This API only responds with common information.

## Workload

### List Workloads

Retrieves a list of workloads.

```
GET /ncs/v1.0/appkeys/{appKey}/workloads?page=1&size=30
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| page | Query | Integer | X | Page number to retrieve |
| size | Query | Integer | X | Page size to retrieve (default: 10) |

#### Response

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| X-Total-Count | Header | Integer | O | Number of workloads created in Appkey |
| workloads | Body | Array | O | List of workloads |
| workloads.createdAt | Body | String | O | Created time (UTC) |
| workloads.id | Body | UUID | O | Workload ID |
| workloads.name | Body | String | O | Workload name |
| workloads.templateId | Body | String | O | Template ID of workloads |
| workloads.url | Body | String | X | URL of workload load balancer |
| workloads.desired | Body | Integer | O | Number of workload tasks requested |
| workloads.available | Body | Integer | O | Number of workload tasks executed |
| workloads.status | Body | Integer | O | Workload status |
| workloads.loadBalancing | Body | Object | O | Information on workload load balancer |
| workloads.loadBalancing.enabled | Body | Boolean | O | Whether to use workload load balancer |
| workloads.loadBalancing.floatingIp | Body | Boolean | O | Whether to use the workload load balancer floating IP |

<details><summary>Example</summary>

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

### View Workload

Retrieves an individual workload.

```
GET /ncs/v1.0/appkeys/{appKey}/workloads/{workloadId}
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| workloadId | URL | String | O | Workload ID |

#### Response

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| X-Total-Count | Header | Integer | O | Number of workloads created in Appkey |
| workloads | Body | Array | O | List of workloads |
| workloads.createdAt | Body | String | O | Created time (UTC) |
| workloads.id | Body | UUID | O | Workload ID |
| workloads.name | Body | String | O | Workload name |
| workloads.templateId | Body | String | O | Template ID of workloads |
| workloads.url | Body | String | X | URL of workload load balancer |
| workloads.desired | Body | Integer | O | Number of workload tasks requested |
| workloads.available | Body | Integer | O | Number of workload tasks executed |
| workloads.status | Body | String | O | Workload status |
| workloads.tasks | Body | Array | O | Tasks of workload |
| workloads.tasks.id | Body | UUID | O | Task ID |
| workloads.tasks.containers | Body | Array | O | List of containers in a task |
| workloads.tasks.containers.name | Body | String | O | Container name |
| workloads.tasks.containers.image | Body | String | O | Container image |
| workloads.tasks.containers.ip | Body | String | X | Container IP |
| workloads.tasks.containers.cpus | Body | Float | O | Number of CPU assigned to container |
| workloads.tasks.containers.memoryLimit | Body | Object | O | Information on memory assigned to container |
| workloads.tasks.containers.memoryLimit.hard | Body | Integer | O | Memory assigned to container (MiB) |
| workloads.tasks.containers.gpus | Body | Integer | X | Whether the container uses a GPU<br>0: Not used<br>1: Used |
| workloads.tasks.containers.gpuFlavor | Body | String | X | Information on GPU Flavor assigned to container |
| workloads.tasks.containers.ports | Body | Array | X | Container port information |
| workloads.tasks.containers.ports.containerPort | Body | Integer | O | Container port |
| workloads.tasks.containers.ports.protocol | Body | String | O | Container protocol |
| workloads.tasks.containers.command | Body | String List | X | Command to run when container starts |
| workloads.tasks.containers.env | Body | Array | X | Container environment variable |
| workloads.tasks.containers.env.name | Body | String | O | Container environment variable name |
| workloads.tasks.containers.env.value | Body | String | O | Container environment variable value |
| workloads.tasks.containers.volumes | Body | Array | X | Information on storage used in containers |
| workloads.tasks.containers.volumes.name | Body | String | O | Storage Name |
| workloads.tasks.containers.volumes.path | Body | String | O | NAS Storage Connection Path |
| workloads.tasks.containers.volumes.mountPath | Body | String | X | Container storage connection path |
| workloads.tasks.containers.workDirectory | Body | String | X | Task Directory of Container |
| workloads.tasks.containers.state | Body | String | O | Container status |
| workloads.tasks.containers.startedAt | Body | String | X | Container start time |
| workloads.tasks.containers.restartCount | Body | String | O | The number of time that container restarted |
| workloads.loadBalancing | Body | Object | O | Information on workload load balancer |
| workloads.loadBalancing.enabled | Body | Boolean | O | Whether to use workload load balancer |
| workloads.loadBalancing.floatingIp | Body | Boolean | O | Whether to use the workload load balancer floating IP |
| workloads.loadBalancing.ipAddress | Body | String | X | Workload IP, Floating IP |
| workloads.networks | Body | Array | O | Workload network information |
| workloads.networks.vpcId | Body | String | O | VPC ID of workload |
| workloads.networks.subnetId | Body | String | O | Subnet ID of workload |
| workloads.securityGroups | Body | String | X | Security group information of workload |
| workloads.securityGroups.id | Body | String | O | Security group ID of workload |

<details><summary>Example</summary>

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

### View Workload Log

Retrieves the container logs for your workload.

```
GET /ncs/v1.0/appkeys/{appKey}/workloads/{workloadId}/tasks/{taskId}/logs?container={ContainerName}&from={YYYY-MM-DDThh:mm:ssZ}&to={YYYY-MM-DDThh:mm:ssZ}
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| container | Query | String | O | Container name |
| limitBytes | Query | Integer | X | Maximum log size to query<br>default: 100 KB<br>Maximum: 10 MB |
| from | Query | String | X | Log start time<br>default: 5 minutes ago |
| to | Query | String | X | Log end time<br>default: Current time |

#### Response

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| logs | Body | Array | O | Container log list |
| log | Body | String | O | Log level |
| logType | Body | String | O | Log occurrence time (UTC) |

<details><summary>Example</summary>

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

### View Workload Event

Retrieves an workload event.

```
GET /ncs/v1.0/appkeys/{appKey}/workloads/{workloadId}/tasks/{taskId}/events?page=1&size=30
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| type | Query | Integer | X | Event type<br>Normal<br>Warning |
| q | Query | String | X | Filtering event content |
| page | Query | String | X | Page to search |
| size | Query | String | X | Page size to retrieve (default: 10) |

#### Response

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| events | Body | Array | O | Events |
| type | Body | String | O | Event type<br>Normal<br>Warning |
| reason | Body | String | O | Cause of the event |
| message | Body | String | O | Event content |
| createTimestamp | Body | String | O | Date of first occurrence of event (UTC) |
| lastTimestamp | Body | String | O | Date of last occurrence of event (UTC) |
| count | Body | Integer | O | Number of times event occurred |

<details><summary>Example</summary>

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

### View a List of Workload Run History

Retrieves a list of workload run history.

```
GET /ncs/v1.0/appkeys/{appKey}/workloads/{workloadId}/history?page=1&size=30
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| page | Query | Integer | X | Page number to retrieve |
| size | Query | Integer | X | Page size to retrieve (default: 10) |

#### Response

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| history | Body | Array | O | History list |
| history.id | Body | Integer | O | History ID |
| history.createdAt | Body | String | O | Deployment time |
| history.deletedAt | Body | String | O | End time |
| history.templateId | Body | UUID | O | Template ID used by workloads |
| history.name | Body | String | O | Template name |
| history.status | Body | String | O | Status<br>Succeeded<br>Terminated<br>Pending |

<details><summary>Example</summary>

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

### View Workload Run History

Retrieves a run history for an individual workload.

```
GET /ncs/v1.0/appkeys/{appKey}/workloads/{workloadId}/history/{historyId}
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| historyId | URL | Integer | O | History ID |

#### Response

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| history | Body | Object | O | History Information |
| history.id | Body | Integer | O | History ID |
| history.createdAt | Body | String | O | Execution time |
| history.deletedAt | Body | String | O | End time |
| history.templateId | Body | UUID | O | Template ID used by workloads |
| history.name | Body | String | O | Template name used by workloads |
| history.status | Body | String | O | Status<br>Succeeded<br>Terminated<br>Pending |
| templates | Body | Object | O | Template information |
| templates.createdAt | Body | String | O | Template creation time (UTC) |
| templates.deletedAt | Body | String | O | Template deletion time (UTC) |
| templates.id | Body | UUID | O | Template ID |
| templates.name | Body | String | O | Template name |
| templates.description | Body | String | X | Template description |
| templates.containers | Body | Array | O | List of containers in a template |
| templates.containers.name | Body | String | O | Container name |
| templates.containers.image | Body | String | O | Container image |
| templates.containers.cpus | Body | Float | O | Number of CPU assigned to container |
| templates.containers.memoryLimit | Body | Object | O | Information on memory assigned to container |
| templates.containers.memoryLimit.hard | Body | Integer | O | Memory assigned to container (MiB) |
| templates.containers.gpus | Body | Integer | X | Whether the container uses a GPU<br>0: Not used<br>1: Used |
| templates.containers.gpuFlavor | Body | String | X | Information on GPU Flavor assigned to container |
| templates.containers.ports | Body | Array | X | Information on ports used in containers |
| templates.containers.ports.containerPort | Body | Integer | O | Container port |
| templates.containers.ports.protocol | Body | String | O | Container protocol |
| templates.containers.command | Body | String List | X | Command to run when container starts |
| templates.containers.env | Body | Array | X | Container environment variable |
| templates.containers.env.name | Body | String | O | Container environment variable name |
| templates.containers.env.value | Body | String | O | Container environment variable value |
| templates.containers.volumes | Body | Array | X | Information on storage used in containers |
| templates.containers.volumes.name | Body | String | O | Storage Name |
| templates.containers.volumes.path | Body | String | O | NAS Storage Connection Path |
| templates.containers.volumes.mountPath | Body | String | X | Container storage connection path |
| templates.containers.workDirectory | Body | String | X | Task Directory of Container |
| templates.networks | Body | Array | O | Template network information |
| templates.networks.vpcId | Body | String | O | VPC ID of template |
| templates.networks.subnetId | Body | String | O | Subnet ID of template |

<details><summary>Example</summary>

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

### Create Workload

Creates a workload.

```
POST /ncs/v1.0/appkeys/{appKey}/workloads
Content-Type: application/json
```

#### Request

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| workload | Body | Object | O | Workload information |
| workload.desired | Body | Integer | O | Number of workload tasks requested |
| workload.loadBalancing | Body | Object | O | Information on workload load balancer |
| workload.loadBalancing.enabled | Body | Boolean | O | Whether to use workload load balancer |
| workload.loadBalancing.floatingIp | Body | Boolean | O | Whether to use the workload load balancer floating IP |
| workload.name | Body | String | O | Workload name |
| workload.templateId | Body | String | O | Template ID of workloads |

<details><summary>Example</summary>

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

#### Response

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| workloads | Body | Object | O | Workload information |
| workloads.createdAt | Body | String | O | Created time (UTC) |
| workloads.id | Body | UUID | O | Workload ID |
| workloads.name | Body | String | O | Workload name |
| workloads.templateId | Body | String | O | Template ID of workloads |
| workloads.url | Body | String | X | URL of workload load balancer |
| workloads.desired | Body | Integer | O | Number of workload tasks requested |
| workloads.loadBalancing | Body | Object | O | Information on workload load balancer |
| workloads.loadBalancing.enabled | Body | Boolean | O | Whether to use workload load balancer |
| workloads.loadBalancing.floatingIp | Body | Boolean | O | Whether to use the workload load balancer floating IP |

<details><summary>Example</summary>

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

### Change Workload

Changes a workload.

```
PUT /ncs/v1.0/appkeys/{appKey}/workloads/{workloadId}
Content-Type: application/json
```

#### Request

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| workloadId | URL | String | O | Workload ID |
| workload.desired | Body | Integer | O | Number of workload tasks requested |
| workload.loadBalancing | Body | Object | O | Information on workload load balancer |
| workload.loadBalancing.enabled | Body | Boolean | O | Whether to use workload load balancer |
| workload.loadBalancing.floatingIp | Body | Boolean | O | Whether to use the workload load balancer floating IP |
| workload.name | Body | String | O | Workload name |
| workload.templateId | Body | String | O | Template ID of workloads |

<details><summary>Example</summary>

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

#### Response

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| workloads | Body | Object | O | Workload information |
| workloads.createdAt | Body | String | O | Created time (UTC) |
| workloads.id | Body | UUID | O | Workload ID |
| workloads.name | Body | String | O | Workload name |
| workloads.templateId | Body | String | O | Template ID of workloads |
| workloads.url | Body | String | X | URL of workload load balancer |
| workloads.desired | Body | Integer | O | Number of workload tasks requested |
| workloads.loadBalancing | Body | Object | O | Information on workload load balancer |
| workloads.loadBalancing.enabled | Body | Boolean | O | Whether to use workload load balancer |
| workloads.loadBalancing.floatingIp | Body | Boolean | O | Whether to use the workload load balancer floating IP |

<details><summary>Example</summary>

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

### Delete Workload

Deletes a workload.

```
DELETE /ncs/v1.0/appkeys/{appKey}/workloads/{workloadId}
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| workloadId | URL | String | O | Workload ID |

#### Response

This API only responds with common information.


## Response code
| resultCode | resultMessage | Description |
| --- | --- | --- |
| 200 | SUCCESS | Successful request |
| 10001 | Authentication error. | Invalid Appkey. |
| 10002 | Bad Request. | An invalid value is requested. |
| 10003 | An error occurred while parsing the request body. | An error occurred while parsing the request body. |
| 10004 | Internal server error. |  |
| 10006 | You have exceeded the maximum number of {{.Resource}} that can be created. Please contact the Customer Center to increase the limit. | Exceeded the maximum number of {{.Resource}} that can be created. |
| 10041 | Could not find the template. | Could not find the template. |
| 10042 | Could not use the ECR. | Could not use the ECR. |
| 10043 | The network information does not exist. | The network information does not exist. |
| 10044 | The template in use by the workload cannot be deleted. | The template in use by the workload cannot be deleted. |
| 10045 | Duplicate container port exists in the template. | Duplicate container port exists in the template. |
| 10046 | Template with the same name already exists. | Template with the same name already exists. |
| 10047 | Resource {{.gpuFlavor}} is not available. If you want to use, please contact the Customer Center. | Resource {{.gpuFlavor}} is not available. |
| 10061 | Could not find the workload. | Could not find the workload. |
| 10062 | Pod does not exist. | Pod does not exist. |
| 10063 | You cannot use the load balancer because the container port is not specified in the template. | You cannot use the load balancer because the container port is not specified in the template. |
| 10064 | A workload with the same name already exists. | A workload with the same name already exists. |
| 10065 | Invalid container specifications in the template. | Invalid container specifications in the template. |
| 10066 | Could not create a workload due to insufficient resources. | Could not create a workload due to insufficient resources. |
| 10067 | You cannot change the workload name. | You cannot change the workload name. |
| 10068 | You cannot change to the template that uses a different network. | You cannot change to the template that uses a different network. |
| 10069 | In the template, you must set the same container port as the port used by the load balancer. | In the template, you must set the same container port as the port used by the load balancer. |
