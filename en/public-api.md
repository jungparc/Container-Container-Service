## Container > NHN Container Service(NCS) > API Guide

This document explains the APIs required for configuring workloads and templates.
To use the APIs, you need an API endpoint and a token. See [API Call and Authentication](/nhncloud/en/public-api/api-authentication)to prepare a token to use the API.

The API domain is as follows.

| Region | Domain |
| --- | --- |
| Korea (Pangyo) region | https://kr1-ncs.api.nhncloudservice.com |

### API Common Response Information

Returns <strong>200 OK<strong> for all API requests. For more information on the response results, see Response Body Header.

| Name | Type | Format | Description |
| --- | --- | --- | --- |
| header | Body | Object | API response information |
| header.isSuccessful | Body | Boolean | <li>true: normal</li><li>false: error</li> |
| header.resultCode | Body | Integer | <li>200: normal</li><li>10000 or higher: error</li> |
| header.resultMessage | Body | String | <li>SUCCESS: Normal</li><li>others: error cause message</li> |

<details>
  <summary>Example</summary>

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
> In each API response, you may find fields that are not specified within this guide. Those fields are for NHN Cloud internal usage, and as such refrain from using them since they may be changed without prior notice.

## Template

### View Template List

Retrieves the list of templates.

```bash
GET /ncs/v1.0/appkeys/{appKey}/templates
x-nhn-authorization: {token}
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|
| page | Query | Integer | X | Page number to retrieve |
| size | Query | Integer | X | Page size to retrieve (default: 10) |
| disable_containers | Query | Boolean | X | <li>true: Retrieve excluding containers</li><li>false: Retrieve including containers (default)</li> |

#### Response

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| X-Total-Count | Header | Integer | O | Number of templates created in Appkey |
| templates | Body | Array | O | Template list |
| templates.id | Body | UUID | O | Template ID |
| templates.version | Body | String | O | Template version |
| templates.name | Body | String | O | Template name |
| templates.createdAt | Body | String | O | Created time (UTC) |
| templates.description | Body | String | X | Template description |
| templates.versionDescription | Body | String | O | Template version description |
| templates.networks | Body | Array | O | Template network information |
| templates.networks.vpcId | Body | String | O | VPC ID of template |
| templates.networks.subnetId | Body | String | O | Subnet ID of template |
| templates.dnsConfig | Body | String List | X | DNS server information set up in the container |
| templates.hostAliases | Body | List | X | Information set up in container `/etc/hosts` |
| templates.hostAliases.ip | Body | String | O | IP of the hostnames set in the container |
| templates.hostAliases.hostnames | Body | String List | O | hostnames of the IP set in the container |
| templates.versionCount | Body | Integer | O | Number of versions of the template |
| templates.workloadCount | Body | Integer | O | Number of workloads using the template |
| templates.containers | Body | Array | O | List of containers in a template |
| templates.containers.name | Body | String | O | Container name |
| templates.containers.type | Body | String | O | Container Type<ul><li>normal: Normal</li><li>init: Initialize</li></ul>|
| templates.containers.image | Body | String | O | Container image |
| templates.containers.cpus | Body | Float | O | Number of CPU assigned to container |
| templates.containers.memoryLimit | Body | Object | O | Information on memory assigned to container |
| templates.containers.memoryLimit.hard | Body | Integer | O | Memory assigned to container (MiB) |
| templates.containers.gpuFlavor | Body | String | X | Information on GPU Flavor<ul><li>ncs1.g1m5</li><li>ncs1.g2m10</li></ul> |
| templates.containers.ports | Body | Array | X | Information on ports used in containers |
| templates.containers.ports.containerPort | Body | Integer | O | Container port |
| templates.containers.ports.protocol | Body | String | O | Container protocol<ul><li>TCP</li><li>UDP</li><li>HTTP</li><li>HTTPS</li><li>TERMINATED_HTTPS</li></ul> |
| templates.containers.command | Body | String List | X | Command to run when container starts |
| templates.containers.args | Body | String List | X | Arguments used when the container is started |
| templates.containers.workDirectory | Body | String | X | Task Directory of Container |
| templates.containers.env | Body | Array | X | Container environment variable |
| templates.containers.env.name | Body | String | O | Container environment variable name |
| templates.containers.env.value | Body | String | O | Container environment variable value |
| templates.containers.postStart | Body | String List | X | Commands that run immediately after container creation |
| templates.containers.preStop | Body | String List | X | Commands that run immediately after container termination |
| templates.containers.configs | Body | List | X | ConfigMap information used by containers |
| templates.containers.configs.id | Body | Integer | O | ConfigMap ID |
| templates.containers.configs.type | Body | String | O | Service type to get ConfigMap information<ul><li>obs: Object Storage</li></ul> |
| templates.containers.configs.value | Body | String | O | Object URL |
| templates.containers.configs.mountPath | Body | String | O | Container mount path |
| templates.containers.secrets | Body | List | X | Secret information used by containers |
| templates.containers.secrets.type | Body | String | O | Service type to get secret information<ul><li>skm: Secure Key Manager</li></ul> |
| templates.containers.secrets.value | Body | String | O | Key ID |
| templates.containers.secrets.mountPath | Body | String | O | Container mount path |
| templates.containers.volumes | Body | Array | X | Information on NAS storage used in containers |
| templates.containers.volumes.name | Body | String | O | Storage Name |
| templates.containers.volumes.path | Body | String | O | NAS Storage Connection Path |
| templates.containers.volumes.mountPath | body | String | X | Connection path for containers |
| templates.containers.probe | Body | List | X | Setting up container probes |
| templates.containers.probe.type | Body | String | O | Container Probe Type<ul><li>startup</li><li>liveness</li></ul> |
| templates.containers.probe.failureThreshold | Body | Integer | O | Probe failure criteria |
| templates.containers.probe.initialDelaySeconds | Body | Integer | O | Probe startup latency |
| templates.containers.probe.periodSeconds | Body | Integer | O | Probe execution interval |
| templates.containers.probe.timeoutSeconds | Body | Integer | O | Probe execution timeout |
| templates.containers.probe.exec | Body | String List | O | Command to run a probe |
| templates.containers.stopTimeout | Body | Integer | X | Initialization container execution timeout (seconds) |

<details>
  <summary>Example</summary>

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

### View Template

Retrieves information of an individual template.

```bash
GET /ncs/v1.0/appkeys/{appKey}/templates/{templateId}
x-nhn-authorization: {token}
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| templateId | URL | String | O | Template ID |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|

#### Response

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| template | Body | Object | O | Template information |
| template.id | Body | UUID | O | Template ID |
| template.version | Body | String | O | Template version |
| template.name | Body | String | O | Template name |
| template.createdAt | Body | String | O | Created time (UTC) |
| template.description | Body | String | X | Template description |
| template.versionDescription | Body | String | X | Template version description |
| template.networks | Body | Array | O | Template network information |
| template.networks.vpcId | Body | String | O | VPC ID of template |
| template.networks.subnetId | Body | String | O | Subnet ID of template |
| template.dnsConfig | Body | String List | X | DNS server information set up in the container |
| template.hostAliases | Body | List | X | Information set up in container `/etc/hosts` |
| template.hostAliases.ip | Body | String | O | IP of the hostnames set in the container |
| template.hostAliases.hostnames | Body | String List | O | hostnames of the IP set in the container |
| template.versionCount | Body | Integer | O | Number of versions of the template |
| template.workloadCount | Body | Integer | O | Number of workloads using the template |
| template.containers | Body | Array | O | List of containers in a template |
| template.containers.name | Body | String | O | Container name |
| template.containers.type | Body | String | O | Container Type<ul><li>normal: Normal</li><li>init: Initialize</li></ul> |
| template.containers.image | Body | String | O | Container image |
| template.containers.cpus | Body | Float | O | Number of CPU assigned to container |
| template.containers.memoryLimit | Body | Object | O | Information on memory assigned to container |
| template.containers.memoryLimit.hard | Body | Integer | O | Memory assigned to container (MiB) |
| template.containers.gpuFlavor | Body | String | X | Information on GPU Flavor<ul><li>ncs1.g1m5</li><li>ncs1.g2m10</li></ul> |
| template.containers.ports | Body | Array | X | Information on ports used in containers |
| template.containers.ports.containerPort | Body | Integer | O | Container port |
| template.containers.ports.protocol | Body | String | O | Container protocol<ul><li>TCP</li><li>UDP</li><li>HTTP</li><li>HTTPS</li><li>TERMINATED_HTTPS</li></ul> |
| template.containers.command | Body | String List | X | Command to run when container starts |
| template.containers.args | Body | String List | X | Arguments used when the container is started |
| template.containers.workDirectory | Body | String | X | Task Directory of Container |
| template.containers.env | Body | Array | X | Container environment variable |
| template.containers.env.name | Body | String | O | Container environment variable name |
| template.containers.env.value | Body | String | O | Container environment variable value |
| template.containers.postStart | Body | String List | X | Commands that run immediately after container creation |
| template.containers.preStop | Body | String List | X | Commands executed just before container termination |
| template.containers.configs | Body | List | X | ConfigMap information used by containers |
| template.containers.configs.id | Body | Integer | O | ConfigMap ID |
| template.containers.configs.type | Body | String | O | Service type to get ConfigMap information<ul><li>obs: Object Storage</li></ul> |
| template.containers.configs.value | Body | String | O | Object URL |
| template.containers.configs.mountPath | Body | String | O | Container mount path |
| template.containers.secrets | Body | List | X | Secret information used by containers |
| template.containers.secrets.type | Body | String | O | Service type to get secret information<ul><li>skm: Secure Key Manager</li></ul> |
| template.containers.secrets.value | Body | String | O | Key ID |
| template.containers.secrets.mountPath | Body | String | O | Container mount path |
| template.containers.volumes | Body | Array | X | Information on NAS storage used in containers |
| template.containers.volumes.name | Body | String | O | Storage Name |
| template.containers.volumes.path | Body | String | O | NAS Storage Connection Path |
| template.containers.volumes.mountPath | body | String | X | Connection path for containers |
| template.containers.probe | Body | List | X | Setting up container probes |
| template.containers.probe.type | Body | String | O | Container Probe Type<ul><li>startup</li><li>liveness</li></ul> |
| template.containers.probe.failureThreshold | Body | Integer | O | Probe failure criteria |
| template.containers.probe.initialDelaySeconds | Body | Integer | O | Probe startup latency |
| template.containers.probe.periodSeconds | Body | Integer | O | Probe execution interval |
| template.containers.probe.timeoutSeconds | Body | Integer | O | Probe execution timeout |
| template.containers.probe.exec | Body | String List | O | Command to run a probe |
| template.containers.stopTimeout | Body | Integer | X | Initialization container execution timeout (seconds) |

<details>
  <summary>Example</summary>

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

### Create Template

Creates a template.

```bash
POST /ncs/v1.0/appkeys/{appKey}/templates
Content-Type: application/json
x-nhn-authorization: {token}
```

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|
| template | Body | Array | O | Template information |
| template.name | Body | String | O | Template name |
| template.version | Body | String | X | Template version |
| template.description | Body | String | X | Template description |
| template.versionDescription | Body | String | X | Template version description |
| template.networks | Body | Array | O | Template network information |
| template.networks.vpcId | Body | String | O | VPC ID of template |
| template.networks.subnetId | Body | String | O | Subnet ID of template |
| template.dnsConfig | Body | String List | X | DNS server information used by the container (up to three settings) |
| template.hostAliases | Body | List | X | Information to set in container `/etc/hosts` |
| template.hostAliases.ip | Body | String | O | IP information used by hostnames |
| template.hostAliases.hostnames | Body | String List | O | Host information used by hostnames |
| template.containers | Body | Array | O | List of containers in a template |
| template.containers.name | Body | String | O | Container name |
| template.containers.type | Body | String | X | Container Type<ul><li>normal: Normal</li><li>init: Initialize</li></ul>|
| template.containers.image | Body | String | O | Container image |
| template.containers.imageRegistryCredentials | Body | Object | X | Information accessible to private registries |
| template.containers.imageRegistryCredentials.username | Body | String | O | Private registry ID |
| template.containers.imageRegistryCredentials.password | Body | String | O | Private registry password |
| template.containers.cpus | Body | Float | O | Number of CPU assigned to container |
| template.containers.memoryLimit | Body | Object | O | Information on memory assigned to container |
| template.containers.memoryLimit.hard | Body | Integer | O | Memory assigned to container (MiB) |
| template.containers.gpuFlavor | Body | String | X | Information on GPU Flavor<ul><li>ncs1.g1m5</li><li>ncs1.g2m10</li></ul> |
| template.containers.ports | Body | Array | X | Information on ports used in containers |
| template.containers.ports.containerPort | Body | Integer | O | Container port |
| template.containers.ports.protocol | Body | String | O | Container protocol<ul><li>TCP</li><li>UDP</li><li>HTTP</li><li>HTTPS</li><li>TERMINATED_HTTPS</li></ul> |
| template.containers.command | Body | String List | X | Command to run when container starts |
| template.containers.args | Body | String List | X | Arguments used when the container is started |
| template.containers.workDirectory | Body | String | X | Task Directory of Container |
| template.containers.env | Body | Array | X | Container environment variable |
| template.containers.env.name | Body | String | O | Container environment variable name |
| template.containers.env.value | Body | String | O | Container environment variable value |
| template.containers.postStart | Body | String List | X | Commands that run immediately after container creation |
| template.containers.preStop | Body | String List | X | Commands that run immediately after container termination |
| template.containers.configs | Body | List | X | ConfigMap information used by the container (up to 10) |
| template.containers.configs.id | Body | Integer | O | ConfigMap ID |
| template.containers.configs.type | Body | String | O | Service type to get ConfigMap information<ul><li>obs: Object Storage</li></ul> |
| template.containers.configs.appKey | Body | String | O | Object Storage AppKey |
| template.containers.configs.userAccessKeyId | Body | String | O | User Access Key for users accessing the Object Storage service |
| template.containers.configs.secretAccessKey | Body | String | O | Secret Access Key for users accessing the Object Storage service |
| template.containers.configs.value | Body | String | O | Object URL |
| template.containers.configs.mountPath | Body | String | O | Container mount path |
| template.containers.secrets | Body | List | X | Secret information used by the container (up to 10) |
| template.containers.secrets.type | Body | String | O | Service type to get secret information<ul><li>skm: Secure Key Manager</li></ul> |
| template.containers.secrets.value | Body | String | O | Key ID |
| template.containers.secrets.mountPath | Body | String | O | Container mount path |
| template.containers.volumes | Body | Array | X | Information on NAS storage used in containers |
| template.containers.volumes.name | Body | String | O | Storage Name |
| template.containers.volumes.path | Body | String | O | NAS Storage Connection Path |
| template.containers.volumes.mountPath | body | String | X | Connection path of the container (default: /mnt) |
| template.containers.probe | Body | List | X | Setting up container probes |
| template.containers.probe.type | Body | String | O | Container Probe Type<ul><li>startup</li><li>liveness</li></ul> |
| template.containers.probe.failureThreshold | Body | Integer | O | Probe failure criteria |
| template.containers.probe.initialDelaySeconds | Body | Integer | O | Probe startup latency |
| template.containers.probe.periodSeconds | Body | Integer | O | Probe execution interval<li>A value less than timeoutSeconds must be set.</li> |
| template.containers.probe.timeoutSeconds | Body | Integer | O | Probe execution timeout<li>A value greater than periodSeconds must be set.</li> |
| template.containers.probe.exec | Body | String List | O | Command to run a probe |
| template.containers.stopTimeout | Body | Integer | X | Initialization container execution timeout (seconds)<ul><li>30 ~ 120 (default: 30)</li></ul>|


<details>
  <summary>Example</summary>

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

#### Response

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| template | Body | Object | O | Template information |
| template.id | Body | UUID | O | Template ID |
| template.name | Body | String | O | Template name |
| template.version | Body | String | O | Template version |
| template.createdAt | Body | String | O | Created time (UTC) |
| template.description | Body | String | X | Template description |
| template.versionDescription | Body | String | X | Template version description |
| template.networks | Body | Array | O | Template network information |
| template.networks.vpcId | Body | String | O | VPC ID of template |
| template.networks.subnetId | Body | String | O | Subnet ID of template |
| template.dnsConfig | Body | String List | X | DNS server information set up in the container |
| template.hostAliases | Body | List | X | Information set up in container `/etc/hosts` |
| template.hostAliases.ip | Body | String | O | IP of the hostnames set in the container |
| template.hostAliases.hostnames | Body | String List | O | hostnames of the IP set in the container |
| template.containers | Body | Array | O | List of containers in a template |
| template.containers.name | Body | String | O | Container name |
| template.containers.type | Body | String | O | Container Type<ul><li>normal: Normal</li><li>init: Initialize</li></ul>|
| template.containers.image | Body | String | O | Container image |
| template.containers.cpus | Body | Float | O | Number of CPU assigned to container |
| template.containers.memoryLimit | Body | Object | O | Information on memory assigned to container |
| template.containers.memoryLimit.hard | Body | Integer | O | Memory assigned to container (MiB) |
| template.containers.gpuFlavor | Body | String | X | Information on GPU Flavor<ul><li>ncs1.g1m5</li><li>ncs1.g2m10</li></ul> |
| template.containers.ports | Body | Array | X | Information on ports used in containers |
| template.containers.ports.containerPort | Body | Integer | O | Container port |
| template.containers.ports.protocol | Body | String | O | Container protocol<ul><li>TCP</li><li>UDP</li><li>HTTP</li><li>HTTPS</li><li>TERMINATED_HTTPS</li></ul> |
| template.containers.command | Body | String List | X | Command to run when container starts |
| template.containers.args | Body | String List | X | Arguments used when the container is started |
| template.containers.workDirectory | Body | String | X | Task Directory of Container |
| template.containers.env | Body | Array | X | Container environment variable |
| template.containers.env.name | Body | String | O | Container environment variable name |
| template.containers.env.value | Body | String | O | Container environment variable value |
| template.containers.postStart | Body | String List | X | Commands that run immediately after container creation |
| template.containers.preStop | Body | String List | X | Commands that run immediately after container termination |
| template.containers.configs | Body | List | X | ConfigMap information used by containers |
| template.containers.configs.id | Body | Integer | O | ConfigMap ID |
| template.containers.configs.type | Body | String | O | Service type to get ConfigMap information<ul><li>obs: Object Storage</li></ul> |
| template.containers.configs.value | Body | String | O | Object URL |
| template.containers.configs.mountPath | Body | String | O | Container mount path |
| template.containers.secrets | Body | List | X | Secret information used by containers |
| template.containers.secrets.type | Body | String | O | Service type to get secret information<ul><li>skm: Secure Key Manager</li></ul> |
| template.containers.secrets.value | Body | String | O | Key ID |
| template.containers.secrets.mountPath | Body | String | O | Container mount path |
| template.containers.volumes | Body | Array | X | Information on NAS storage used in containers |
| template.containers.volumes.name | Body | String | O | Storage Name |
| template.containers.volumes.path | Body | String | O | NAS Storage Connection Path |
| template.containers.volumes.mountPath | body | String | X | Connection path for containers |
| template.containers.probe | Body | List | X | Setting up container probes |
| template.containers.probe.type | Body | String | O | Container Probe Type<ul><li>startup</li><li>liveness</li></ul> |
| template.containers.probe.failureThreshold | Body | Integer | O | Probe failure criteria |
| template.containers.probe.initialDelaySeconds | Body | Integer | O | Probe startup latency |
| template.containers.probe.periodSeconds | Body | Integer | O | Probe execution interval |
| template.containers.probe.timeoutSeconds | Body | Integer | O | Probe execution timeout |
| template.containers.probe.exec | Body | String List | O | Command to run a probe |
| template.containers.stopTimeout | Body | Integer | X | Initialization container execution timeout (seconds) |

<details>
  <summary>Example</summary>

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

### Delete Template

Deletes a template.

```bash
DELETE /ncs/v1.0/appkeys/{appKey}/templates/{templateId}
x-nhn-authorization: {token}
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| templateId | URL | String | O | Template ID |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|

#### Response

This API only responds with common information.

### View a list of template versions

```bash
GET /ncs/v1.0/appkeys/{appKey}/templates/{templateId}/versions
x-nhn-authorization: {token}
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| templateId | Path | String | O | Template ID |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|
| q | Query | String | X | Search parameters |
| page | Query | Integer | X | Page number to retrieve |
| size | Query | Integer | X | Page size to retrieve (default: 10) |
| sort | Query | String | X | Name of the field to sort by<br>Prefix field names with `-`for reverse sorting<br>Example: `sort=-name` |

#### Response

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| X-Total-Count | Header | Integer | O | Number of versions of the template |
| templates | Body | Array | O | Template version list |
| templates.id | Body | UUID | O | Template ID |
| templates.version | Body | String | O | Template version |
| templates.sourceVersion | Body | String | O | Template base version |
| templates.name | Body | String | O | Template name |
| templates.createdAt | Body | String | O | Created time (UTC) |
| templates.description | Body | String | X | Template description |
| templates.versionDescription | Body | String | X | Template version description |
| templates.networks | Body | Array | O | Template network information |
| templates.networks.vpcId | Body | String | O | VPC ID of template |
| templates.networks.subnetId | Body | String | O | Subnet ID of template |
| templates.dnsConfig | Body | String List | X | DNS server information set up in the container |
| templates.hostAliases | Body | List | X | Information set up in container `/etc/hosts` |
| templates.hostAliases.ip | Body | String | O | IP of the hostnames set in the container |
| templates.hostAliases.hostnames | Body | String List | O | hostnames of the IP set in the container |
| templates.versionCount | Body | Integer | O | Number of versions of the template |
| templates.workloadCount | Body | Integer | O | Number of workloads using the template |
| templates.containers | Body | Array | O | List of containers in a template |
| templates.containers.name | Body | String | O | Container name |
| templates.containers.type | Body | String | O | Container Type<ul><li>normal: Normal</li><li>init: Initialize</li></ul>|
| templates.containers.image | Body | String | O | Container image |
| templates.containers.cpus | Body | Float | O | Number of CPU assigned to container |
| templates.containers.memoryLimit | Body | Object | O | Information on memory assigned to container |
| templates.containers.memoryLimit.hard | Body | Integer | O | Memory assigned to container (MiB) |
| templates.containers.gpuFlavor | Body | String | X | Information on GPU Flavor<ul><li>ncs1.g1m5</li><li>ncs1.g2m10</li></ul> |
| templates.containers.ports | Body | Array | X | Information on ports used in containers |
| templates.containers.ports.containerPort | Body | Integer | O | Container port |
| template.containers.ports.protocol | Body | String | O | Container protocol<ul><li>TCP</li><li>UDP</li><li>HTTP</li><li>HTTPS</li><li>TERMINATED_HTTPS</li></ul> |
| templates.containers.command | Body | String List | X | Command to run when container starts |
| templates.containers.args | Body | String List | X | Arguments used when the container is started |
| templates.containers.workDirectory | Body | String | X | Task Directory of Container |
| templates.containers.env | Body | Array | X | Container environment variable |
| templates.containers.env.name | Body | String | O | Container environment variable name |
| templates.containers.env.value | Body | String | O | Container environment variable value |
| templates.containers.postStart | Body | String List | X | Commands that run immediately after container creation |
| templates.containers.preStop | Body | String List | X | Commands that run immediately after container termination |
| templates.containers.configs | Body | List | X | ConfigMap information used by containers |
| templates.containers.configs.id | Body | Integer | O | ConfigMap ID |
| templates.containers.configs.type | Body | String | O | Service type to get ConfigMap information<ul><li>obs: Object Storage</li></ul> |
| templates.containers.configs.value | Body | String | O | Object URL |
| templates.containers.configs.mountPath | Body | String | O | Container mount path |
| templates.containers.secrets | Body | List | X | Secret information used by containers |
| templates.containers.secrets.type | Body | String | O | Service type to get secret information<ul><li>skm: Secure Key Manager</li></ul> |
| templates.containers.secrets.value | Body | String | O | Key ID |
| templates.containers.secrets.mountPath | Body | String | O | Container mount path |
| templates.containers.volumes | Body | Array | X | Information on NAS storage used in containers |
| templates.containers.volumes.name | Body | String | O | Storage Name |
| templates.containers.volumes.path | Body | String | O | NAS Storage Connection Path |
| templates.containers.volumes.mountPath | body | String | X | Connection path for containers |
| templates.containers.probe | Body | List | X | Setting up container probes |
| templates.containers.probe.type | Body | String | O | Container Probe Type<ul><li>startup</li><li>liveness</li></ul> |
| templates.containers.probe.failureThreshold | Body | Integer | O | Probe failure criteria |
| templates.containers.probe.initialDelaySeconds | Body | Integer | O | Probe startup latency |
| templates.containers.probe.periodSeconds | Body | Integer | O | Probe execution interval |
| templates.containers.probe.timeoutSeconds | Body | Integer | O | Probe execution timeout |
| templates.containers.probe.exec | Body | String List | O | Command to run a probe |
| templates.containers.stopTimeout | Body | Integer | X | Initialization container execution timeout (seconds) |

<details>
  <summary>Example</summary>

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

### View template versions

View individual template version information.

```bash
GET /ncs/v1.0/appkeys/{appKey}/templates/{templateId}/versions/{version}
x-nhn-authorization: {token}
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| templateId | URL | String | O | Template ID |
| String | URL | String | O | Template version |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|

#### Response

* Same as template detail lookup

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| template | Body | Object | O | Template version information |
| template.id | Body | UUID | O | Template ID |
| template.version | Body | String | O | Template version |
| template.sourceVersion | Body | String | O | Template base version |
| template.name | Body | String | O | Template name |
| template.createdAt | Body | String | O | Created time (UTC) |
| template.description | Body | String | X | Template description |
| template.versionDescription | Body | String | X | Template version description |
| template.networks | Body | Array | O | Template network information |
| template.networks.vpcId | Body | String | O | VPC ID of template |
| template.networks.subnetId | Body | String | O | Subnet ID of template |
| template.dnsConfig | Body | String List | X | DNS server information set up in the container |
| template.hostAliases | Body | List | X | Information set up in container `/etc/hosts` |
| template.hostAliases.ip | Body | String | O | IP of the hostnames set in the container |
| template.hostAliases.hostnames | Body | String List | O | hostnames of the IP set in the container |
| template.versionCount | Body | Integer | O | Number of versions of the template |
| template.workloadCount | Body | Integer | O | Number of workloads using the template |
| template.containers | Body | Array | O | List of containers in a template |
| template.containers.name | Body | String | O | Container name |
| template.containers.type | Body | String | O | Container Type<ul><li>normal: Normal</li><li>init: Initialize</li></ul>|
| template.containers.image | Body | String | O | Container image |
| template.containers.cpus | Body | Float | O | Number of CPU assigned to container |
| template.containers.memoryLimit | Body | Object | O | Information on memory assigned to container |
| template.containers.memoryLimit.hard | Body | Integer | O | Memory assigned to container (MiB) |
| template.containers.gpuFlavor | Body | String | X | Information on GPU Flavor<ul><li>ncs1.g1m5</li><li>ncs1.g2m10</li></ul> |
| template.containers.ports | Body | Array | X | Information on ports used in containers |
| template.containers.ports.containerPort | Body | Integer | O | Container port |
| template.containers.ports.protocol | Body | String | O | Container protocol<ul><li>TCP</li><li>UDP</li><li>HTTP</li><li>HTTPS</li><li>TERMINATED_HTTPS</li></ul> |
| template.containers.command | Body | String List | X | Command to run when container starts |
| template.containers.args | Body | String List | X | Arguments used when the container is started |
| template.containers.workDirectory | Body | String | X | Task Directory of Container |
| template.containers.env | Body | Array | X | Container environment variable |
| template.containers.env.name | Body | String | O | Container environment variable name |
| template.containers.env.value | Body | String | O | Container environment variable value |
| template.containers.postStart | Body | String List | X | Commands that run immediately after container creation |
| template.containers.preStop | Body | String List | X | Commands that run immediately after container termination |
| template.containers.configs | Body | List | X | ConfigMap information used by containers |
| template.containers.configs.id | Body | Integer | O | ConfigMap ID |
| template.containers.configs.type | Body | String | O | Service type to get ConfigMap information<ul><li>obs: Object Storage</li></ul> |
| template.containers.configs.value | Body | String | O | Object URL |
| template.containers.configs.mountPath | Body | String | O | Container mount path |
| template.containers.secrets | Body | List | X | Secret information used by containers |
| template.containers.secrets.type | Body | String | O | Service type to get secret information<ul><li>skm: Secure Key Manager</li></ul> |
| template.containers.secrets.value | Body | String | O | Key ID |
| template.containers.secrets.mountPath | Body | String | O | Container mount path |
| template.containers.volumes | Body | Array | X | Information on NAS storage used in containers |
| template.containers.volumes.name | Body | String | O | Storage Name |
| template.containers.volumes.path | Body | String | O | NAS Storage Connection Path |
| template.containers.volumes.mountPath | body | String | X | Connection path for containers |
| template.containers.probe | Body | List | X | Setting up container probes |
| template.containers.probe.type | Body | String | O | Container Probe Type<ul><li>startup</li><li>liveness</li></ul> |
| template.containers.probe.failureThreshold | Body | Integer | O | Probe failure criteria |
| template.containers.probe.initialDelaySeconds | Body | Integer | O | Probe startup latency |
| template.containers.probe.periodSeconds | Body | Integer | O | Probe execution interval |
| template.containers.probe.timeoutSeconds | Body | Integer | O | Probe execution timeout |
| template.containers.probe.exec | Body | String List | O | Command to run a probe |
| template.containers.stopTimeout | Body | Integer | X | Initialization container execution timeout (seconds) |

<details>
  <summary>Example</summary>

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

### Create Template Version

Creates a version of the template.

```bash
POST /ncs/v1.0/appkeys/{appKey}/templates/{templateId}/versions
x-nhn-authorization: {token}
```

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| templateId | URL | String | O | Template ID |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|
| template | Body | Object | O | Template version information |
| template.version | Body | String | O | Template version |
| template.sourceVersion | Body | String | O | Template base version |
| template.name | Body | String | O | Template name |
| template.versionDescription | Body | String | X | Template version description |
| template.dnsConfig | Body | String List | X | DNS server information set up in the container |
| template.hostAliases | Body | List | X | Information set up in container `/etc/hosts` |
| template.hostAliases.ip | Body | String | O | IP of the hostnames set in the container |
| template.hostAliases.hostnames | Body | String List | O | hostnames of the IP set in the container |
| template.applyImmediately | Body | Boolean | X | true: Enable instant deployment, false: Disable instant deployment (default: false) |
| template.containers | Body | Array | O | List of containers in a template |
| template.containers.name | Body | String | O | Container name |
| template.containers.type | Body | String | O | Container Type<ul><li>normal: Normal</li><li>init: Initialize</li></ul>|
| template.containers.image | Body | String | O | Container image |
| template.containers.imageRegistryCredentials | Body | Object | X | Information accessible to private registries |
| template.containers.imageRegistryCredentials.changed | Body | Boolean | X | Whether to use an existing account (default: false)<ul><li>false: Use an existing account</li><li>true: Use a new account (username, password must be passed)</li></ul> |
| template.containers.imageRegistryCredentials.username | Body | String | O | Private registry ID |
| template.containers.imageRegistryCredentials.password | Body | String | O | Private registry password |
| template.containers.cpus | Body | Float | O | Number of CPU assigned to container |
| template.containers.memoryLimit | Body | Object | O | Information on memory assigned to container |
| template.containers.memoryLimit.hard | Body | Integer | O | Memory assigned to container (MiB) |
| template.containers.gpuFlavor | Body | String | X | Information on GPU Flavor<ul><li>ncs1.g1m5</li><li>ncs1.g2m10</li></ul> |
| template.containers.ports | Body | Array | X | Information on ports used in containers |
| template.containers.ports.containerPort | Body | Integer | O | Container port |
| template.containers.ports.protocol | Body | String | O | Container protocol<ul><li>TCP</li><li>UDP</li><li>HTTP</li><li>HTTPS</li><li>TERMINATED_HTTPS</li></ul> |
| template.containers.command | Body | String List | X | Command to run when container starts |
| template.containers.args | Body | String List | X | Arguments used when the container is started |
| template.containers.workDirectory | Body | String | X | Task Directory of Container |
| template.containers.env | Body | Array | X | Container environment variable |
| template.containers.env.name | Body | String | O | Container environment variable name |
| template.containers.env.value | Body | String | O | Container environment variable value |
| template.containers.postStart | Body | String List | X | Commands that run immediately after container creation |
| template.containers.preStop | Body | String List | X | Commands executed just before container termination |
| template.containers.configs | Body | List | X | ConfigMap information used by containers |
| template.containers.configs.id | Body | Integer | O | ConfigMap ID |
| template.containers.configs.type | Body | String | O | Service type to get ConfigMap information<ul><li>obs: Object Storage</li></ul> |
| template.containers.configs.value | Body | String | O | Object URL |
| template.containers.configs.mountPath | Body | String | O | Container mount path |
| template.containers.configs.changedAppKey | Body | Boolean | X | Whether to use existing AppKeys (default: false)<ul><li>false: Use existing key</li><li>true: use new key (AppKey must be passed as required)</li></ul> |
| template.containers.configs.appKey | Body | String | X | Object Storage AppKey |
| template.containers.configs.changedUserAccessKeyId | Body | Boolean | X | Whether to use an existing UserAccessKeyId (default: false)<ul><li>false: Use existing key</li><li>true: Use new key (UserAccessKeyId must be passed as required)</li></ul> |
| template.containers.configs.userAccessKeyId | Body | String | X | User Access Key for users accessing the Object Storage service |
| template.containers.configs.changedSecretAccessKey | Body | Boolean | X | Whether to use an existing SecretAccessKey (default: false)<ul><li>false: Use existing key</li><li>true: use new key (SecretAccessKey must be passed)</li></ul> |
| template.containers.configs.secretAccessKey | Body | String | X | Secret Access Key for users accessing the Object Storage service |
| template.containers.secrets | Body | List | X | Secret information used by containers |
| template.containers.secrets.type | Body | String | O | Service type to get secret information<ul><li>skm: Secure Key Manager</li></ul> |
| template.containers.secrets.value | Body | String | O | Key ID |
| template.containers.secrets.mountPath | Body | String | O | Container mount path |
| template.containers.volumes | Body | Array | X | Information on NAS storage used in containers |
| template.containers.volumes.name | Body | String | O | Storage Name |
| template.containers.volumes.path | Body | String | O | NAS Storage Connection Path |
| template.containers.volumes.mountPath | body | String | X | Connection path for containers |
| template.containers.probe | Body | List | X | Setting up container probes |
| template.containers.probe.type | Body | String | O | Container Probe Type<ul><li>startup</li><li>liveness</li></ul> |
| template.containers.probe.failureThreshold | Body | Integer | O | Probe failure criteria |
| template.containers.probe.initialDelaySeconds | Body | Integer | O | Probe startup latency |
| template.containers.probe.periodSeconds | Body | Integer | O | Probe execution interval |
| template.containers.probe.timeoutSeconds | Body | Integer | O | Probe execution timeout |
| template.containers.probe.exec | Body | String List | O | Command to run a probe |
| template.containers.stopTimeout | Body | Integer | X | Initialization container execution timeout (seconds) |

<details>
  <summary>Example</summary>

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

#### Response

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| template | Body | Object | O | Template information |
| template.id | Body | UUID | O | Template ID |
| template.version | Body | String | O | Template version |
| template.sourceVersion | Body | String | O | Template base version |
| template.name | Body | String | O | Template name |
| template.createdAt | Body | String | O | Created time (UTC) |
| template.description | Body | String | X | Template description |
| template.versionDescription | Body | String | X | Template version description |
| template.networks | Body | Array | O | Template network information |
| template.networks.vpcId | Body | String | O | VPC ID of template |
| template.networks.subnetId | Body | String | O | Subnet ID of template |
| template.dnsConfig | Body | String List | X | DNS server information set up in the container |
| template.hostAliases | Body | List | X | Information set up in container `/etc/hosts` |
| template.hostAliases.ip | Body | String | O | IP of the hostnames set in the container |
| template.hostAliases.hostnames | Body | String List | O | hostnames of the IP set in the container |
| template.containers | Body | Array | O | List of containers in a template |
| template.containers.name | Body | String | O | Container name |
| template.containers.type | Body | String | O | Container Type<ul><li>normal: Normal</li><li>init: Initialize</li></ul>|
| template.containers.image | Body | String | O | Container image |
| template.containers.cpus | Body | Float | O | Number of CPU assigned to container |
| template.containers.memoryLimit | Body | Object | O | Information on memory assigned to container |
| template.containers.memoryLimit.hard | Body | Integer | O | Memory assigned to container (MiB) |
| template.containers.gpuFlavor | Body | String | X | Information on GPU Flavor<ul><li>ncs1.g1m5</li><li>ncs1.g2m10</li></ul> |
| template.containers.ports | Body | Array | X | Information on ports used in containers |
| template.containers.ports.containerPort | Body | Integer | O | Container port |
| template.containers.ports.protocol | Body | String | O | Container protocol<ul><li>TCP</li><li>UDP</li><li>HTTP</li><li>HTTPS</li><li>TERMINATED_HTTPS</li></ul> |
| template.containers.command | Body | String List | X | Command to run when container starts |
| template.containers.args | Body | String List | X | Arguments used when the container is started |
| template.containers.workDirectory | Body | String | X | Task Directory of Container |
| template.containers.env | Body | Array | X | Container environment variable |
| template.containers.env.name | Body | String | O | Container environment variable name |
| template.containers.env.value | Body | String | O | Container environment variable value |
| template.containers.postStart | Body | String List | X | Commands that run immediately after container creation |
| template.containers.preStop | Body | String List | X | Commands executed just before container termination |
| template.containers.configs | Body | List | X | ConfigMap information used by containers |
| template.containers.configs.id | Body | Integer | O | ConfigMap ID |
| template.containers.configs.type | Body | String | O | Service type to get ConfigMap information<ul><li>obs: Object Storage</li></ul> |
| template.containers.configs.value | Body | String | O | Object URL |
| template.containers.configs.mountPath | Body | String | O | Container mount path |
| template.containers.secrets | Body | List | X | Secret information used by containers |
| template.containers.secrets.type | Body | String | O | Service type to get secret information<ul><li>skm: Secure Key Manager</li></ul> |
| template.containers.secrets.value | Body | String | O | Key ID |
| template.containers.secrets.mountPath | Body | String | O | Container mount path |
| template.containers.volumes | Body | Array | X | Information on NAS storage used in containers |
| template.containers.volumes.name | Body | String | O | Storage Name |
| template.containers.volumes.path | Body | String | O | NAS Storage Connection Path |
| template.containers.volumes.mountPath | body | String | X | Connection path for containers |
| template.containers.probe | Body | List | X | Setting up container probes |
| template.containers.probe.type | Body | String | O | Container Probe Type<ul><li>startup</li><li>liveness</li></ul> |
| template.containers.probe.failureThreshold | Body | Integer | O | Probe failure criteria |
| template.containers.probe.initialDelaySeconds | Body | Integer | O | Probe startup latency |
| template.containers.probe.periodSeconds | Body | Integer | O | Probe execution interval |
| template.containers.probe.timeoutSeconds | Body | Integer | O | Probe execution timeout |
| template.containers.probe.exec | Body | String List | O | Command to run a probe |
| template.containers.stopTimeout | Body | Integer | X | Initialization container execution timeout (seconds) |

<details>
  <summary>Example</summary>

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

### Delete Template Version

```bash
DELETE /ncs/v1.0/appkeys/{appkey}/templates/{templateId}/versions/{version}
x-nhn-authorization: {token}
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| templateId | URL | String | O | Template ID |
| String | URL | String | O | Template version |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|

#### Response

This API only responds with common information.

## Workload

### List Workloads

Retrieves a list of workloads.

```bash
GET /ncs/v1.0/appkeys/{appKey}/workloads
x-nhn-authorization: {token}
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|
| q | Query | String | X | Filter by workload name, template ID, and template version<ul>Example:<li>q=templateId=${template ID}</li><li>q=${workload name)</li><li>q=templateId=${Template ID}&version=${Template version}</li></ul> |
| page | Query | Integer | X | Page number to retrieve |
| size | Query | Integer | X | Page size to retrieve (default: 10) |

#### Response

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| X-Total-Count | Header | Integer | O | Number of workloads created in Appkey |
| workloads | Body | Array | O | List of workloads |
| workloads.id | Body | UUID | O | Workload ID |
| workloads.name | Body | String | O | Workload name |
| workloads.type | Body | String | O | Deployment controller<ul><li>deployment</li><li>statefulset</li></ul> |
| workloads.templateId | Body | String | O | Template ID of workloads |
| workloads.templateVersion | Body | String | O | Template version of the workload |
| workloads.createdAt | Body | String | O | Created time (UTC) |
| workloads.desired | Body | Integer | O | Number of workload tasks requested |
| workloads.available | Body | Integer | O | Number of workload tasks executed |
| workloads.internalLBTimeout | Body | Integer | X | Internal request response latency |
| workloads.status | Body | Integer | O | Workload status |
| workloads.url | Body | String | X | URL of workload load balancer |
| workloads.loadBalancing | Body | Object | O | Information on workload load balancer |
| workloads.loadBalancing.enabled | Body | Boolean | O | Whether to use workload load balancer |
| workloads.loadBalancing.floatingIp | Body | Boolean | O | Whether to use the workload load balancer floating IP |
| workloads.loadBalancing.healthMonitor | Body | Object | X | About checking the health of the load balancer |
| workloads.loadBalancing.healthMonitor.delay | Body | Integer | O | How often to check status |
| workloads.loadBalancing.healthMonitor.timeout | Body | Integer | O | Maximum response latency |
| workloads.loadBalancing.healthMonitor.maxRetries | Body | Integer | O | Maximum number of retries |
| workloads.loadBalancing.healthMonitor.httpMethod | Body | String | X | HTTP Methods<ul><li>GET</li></ul> |
| workloads.loadBalancing.healthMonitor.expectedCodes | Body | String | X | HTTP status codes<ul><li>200</li><li>200,202</li><li>200-204</li></ul> |
| workloads.loadBalancing.healthMonitor.urlPath | Body | String | X | HTTP URL |
| workloads.loadBalancing.certificate | Body | String | X | Certificates used by the load balancer when using TERMINATED_HTTPS |
| workloads.loadBalancing.privateKey | Body | String | X | Private key used by the load balancer when using TERMINATED_HTTPS |
| workloads.loadBalancing.tlsVersion | Body | String | X | TLS version when using TERMINATED_HTTPS<ul><li>SSLv3</li><li>TLSv1.0</li><li>TLSv1.0_2016</li><li>TLSv1.1</li><li>TLSv1.2</li><li>TLSv1.3</li></ul> |
| workloads.loadBalancing.containerHref | Body | String | X | Secret container ID<li>If you separately registered a certificate and private key to use in TERMINATED_HTTPS using the Load Balancer API, use it.</li> |
| workloads.loadBalancing.ipAclGroupsBinding | Body | List | X | List of IP access control groups to apply to the load balancer |
| workloads.loadBalancing.ipAclGroupsBinding.ipAclGroupId | Body | String | O | IP Access Control Group ID |
| workloads.schedule | Body | Object | X | About scheduled execution settings |
| workloads.schedule.timeZone | Body | String | O | Schedule execution base time<ul><li>Example: Asia/Seoul, UTC</li><li>[List of tz database time zones](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)</li></ul> |
| workloads.schedule.cron | Body | String | O | Scheduled Execution Cron Expressions |
| workloads.schedule.jobsHistoryLimit | Body | Integer | O | Number of scheduled execution history |
| workloads.schedule.concurrencyPolicy | Body | String | O | Concurrency policy<ul><li>Forbid</li><li>Replace</li></ul> |
| workloads.schedule.timeOffset | Body | String | O | Offset the scheduled execution base time |
| workloads.internalLoadBalancing | Body | Object | X | Internal load balancer information |
| workloads.internalLoadBalancing.enalbed | Body | Boolean | O | Whether to use an internal load balancer |
| workloads.internalLoadBalancing.type | Body | String | X | How to assign internal load balancer IPs<ul><li>Dynamic: Automatically assigned</li><li>static: Specify an IP</li></ul> |
| workloads.internalLoadBalancing.ip | Body | String | X | Specify internal load balancer IP |
| workloads.privateDns | Body | Object | X | Determinine whether to register workload working IPs with Private DNS |
| workloads.privateDns.ttl | Body | Integer | O | TTL value of a recordset |
| workloads.privateDns.zoneId | Body | String | O | The Private DNS Zone ID used by the workload |
| workloads.privateDns.domain | Body | String | O | About domains registered with Private DNS |
| workloads.activeDeadline | Body | Object | X | Information on workload scheduling end  |
| workloads.activeDeadline.timeZone | Body | String | O | Scheduling end base time<li>Example: Asia/Seoul, UTC</li> |
| workloads.activeDeadline.timeOffset | Body | String | O | Offset the scheduling end base time |
| workloads.activeDeadline.time | Body | String | O | Scheduling end time |
| workloads.autoScaler | Body | Object | X | Information on AutoScaler settings |
| workloads.autoScaler.scaleOut | Body | Object | O | Information on ScaleOut |
| workloads.autoScaler.scaleOut.enabled | Body | Boolean | O | Whether to use ScaleOut |
| workloads.autoScaler.scaleOut.maxReplicas | Body | Integer | X | Maximum number of jobs for autoscaling |
| workloads.autoScaler.scaleOut.coolDownMinute | Body | Integer | X | Wait time after scaling up |
| workloads.autoScaler.scaleOut.condition | Body | List | X | Scale out condition |
| workloads.autoScaler.scaleOut.condition.resource | Body | String | X | Resources based on scale out conditions<ul><li>cpu</li><li>memory</li><li>gpu</li><li>gpu-memory</li></ul> |
| workloads.autoScaler.scaleOut.condition.threshold | Body | Integer | X | Scale out condition resource usage (1-100) |
| workloads.autoScaler.scaleOut.condition.duration | Body | Integer | X | Scale out condition resource usage hold time (minutes) |
| workloads.autoScaler.scaleIn | Body | Object | X | Information on ScaleIn |
| workloads.autoScaler.scaleIn.enabled | Body | Boolean | O | Whether to use ScaleIn |
| workloads.autoScaler.scaleIn.minReplicas | Body | Integer | X | Minimum number of jobs for autoscaling |
| workloads.autoScaler.scaleIn.coolDownMinute | Body | Integer | X | Wait time after scale in |
| workloads.autoScaler.scaleIn.condition | Body | List | X | Scale in condition |
| workloads.autoScaler.scaleIn.condition.resource | Body | String | X | Resources based on scale in conditions<ul><li>cpu</li><li>memory</li><li>gpu</li><li>gpu-memory</li></ul> |
| workloads.autoScaler.scaleIn.condition.threshold | Body | Integer | X | Scale in condition resource usage (1-100) |
| workloads.autoScaler.scaleIn.condition.duration | Body | Integer | X | Scale in condition resource usage hold time (minutes) |
| workloads.securityGroups | Body | List | X | Information on SecurityGroups |
| workloads.securityGroups.id | Body | String | O | SecurityGroups ID |

<details>
  <summary>Example</summary>

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

### View Workload

Retrieves an individual workload.

```bash
GET /ncs/v1.0/appkeys/{appKey}/workloads/{workloadId}
x-nhn-authorization: {token}
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| workloadId | URL | String | O | Workload ID |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|

#### Response

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| workload | Body | Array | O | Workload information |
| workload.id | Body | UUID | O | Workload ID |
| workload.name | Body | String | O | Workload name |
| workload.type | Body | String | O | Deployment controller<ul><li>deployment</li><li>statefulset</li></ul> |
| workload.templateId | Body | String | O | Template ID of workloads |
| workload.templateVersion | Body | String | O | Template version of the workload |
| workload.createdAt | Body | String | O | Created time (UTC) |
| workload.desired | Body | Integer | O | Number of workload tasks requested |
| workload.available | Body | Integer | O | Number of workload tasks executed |
| workload.internalLBTimeout | Body | Integer | X | Internal request response latency |
| workload.status | Body | Integer | O | Workload status |
| workload.url | Body | String | X | URL of workload load balancer |
| workload.loadBalancing | Body | Object | O | Information on workload load balancer |
| workload.loadBalancing.enabled | Body | Boolean | O | Whether to use workload load balancer |
| workload.loadBalancing.floatingIp | Body | Boolean | O | Whether to use the workload load balancer floating IP |
| workload.loadBalancing.healthMonitor | Body | Object | X | About checking the health of the load balancer |
| workload.loadBalancing.healthMonitor.delay | Body | Integer | O | How often to check status |
| workload.loadBalancing.healthMonitor.timeout | Body | Integer | O | Maximum response latency |
| workload.loadBalancing.healthMonitor.maxRetries | Body | Integer | O | Maximum number of retries |
| workload.loadBalancing.healthMonitor.httpMethod | Body | String | X | HTTP Methods<ul><li>GET</li></ul> |
| workload.loadBalancing.healthMonitor.expectedCodes | Body | String | X | HTTP status codes<ul><li>200</li><li>200,202</li><li>200-204</li></ul> |
| workload.loadBalancing.healthMonitor.urlPath | Body | String | X | HTTP URL |
| workload.loadBalancing.certificate | Body | String | X | Certificates used by the load balancer when using TERMINATED_HTTPS |
| workload.loadBalancing.privateKey | Body | String | X | Private key used by the load balancer when using TERMINATED_HTTPS |
| workload.loadBalancing.tlsVersion | Body | String | X | TLS version when using TERMINATED_HTTPS<ul><li>SSLv3</li><li>TLSv1.0</li><li>TLSv1.0_2016</li><li>TLSv1.1</li><li>TLSv1.2</li><li>TLSv1.3</li></ul> |
| workload.loadBalancing.containerHref | Body | String | X | Secret container ID<li>If you separately registered a certificate and private key to use in TERMINATED_HTTPS using the Load Balancer API, use it.</li> |
| workload.loadBalancing.ipAclGroupsBinding | Body | List | X | List of IP access control groups to apply to the load balancer |
| workload.loadBalancing.ipAclGroupsBinding.ipAclGroupId | Body | String | O | IP Access Control Group ID |
| workload.schedule | Body | Object | X | About scheduled execution settings |
| workload.schedule.timeZone | Body | String | O | Schedule execution base time<ul><li>Example: Asia/Seoul, UTC</li><li>[List of tz database time zones](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)</li></ul> |
| workload.schedule.cron | Body | String | O | Scheduled Execution Cron Expressions |
| workload.schedule.jobsHistoryLimit | Body | Integer | O | Number of scheduled execution history |
| workload.schedule.concurrencyPolicy | Body | String | O | Concurrency policy<ul><li>Forbid</li><li>Replace</li></ul> |
| workload.schedule.timeOffset | Body | String | O | Offset the scheduled execution base time |
| workload.internalLoadBalancing | Body | Object | X | Internal load balancer information |
| workload.internalLoadBalancing.enalbed | Body | Boolean | O | Whether to use an internal load balancer |
| workload.internalLoadBalancing.type | Body | String | X | How to assign internal load balancer IPs<ul><li>Dynamic: Automatically assigned</li><li>static: Specify an IP</li></ul> |
| workload.internalLoadBalancing.ip | Body | String | X | Specify internal load balancer IP |
| workload.privateDns | Body | Object | X | Determinine whether to register workload working IPs with Private DNS |
| workload.privateDns.ttl | Body | Integer | O | TTL value of a recordset |
| workload.privateDns.zoneId | Body | String | O | The Private DNS Zone ID used by the workload |
| workload.privateDns.domain | Body | String | O | About domains registered with Private DNS |
| workload.activeDeadline | Body | Object | X | Information on workload scheduling end  |
| workload.activeDeadline.timeZone | Body | String | O | Scheduling end base time<li>Example: Asia/Seoul, UTC</li> |
| workload.activeDeadline.timeOffset | Body | String | O | Offset the scheduling end base time |
| workload.activeDeadline.time | Body | String | O | Scheduling end time |
| workload.autoScaler | Body | Object | X | Information on AutoScaler settings |
| workload.autoScaler.scaleOut | Body | Object | O | Information on ScaleOut |
| workload.autoScaler.scaleOut.enabled | Body | Boolean | O | Whether to use ScaleOut |
| workload.autoScaler.scaleOut.maxReplicas | Body | Integer | X | Maximum number of jobs for autoscaling |
| workload.autoScaler.scaleOut.coolDownMinute | Body | Integer | X | Wait time after scaling up |
| workload.autoScaler.scaleOut.condition | Body | List | X | Scale out condition |
| workload.autoScaler.scaleOut.condition.resource | Body | String | X | Resources based on scale out conditions<ul><li>cpu</li><li>memory</li><li>gpu</li><li>gpu-memory</li></ul> |
| workload.autoScaler.scaleOut.condition.threshold | Body | Integer | X | Scale out condition resource usage (1-100) |
| workload.autoScaler.scaleOut.condition.duration | Body | Integer | X | Scale out condition resource usage hold time (minutes) |
| workload.autoScaler.scaleIn | Body | Object | X | Information on ScaleIn |
| workload.autoScaler.scaleIn.enabled | Body | Boolean | O | Whether to use ScaleIn |
| workload.autoScaler.scaleIn.minReplicas | Body | Integer | X | Minimum number of jobs for autoscaling |
| workload.autoScaler.scaleIn.coolDownMinute | Body | Integer | X | Wait time after scale in |
| workload.autoScaler.scaleIn.condition | Body | List | X | Scale in condition |
| workload.autoScaler.scaleIn.condition.resource | Body | String | X | Resources based on scale in conditions<ul><li>cpu</li><li>memory</li><li>gpu</li><li>gpu-memory</li></ul> |
| workload.autoScaler.scaleIn.condition.threshold | Body | Integer | X | Scale in condition resource usage (1-100) |
| workload.autoScaler.scaleIn.condition.duration | Body | Integer | X | Scale in condition resource usage hold time (minutes) |
| workload.securityGroups | Body | List | X | Information on SecurityGroups |
| workload.securityGroups.id | Body | String | O | SecurityGroups ID |
| workload.tasks | Body | Array | O | Tasks of workload |
| workload.tasks.id | Body | UUID | O | Task ID |
| workload.tasks.containers | Body | Array | O | List of containers in a task |
| workload.tasks.containers.name | Body | String | O | Container name |
| workload.tasks.containers.type | Body | String | O | Container Type<ul><li>normal: Normal</li><li>init: Initialize</li></ul>|
| workload.tasks.containers.image | Body | String | O | Container image |
| workload.tasks.containers.ip | Body | String | X | Container IP |
| workload.tasks.containers.cpus | Body | Float | O | Number of CPU assigned to container |
| workload.tasks.containers.memoryLimit | Body | Object | O | Information on memory assigned to container |
| workload.tasks.containers.memoryLimit.hard | Body | Integer | O | Memory assigned to container (MiB) |
| workload.tasks.containers.gpuFlavor | Body | String | X | Information on GPU Flavor assigned to container |
| workload.tasks.containers.ports | Body | Array | X | Container port information |
| workload.tasks.containers.ports.containerPort | Body | Integer | O | Container port |
| workload.tasks.containers.ports.protocol | Body | String | O | Container protocol<ul><li>TCP</li><li>UDP</li><li>HTTP</li><li>HTTPS</li><li>TERMINATED_HTTPS</li></ul> |
| workload.tasks.containers.command | Body | String List | X | Command to run when container starts |
| workload.tasks.containers.args | Body | String List | X | Arguments used when the container is started |
| workload.tasks.containers.workDirectory | Body | String | X | Task Directory of Container |
| workload.tasks.containers.env | Body | Array | X | Container environment variable |
| workload.tasks.containers.env.name | Body | String | O | Container environment variable name |
| workload.tasks.containers.env.value | Body | String | O | Container environment variable value |
| workload.tasks.containers.postStart | Body | String List | X | Commands that run immediately after container creation |
| workload.tasks.containers.preStop | Body | String List | X | Commands executed just before container termination |
| workload.tasks.containers.configs | Body | List | X | ConfigMap information used by containers |
| workload.tasks.containers.configs.id | Body | Integer | O | ConfigMap ID |
| workload.tasks.containers.configs.type | Body | String | O | Service type to get ConfigMap information<ul><li>obs: Object Storage</li></ul> |
| workload.tasks.containers.configs.value | Body | String | O | Object URL |
| workload.tasks.containers.configs.mountPath | Body | String | O | Container mount path |
| workload.tasks.containers.secrets | Body | List | X | Secret information used by containers |
| workload.tasks.containers.secrets.type | Body | String | O | Service type to get secret information<ul><li>skm: Secure Key Manager</li></ul> |
| workload.tasks.containers.secrets.value | Body | String | O | Key ID |
| workload.tasks.containers.secrets.mountPath | Body | String | O | Container mount path |
| workload.tasks.containers.volumes | Body | Array | X | Information on storage used in containers |
| workload.tasks.containers.volumes.name | Body | String | O | Storage Name |
| workload.tasks.containers.volumes.path | Body | String | O | NAS Storage Connection Path |
| workload.tasks.containers.volumes.mountPath | body | String | X | Connection path of the container (default: /mnt) |
| workload.tasks.containers.probe | Body | List | X | Setting up container probes |
| workload.tasks.containers.probe.type | Body | String | O | Container Probe Type<ul><li>startup</li><li>liveness</li></ul> |
| workload.tasks.containers.probe.failureThreshold | Body | Integer | O | Probe failure criteria |
| workload.tasks.containers.probe.initialDelaySeconds | Body | Integer | O | Probe startup latency |
| workload.tasks.containers.probe.periodSeconds | Body | String | O | Probe execution interval |
| workload.tasks.containers.probe.timeoutSeconds | Body | String | O | Probe execution timeout |
| workload.tasks.containers.probe.exec | Body | String List | O | Command to run a probe |
| workload.tasks.containers.stopTimeout | Body | Integer | X | Initialization container execution timeout (seconds) |
| workload.tasks.containers.state | Body | String | O | Container status |
| workload.tasks.containers.startedAt | Body | String | O | Container start time |
| workload.tasks.containers.finishedAt | Body | String | X | Initialization container completion time |
| workload.tasks.containers.restartCount | Body | String | O | The number of time that container restarted |

<details>
  <summary>Example</summary>

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

### View Workload Log

Retrieves the container logs for your workload.

```bash
GET /ncs/v1.0/appkeys/{appKey}/workloads/{workloadId}/tasks/{taskId}/logs?container={ContainerName}&from={YYYY-MM-DDThh:mm:ssZ}&to={YYYY-MM-DDThh:mm:ssZ}
x-nhn-authorization: {token}
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| workloadId | URL | String | O | Workload ID |
| taskId | URL | String | O | Task ID |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|
| containerName | Query | String | O | Container name |
| from | Query | String | X | Log start time (default: 5 minutes before current) |
| to | Query | String | X | Log end time (default: current time) |
| page | Query | String | X | Page to search |
| size | Query | String | X | Page size to view (default: 100) |

#### Response

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| logs | Body | Array | O | Container log list |
| log | Body | String | O | Log level |
| logType | Body | String | O | Log occurrence time (UTC) |

<details>
  <summary>Example</summary>

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

### View Workload Event

Retrieves an workload event.

```bash
GET /ncs/v1.0/appkeys/{appKey}/workloads/{workloadId}/tasks/{taskId}/events
x-nhn-authorization: {token}
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| workloadId | URL | String | O | Workload ID |
| taskId | URL | String | O | Task ID |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|
| type | Query | Integer | X | Event type<ul><li>Normal</li><li>Warning</li></ul> |
| q | Query | String | X | Filtering event content |
| page | Query | String | X | Page to search |
| size | Query | String | X | Page size to retrieve (default: 10) |
| from | Query | String | X | Start time when the event last occurred (default: 1 hour before the current time) |
| to | Query | String | X | End time when the event last occurred (default: current time) |

#### Response

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| events | Body | Array | O | Events |
| type | Body | String | O | Event type<ul><li>Normal</li><li>Warning</li></ul> |
| reason | Body | String | O | Cause of the event |
| message | Body | String | O | Event content |
| createTimestamp | Body | String | O | Date of first occurrence of event (UTC) |
| lastTimestamp | Body | String | O | Date of last occurrence of event (UTC) |
| count | Body | Integer | O | Number of times event occurred |

<details>
  <summary>Example</summary>

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

### View a List of Workload Run History

Retrieves a list of workload run history.

```bash
GET /ncs/v1.0/appkeys/{appKey}/workloads/{workloadId}/history
x-nhn-authorization: {token}
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| workloadId | URL | String | O | Workload ID |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|
| page | Query | Integer | X | Page number to retrieve |
| size | Query | Integer | X | Page size to retrieve (default: 10) |
| sort | Query | String | X | Name of the field to sort by<br>Prefix field names with `-`for reverse sorting<br>Example: `sort=-id` |

#### Response

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| history | Body | Array | O | History list |
| history.id | Body | Integer | O | History ID |
| history.createdAt | Body | String | O | Deployment time |
| history.deletedAt | Body | String | O | End time |
| history.templateId | Body | UUID | O | Template ID used by workloads |
| history.templateVersion | Body | UUID | O | Template version used by the workload |
| history.name | Body | String | O | Template name |
| history.status | Body | String | O | Status<ul><li>Succeeded</li><li>Terminated</li><liPending</li></ul> |

<details>
  <summary>Example</summary>

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

### View Workload Run History

Retrieves a run history for an individual workload.

```bash
GET /ncs/v1.0/appkeys/{appKey}/workloads/{workloadId}/history/{historyId}
x-nhn-authorization: {token}
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| workloadId | URL | String | O | Workload ID |
| historyId | URL | Integer | O | History ID |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|

#### Response

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| history | Body | Object | O | History Information |
| history.id | Body | Integer | O | History ID |
| history.createdAt | Body | String | O | Execution time |
| history.deletedAt | Body | String | O | End time |
| history.templateId | Body | UUID | O | Template ID used by workloads |
| history.name | Body | String | O | Template name used by workloads |
| history.status | Body | String | O | Status<ul><li>Succeeded</li><li>Terminated</li><li>Pending</li></ul> |
| template | Body | Object | O | Template information |
| template.id | Body | UUID | O | Template ID |
| template.version | Body | String | O | Template version |
| template.name | Body | String | O | Template name |
| template.createdAt | Body | String | O | Created time (UTC) |
| template.description | Body | String | X | Template description |
| template.versionDescription | Body | String | X | Template version description |
| template.networks | Body | Array | O | Template network information |
| template.networks.vpcId | Body | String | O | VPC ID of template |
| template.networks.subnetId | Body | String | O | Subnet ID of template |
| template.dnsConfig | Body | String List | X | DNS server information set up in the container |
| template.hostAliases | Body | List | X | Information set up in container `/etc/hosts` |
| template.hostAliases.ip | Body | String | O | IP of the hostnames set in the container |
| template.hostAliases.hostnames | Body | String List | O | hostnames of the IP set in the container |
| template.containers | Body | Array | O | List of containers in a template |
| template.containers.name | Body | String | O | Container name |
| template.containers.type | Body | String | O | Container Type<ul><li>normal: Normal</li><li>init: Initialize</li></ul>|
| template.containers.image | Body | String | O | Container image |
| template.containers.cpus | Body | Float | O | Number of CPU assigned to container |
| template.containers.memoryLimit | Body | Object | O | Information on memory assigned to container |
| template.containers.memoryLimit.hard | Body | Integer | O | Memory assigned to container (MiB) |
| template.containers.gpuFlavor | Body | String | X | Information on GPU Flavor<ul><li>ncs1.g1m5</li><li>ncs1.g2m10</li></ul> |
| template.containers.ports | Body | Array | X | Information on ports used in containers |
| template.containers.ports.containerPort | Body | Integer | O | Container port |
| template.containers.ports.protocol | Body | String | O | Container protocol<ul><li>TCP</li><li>UDP</li><li>HTTP</li><li>HTTPS</li><li>TERMINATED_HTTPS</li></ul> |
| template.containers.command | Body | String List | X | Command to run when container starts |
| template.containers.args | Body | String List | X | Arguments used when the container is started |
| template.containers.workDirectory | Body | String | X | Task Directory of Container |
| template.containers.env | Body | Array | X | Container environment variable |
| template.containers.env.name | Body | String | O | Container environment variable name |
| template.containers.env.value | Body | String | O | Container environment variable value |
| template.containers.postStart | Body | String List | X | Commands that run immediately after container creation |
| template.containers.preStop | Body | String List | X | Commands executed just before container termination |
| template.containers.configs | Body | List | X | ConfigMap information used by containers |
| template.containers.configs.id | Body | Integer | O | ConfigMap ID |
| template.containers.configs.type | Body | String | O | Service type to get ConfigMap information<ul><li>obs: Object Storage</li></ul> |
| template.containers.configs.value | Body | String | O | Object URL |
| template.containers.configs.mountPath | Body | String | O | Container mount path |
| template.containers.secrets | Body | List | X | Secret information used by containers |
| template.containers.secrets.type | Body | String | O | Service type to get secret information<ul><li>skm: Secure Key Manager</li></ul> |
| template.containers.secrets.value | Body | String | O | Key ID |
| template.containers.secrets.mountPath | Body | String | O | Container mount path |
| template.containers.volumes | Body | Array | X | Information on NAS storage used in containers |
| template.containers.volumes.name | Body | String | O | Storage Name |
| template.containers.volumes.path | Body | String | O | NAS Storage Connection Path |
| template.containers.volumes.mountPath | body | String | X | Connection path for containers |
| template.containers.probe | Body | List | X | Setting up container probes |
| template.containers.probe.type | Body | String | O | Container Probe Type<ul><li>startup</li><li>liveness</li></ul> |
| template.containers.probe.failureThreshold | Body | Integer | O | Probe failure criteria |
| template.containers.probe.initialDelaySeconds | Body | Integer | O | Probe startup latency |
| template.containers.probe.periodSeconds | Body | Integer | O | Probe execution interval |
| template.containers.probe.timeoutSeconds | Body | Integer | O | Probe execution timeout |
| template.containers.probe.exec | Body | String List | O | Command to run a probe |
| template.containers.stopTimeout | Body | Integer | X | Initialization container execution timeout (seconds) |

<details>
  <summary>Example</summary>

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

### View workload scheduled run history

Views the history of a scheduled run.

```bash
GET /ncs/v1.0/appkeys/{appKey}/workloads/{workloadId}/schedulehistory
x-nhn-authorization: {token}
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| workloadId | URL | String | O | Workload ID |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|
| page | Query | Integer | X | Page number to retrieve |
| size | Query | Integer | X | Page size to retrieve (default: 10) |

#### Response

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| schedulehistory | Body | List | O | Scheduled Execution History |
| schedulehistory.id | Body | String | O | Task ID |
| schedulehistory.createdAt | Body | String | O | Start time |
| schedulehistory.finishedAt | Body | Object | O | End time |
| schedulehistory.status | Body | String | O | Scheduled task status<ul><li>Error</li><li>Completed</li><li>Waiting</li><li>Running</li></ul> |

<details>
  <summary>Example</summary>

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

### Create Workload

Creates a workload.

```bash
POST /ncs/v1.0/appkeys/{appKey}/workloads
Content-Type: application/json
x-nhn-authorization: {token}
```

#### Request

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|
| workload | Body | Object | O | Workload information |
| workload.name | Body | String | O | Workload name |
| workload.type | Body | String | X | Deployment controller (default:deployment)<ul><li>deployment</li><li>statefulset</li></ul> |
| workload.templateId | Body | String | O | Template ID of workloads |
| workload.templateVersion | Body | String | X | Template version of the workload (default: latest) |
| workload.desired | Body | Integer | O | Number of workload tasks requested |
| workload.internalLBTimeout | Body | Integer | X | Internal request response latency |
| workload.loadBalancing | Body | Object | O | Information on workload load balancer |
| workload.loadBalancing.enabled | Body | Boolean | O | Whether to use workload load balancer |
| workload.loadBalancing.floatingIp | Body | Boolean | O | Whether to use the workload load balancer floating IP |
| workload.loadBalancing.healthMonitor | Body | Object | X | About checking the health of the load balancer |
| workload.loadBalancing.healthMonitor.delay | Body | Integer | O | How often to check status |
| workload.loadBalancing.healthMonitor.timeout | Body | Integer | O | Maximum response latency |
| workload.loadBalancing.healthMonitor.maxRetries | Body | Integer | O | Maximum number of retries |
| workload.loadBalancing.healthMonitor.httpMethod | Body | String | X | HTTP Methods<ul><li>GET</li></ul> |
| workload.loadBalancing.healthMonitor.expectedCodes | Body | String | X | HTTP status codes<ul><li>200</li><li>200,202</li><li>200-204</li></ul> |
| workload.loadBalancing.healthMonitor.urlPath | Body | String | X | HTTP URL |
| workload.loadBalancing.certificate | Body | String | X | Certificates used by the load balancer when using TERMINATED_HTTPS |
| workload.loadBalancing.privateKey | Body | String | X | Private key used by the load balancer when using TERMINATED_HTTPS |
| workload.loadBalancing.tlsVersion | Body | String | X | TLS version when using TERMINATED_HTTPS<ul><li>SSLv3</li><li>TLSv1.0</li><li>TLSv1.0_2016</li><li>TLSv1.1</li><li>TLSv1.2</li><li>TLSv1.3</li></ul> |
| workload.loadBalancing.containerHref | Body | String | X | Secret container ID<li>If you separately registered a certificate and private key to use in TERMINATED_HTTPS using the Load Balancer API, use it.</li> |
| workload.loadBalancing.ipAclGroupsBinding | Body | List | X | List of IP access control groups to apply to the load balancer |
| workload.loadBalancing.ipAclGroupsBinding.ipAclGroupId | Body | String | O | IP Access Control Group ID |
| workload.schedule | Body | Object | X | About scheduled execution settings |
| workload.schedule.timeZone | Body | String | O | Schedule execution base time<ul><li>Example: Asia/Seoul, UTC</li><li>[List of tz database time zones](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)</li></ul> |
| workload.schedule.cron | Body | String | O | Scheduled Execution Cron Expressions |
| workload.schedule.jobsHistoryLimit | Body | Integer | O | Number of scheduled execution history |
| workload.schedule.concurrencyPolicy | Body | String | O | Concurrency policy<ul><li>Forbid</li><li>Replace</li></ul> |
| workload.internalLoadBalancing | Body | Object | X | Internal load balancer information |
| workload.internalLoadBalancing.enalbed | Body | Boolean | O | Whether to use an internal load balancer |
| workload.internalLoadBalancing.type | Body | String | X | How to assign internal load balancer IPs<ul><li>Dynamic: Automatically assigned</li><li>static: Specify an IP</li></ul> |
| workload.internalLoadBalancing.ip | Body | String | X | Specify internal load balancer IP |
| workload.privateDns | Body | Object | X | Determinine whether to register workload working IPs with Private DNS |
| workload.privateDns.ttl | Body | Integer | O | TTL value of a recordset |
| workload.privateDns.zoneId | Body | String | O | The Private DNS Zone ID used by the workload |
| workload.privateDns.domain | Body | String | O | About domains registered with Private DNS |
| workload.activeDeadline | Body | Object | X | Information on workload scheduling end  |
| workload.activeDeadline.timeZone | Body | String | O | Scheduling end base time<li>Example: Asia/Seoul, UTC</li> |
| workload.activeDeadline.timeOffset | Body | String | O | Offset the scheduling end base time |
| workload.activeDeadline.time | Body | String | O | Scheduling end time |
| workload.autoScaler | Body | Object | X | Information on AutoScaler settings |
| workload.autoScaler.scaleOut | Body | Object | O | Information on ScaleOut |
| workload.autoScaler.scaleOut.enabled | Body | Boolean | O | Whether to use ScaleOut |
| workload.autoScaler.scaleOut.maxReplicas | Body | Integer | X | Maximum number of jobs for autoscaling |
| workload.autoScaler.scaleOut.coolDownMinute | Body | Integer | X | Wait time after scaling up |
| workload.autoScaler.scaleOut.condition | Body | List | X | Scale out condition |
| workload.autoScaler.scaleOut.condition.resource | Body | String | X | Resources based on scale out conditions<ul><li>cpu</li><li>memory</li><li>gpu</li><li>gpu-memory</li></ul> |
| workload.autoScaler.scaleOut.condition.threshold | Body | Integer | X | Scale out condition resource usage (1-100) |
| workload.autoScaler.scaleOut.condition.duration | Body | Integer | X | Scale out condition resource usage hold time (minutes) |
| workload.autoScaler.scaleIn | Body | Object | X | Information on ScaleIn |
| workload.autoScaler.scaleIn.enabled | Body | Boolean | O | Whether to use ScaleIn |
| workload.autoScaler.scaleIn.minReplicas | Body | Integer | X | Minimum number of jobs for autoscaling |
| workload.autoScaler.scaleIn.coolDownMinute | Body | Integer | X | Wait time after scale in |
| workload.autoScaler.scaleIn.condition | Body | List | X | Scale in condition |
| workload.autoScaler.scaleIn.condition.resource | Body | String | X | Resources based on scale in conditions<ul><li>cpu</li><li>memory</li><li>gpu</li><li>gpu-memory</li></ul> |
| workload.autoScaler.scaleIn.condition.threshold | Body | Integer | X | Scale in condition resource usage (1-100) |
| workload.autoScaler.scaleIn.condition.duration | Body | Integer | X | Scale in condition resource usage hold time (minutes) |
| workload.securityGroups | Body | List | X | Information on SecurityGroups |
| workload.securityGroups.id | Body | String | O | SecurityGroups ID |

<details>
  <summary>Example</summary>

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

#### Response

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| workload | Body | Object | O | Workload information |
| workload.id | Body | UUID | O | Workload ID |
| workload.name | Body | String | O | Workload name |
| workload.type | Body | String | O | Deployment controller<ul><li>deployment</li><li>statefulset</li></ul> |
| workload.templateId | Body | String | O | Template ID of workloads |
| workload.templateVersion | Body | String | O | Template version of the workload |
| workload.createdAt | Body | String | O | Created time (UTC) |
| workload.desired | Body | Integer | O | Number of workload tasks requested |
| workload.internalLBTimeout | Body | Integer | X | Internal request response latency |
| workload.loadBalancing | Body | Object | O | Information on workload load balancer |
| workload.loadBalancing.enabled | Body | Boolean | O | Whether to use workload load balancer |
| workload.loadBalancing.floatingIp | Body | Boolean | O | Whether to use the workload load balancer floating IP |
| workload.loadBalancing.healthMonitor | Body | Object | X | About checking the health of the load balancer |
| workload.loadBalancing.healthMonitor.delay | Body | Integer | O | How often to check status |
| workload.loadBalancing.healthMonitor.timeout | Body | Integer | O | Maximum response latency |
| workload.loadBalancing.healthMonitor.maxRetries | Body | Integer | O | Maximum number of retries |
| workload.loadBalancing.healthMonitor.httpMethod | Body | String | X | HTTP Methods<ul><li>GET</li></ul> |
| workload.loadBalancing.healthMonitor.expectedCodes | Body | String | X | HTTP status codes<ul><li>200</li><li>200,202</li><li>200-204</li></ul> |
| workload.loadBalancing.healthMonitor.urlPath | Body | String | X | HTTP URL |
| workload.loadBalancing.certificate | Body | String | X | Certificates used by the load balancer when using TERMINATED_HTTPS |
| workload.loadBalancing.privateKey | Body | String | X | Private key used by the load balancer when using TERMINATED_HTTPS |
| workload.loadBalancing.tlsVersion | Body | String | X | TLS version when using TERMINATED_HTTPS<ul><li>SSLv3</li><li>TLSv1.0</li><li>TLSv1.0_2016</li><li>TLSv1.1</li><li>TLSv1.2</li><li>TLSv1.3</li></ul> |
| workload.loadBalancing.containerHref | Body | String | X | Secret container ID<li>If you separately registered a certificate and private key to use in TERMINATED_HTTPS using the Load Balancer API, use it.</li> |
| workload.loadBalancing.ipAclGroupsBinding | Body | List | X | List of IP access control groups to apply to the load balancer |
| workload.loadBalancing.ipAclGroupsBinding.ipAclGroupId | Body | String | O | IP Access Control Group ID |
| workload.schedule | Body | Object | X | About scheduled execution settings |
| workload.schedule.timeZone | Body | String | O | Schedule execution base time<ul><li>Example: Asia/Seoul, UTC</li><li>[List of tz database time zones](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)</li></ul> |
| workload.schedule.cron | Body | String | O | Scheduled Execution Cron Expressions |
| workload.schedule.jobsHistoryLimit | Body | Integer | O | Number of scheduled execution history |
| workload.schedule.concurrencyPolicy | Body | String | O | Concurrency policy<ul><li>Forbid</li><li>Replace</li></ul> |
| workload.schedule.timeOffset | Body | String | O | Offset the scheduled execution base time |
| workload.internalLoadBalancing | Body | Object | X | Internal load balancer information |
| workload.internalLoadBalancing.enalbed | Body | Boolean | O | Whether to use an internal load balancer |
| workload.internalLoadBalancing.type | Body | String | X | How to assign internal load balancer IPs<ul><li>Dynamic: Automatically assigned</li><li>static: Specify an IP</li></ul> |
| workload.internalLoadBalancing.ip | Body | String | X | Specify internal load balancer IP |
| workload.privateDns | Body | Object | X | Determinine whether to register workload working IPs with Private DNS |
| workload.privateDns.ttl | Body | Integer | O | TTL value of a recordset |
| workload.privateDns.zoneId | Body | String | O | The Private DNS Zone ID used by the workload |
| workload.privateDns.domain | Body | String | O | About domains registered with Private DNS |
| workload.activeDeadline | Body | Object | X | Information on workload scheduling end  |
| workload.activeDeadline.timeZone | Body | String | O | Scheduling end base time<li>Example: Asia/Seoul, UTC</li> |
| workload.activeDeadline.timeOffset | Body | String | O | Offset the scheduling end base time |
| workload.activeDeadline.time | Body | String | O | Scheduling end time |
| workload.autoScaler | Body | Object | X | Information on AutoScaler settings |
| workload.autoScaler.scaleOut | Body | Object | O | Information on ScaleOut |
| workload.autoScaler.scaleOut.enabled | Body | Boolean | O | Whether to use ScaleOut |
| workload.autoScaler.scaleOut.maxReplicas | Body | Integer | X | Maximum number of jobs for autoscaling |
| workload.autoScaler.scaleOut.coolDownMinute | Body | Integer | X | Wait time after scaling up |
| workload.autoScaler.scaleOut.condition | Body | List | X | Scale out condition |
| workload.autoScaler.scaleOut.condition.resource | Body | String | X | Resources based on scale out conditions<ul><li>cpu</li><li>memory</li><li>gpu</li><li>gpu-memory</li></ul> |
| workload.autoScaler.scaleOut.condition.threshold | Body | Integer | X | Scale out condition resource usage (1-100) |
| workload.autoScaler.scaleOut.condition.duration | Body | Integer | X | Scale out condition resource usage hold time (minutes) |
| workload.autoScaler.scaleIn | Body | Object | X | Information on ScaleIn |
| workload.autoScaler.scaleIn.enabled | Body | Boolean | O | Whether to use ScaleIn |
| workload.autoScaler.scaleIn.minReplicas | Body | Integer | X | Minimum number of jobs for autoscaling |
| workload.autoScaler.scaleIn.coolDownMinute | Body | Integer | X | Wait time after scale in |
| workload.autoScaler.scaleIn.condition | Body | List | X | Scale in condition |
| workload.autoScaler.scaleIn.condition.resource | Body | String | X | Resources based on scale in conditions<ul><li>cpu</li><li>memory</li><li>gpu</li><li>gpu-memory</li></ul> |
| workload.autoScaler.scaleIn.condition.threshold | Body | Integer | X | Scale in condition resource usage (1-100) |
| workload.autoScaler.scaleIn.condition.duration | Body | Integer | X | Scale in condition resource usage hold time (minutes) |
| workload.securityGroups | Body | List | X | Information on SecurityGroups |
| workload.securityGroups.id | Body | String | O | SecurityGroups ID |

<details>
  <summary>Example</summary>

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

### Change Workload

Changes a workload.

```bash
PUT /ncs/v1.0/appkeys/{appKey}/workloads/{workloadId}
Content-Type: application/json
x-nhn-authorization: {token}
```

#### Request

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| workloadId | URL | String | O | Workload ID |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|
| workload | Body | Object | O | Workload information |
| workload.name | Body | String | O | Workload name |
| workload.templateId | Body | String | O | Template ID of workloads |
| workload.templateVersion | Body | String | O | Template version of the workload |
| workload.desired | Body | Integer | O | Number of workload tasks requested |
| workload.internalLBTimeout | Body | Integer | X | Internal request response latency |
| workload.loadBalancing | Body | Object | O | Information on workload load balancer |
| workload.loadBalancing.enabled | Body | Boolean | O | Whether to use workload load balancer |
| workload.loadBalancing.floatingIp | Body | Boolean | O | Whether to use the workload load balancer floating IP |
| workload.loadBalancing.healthMonitor | Body | Object | X | About checking the health of the load balancer |
| workload.loadBalancing.healthMonitor.delay | Body | Integer | O | How often to check status |
| workload.loadBalancing.healthMonitor.timeout | Body | Integer | O | Maximum response latency |
| workload.loadBalancing.healthMonitor.maxRetries | Body | Integer | O | Maximum number of retries |
| workload.loadBalancing.healthMonitor.httpMethod | Body | String | X | HTTP Methods<ul><li>GET</li></ul> |
| workload.loadBalancing.healthMonitor.expectedCodes | Body | String | X | HTTP status codes<ul><li>200</li><li>200,202</li><li>200-204</li></ul> |
| workload.loadBalancing.healthMonitor.urlPath | Body | String | X | HTTP URL |
| workload.loadBalancing.certificate | Body | String | X | Certificates used by the load balancer when using TERMINATED_HTTPS |
| workload.loadBalancing.privateKey | Body | String | X | Private key used by the load balancer when using TERMINATED_HTTPS |
| workload.loadBalancing.tlsVersion | Body | String | X | TLS version when using TERMINATED_HTTPS<ul><li>SSLv3</li><li>TLSv1.0</li><li>TLSv1.0_2016</li><li>TLSv1.1</li><li>TLSv1.2</li><li>TLSv1.3</li></ul> |
| workload.loadBalancing.containerHref | Body | String | X | Secret container ID<li>If you separately registered a certificate and private key to use in TERMINATED_HTTPS using the Load Balancer API, use it.</li> |
| workload.loadBalancing.ipAclGroupsBinding | Body | List | X | List of IP access control groups to apply to the load balancer |
| workload.loadBalancing.ipAclGroupsBinding.ipAclGroupId | Body | String | O | IP Access Control Group ID |
| workload.schedule | Body | Object | X | About scheduled execution settings |
| workload.schedule.timeZone | Body | String | O | Schedule execution base time<ul><li>Example: Asia/Seoul, UTC</li><li>[List of tz database time zones](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)</li></ul> |
| workload.schedule.cron | Body | String | O | Scheduled Execution Cron Expressions |
| workload.schedule.jobsHistoryLimit | Body | Integer | O | Number of scheduled execution history |
| workload.schedule.concurrencyPolicy | Body | String | O | Concurrency policy<ul><li>Forbid</li><li>Replace</li></ul> |
| workload.internalLoadBalancing | Body | Object | X | Internal load balancer information |
| workload.internalLoadBalancing.enalbed | Body | Boolean | O | Whether to use an internal load balancer |
| workload.internalLoadBalancing.type | Body | String | X | How to assign internal load balancer IPs<ul><li>Dynamic: Automatically assigned</li><li>static: Specify an IP</li></ul> |
| workload.internalLoadBalancing.ip | Body | String | X | Specify internal load balancer IP |
| workload.activeDeadline | Body | Object | X | Information on workload scheduling end  |
| workload.activeDeadline.timeZone | Body | String | O | Scheduling end base time<li>Example: Asia/Seoul, UTC</li> |
| workload.activeDeadline.timeOffset | Body | String | O | Offset the scheduling end base time |
| workload.activeDeadline.time | Body | String | O | Scheduling end time |
| workload.autoScaler | Body | Object | X | Information on AutoScaler settings |
| workload.autoScaler.scaleOut | Body | Object | O | Information on ScaleOut |
| workload.autoScaler.scaleOut.enabled | Body | Boolean | O | Whether to use ScaleOut |
| workload.autoScaler.scaleOut.maxReplicas | Body | Integer | X | Maximum number of jobs for autoscaling |
| workload.autoScaler.scaleOut.coolDownMinute | Body | Integer | X | Wait time after scaling up |
| workload.autoScaler.scaleOut.condition | Body | List | X | Scale out condition |
| workload.autoScaler.scaleOut.condition.resource | Body | String | X | Resources based on scale out conditions<ul><li>cpu</li><li>memory</li><li>gpu</li><li>gpu-memory</li></ul> |
| workload.autoScaler.scaleOut.condition.threshold | Body | Integer | X | Scale out condition resource usage (1-100) |
| workload.autoScaler.scaleOut.condition.duration | Body | Integer | X | Scale out condition resource usage hold time (minutes) |
| workload.autoScaler.scaleIn | Body | Object | X | Information on ScaleIn |
| workload.autoScaler.scaleIn.enabled | Body | Boolean | O | Whether to use ScaleIn |
| workload.autoScaler.scaleIn.minReplicas | Body | Integer | X | Minimum number of jobs for autoscaling |
| workload.autoScaler.scaleIn.coolDownMinute | Body | Integer | X | Wait time after scale in |
| workload.autoScaler.scaleIn.condition | Body | List | X | Scale in condition |
| workload.autoScaler.scaleIn.condition.resource | Body | String | X | Resources based on scale in conditions<ul><li>cpu</li><li>memory</li><li>gpu</li><li>gpu-memory</li></ul> |
| workload.autoScaler.scaleIn.condition.threshold | Body | Integer | X | Scale in condition resource usage (1-100) |
| workload.autoScaler.scaleIn.condition.duration | Body | Integer | X | Scale in condition resource usage hold time (minutes) |
| workload.securityGroups | Body | List | X | Information on SecurityGroups |
| workload.securityGroups.id | Body | String | O | SecurityGroups ID |

<details>
  <summary>Example</summary>

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

#### Response

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| workloads | Body | Object | O | Workload information |
| workload.id | Body | UUID | O | Workload ID |
| workload.name | Body | String | O | Workload name |
| workload.type | Body | String | O | Deployment controller<ul><li>deployment</li><li>statefulset</li></ul> |
| workload.templateId | Body | String | O | Template ID of workloads |
| workload.templateVersion | Body | String | O | Template version of the workload |
| workload.createdAt | Body | String | O | Created time (UTC) |
| workload.desired | Body | Integer | O | Number of workload tasks requested |
| workload.internalLBTimeout | Body | Integer | X | Internal request response latency |
| workload.loadBalancing | Body | Object | O | Information on workload load balancer |
| workload.loadBalancing.enabled | Body | Boolean | O | Whether to use workload load balancer |
| workload.loadBalancing.floatingIp | Body | Boolean | O | Whether to use the workload load balancer floating IP |
| workload.loadBalancing.healthMonitor | Body | Object | X | About checking the health of the load balancer |
| workload.loadBalancing.healthMonitor.delay | Body | Integer | O | How often to check status |
| workload.loadBalancing.healthMonitor.timeout | Body | Integer | O | Maximum response latency |
| workload.loadBalancing.healthMonitor.maxRetries | Body | Integer | O | Maximum number of retries |
| workload.loadBalancing.healthMonitor.httpMethod | Body | String | X | HTTP Methods<ul><li>GET</li></ul> |
| workload.loadBalancing.healthMonitor.expectedCodes | Body | String | X | HTTP status codes<ul><li>200</li><li>200,202</li><li>200-204</li></ul> |
| workload.loadBalancing.healthMonitor.urlPath | Body | String | X | HTTP URL |
| workload.loadBalancing.certificate | Body | String | X | Certificates used by the load balancer when using TERMINATED_HTTPS |
| workload.loadBalancing.privateKey | Body | String | X | Private key used by the load balancer when using TERMINATED_HTTPS |
| workload.loadBalancing.tlsVersion | Body | String | X | TLS version when using TERMINATED_HTTPS<ul><li>SSLv3</li><li>TLSv1.0</li><li>TLSv1.0_2016</li><li>TLSv1.1</li><li>TLSv1.2</li><li>TLSv1.3</li></ul> |
| workload.loadBalancing.containerHref | Body | String | X | Secret container ID<li>If you separately registered a certificate and private key to use in TERMINATED_HTTPS using the Load Balancer API, use it.</li> |
| workload.loadBalancing.ipAclGroupsBinding | Body | List | X | List of IP access control groups to apply to the load balancer |
| workload.loadBalancing.ipAclGroupsBinding.ipAclGroupId | Body | String | O | IP Access Control Group ID |
| workload.schedule | Body | Object | X | About scheduled execution settings |
| workload.schedule.timeZone | Body | String | O | Schedule execution base time<ul><li>Example: Asia/Seoul, UTC</li><li>[List of tz database time zones](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)</li></ul> |
| workload.schedule.cron | Body | String | O | Scheduled Execution Cron Expressions |
| workload.schedule.jobsHistoryLimit | Body | Integer | O | Number of scheduled execution history |
| workload.schedule.concurrencyPolicy | Body | String | O | Concurrency policy<ul><li>Forbid</li><li>Replace</li></ul> |
| workload.schedule.timeOffset | Body | String | O | Offset the scheduled execution base time |
| workload.internalLoadBalancing | Body | Object | X | Internal load balancer information |
| workload.internalLoadBalancing.enalbed | Body | Boolean | O | Whether to use an internal load balancer |
| workload.internalLoadBalancing.type | Body | String | X | How to assign internal load balancer IPs<ul><li>Dynamic: Automatically assigned</li><li>static: Specify an IP</li></ul> |
| workload.internalLoadBalancing.ip | Body | String | X | Specify internal load balancer IP |
| workload.privateDns | Body | Object | X | Determinine whether to register workload working IPs with Private DNS |
| workload.privateDns.ttl | Body | Integer | O | TTL value of a recordset |
| workload.privateDns.zoneId | Body | String | O | The Private DNS Zone ID used by the workload |
| workload.privateDns.domain | Body | String | O | About domains registered with Private DNS |
| workload.activeDeadline | Body | Object | X | Information on workload scheduling end  |
| workload.activeDeadline.timeZone | Body | String | O | Scheduling end base time<li>Example: Asia/Seoul, UTC</li> |
| workload.activeDeadline.timeOffset | Body | String | O | Offset the scheduling end base time |
| workload.activeDeadline.time | Body | String | O | Scheduling end time |
| workload.autoScaler | Body | Object | X | Information on AutoScaler settings |
| workload.autoScaler.scaleOut | Body | Object | O | Information on ScaleOut |
| workload.autoScaler.scaleOut.enabled | Body | Boolean | O | Whether to use ScaleOut |
| workload.autoScaler.scaleOut.maxReplicas | Body | Integer | X | Maximum number of jobs for autoscaling |
| workload.autoScaler.scaleOut.coolDownMinute | Body | Integer | X | Wait time after scaling up |
| workload.autoScaler.scaleOut.condition | Body | List | X | Scale out condition |
| workload.autoScaler.scaleOut.condition.resource | Body | String | X | Resources based on scale out conditions<ul><li>cpu</li><li>memory</li><li>gpu</li><li>gpu-memory</li></ul> |
| workload.autoScaler.scaleOut.condition.threshold | Body | Integer | X | Scale out condition resource usage (1-100) |
| workload.autoScaler.scaleOut.condition.duration | Body | Integer | X | Scale out condition resource usage hold time (minutes) |
| workload.autoScaler.scaleIn | Body | Object | X | Information on ScaleIn |
| workload.autoScaler.scaleIn.enabled | Body | Boolean | O | Whether to use ScaleIn |
| workload.autoScaler.scaleIn.minReplicas | Body | Integer | X | Minimum number of jobs for autoscaling |
| workload.autoScaler.scaleIn.coolDownMinute | Body | Integer | X | Wait time after scale in |
| workload.autoScaler.scaleIn.condition | Body | List | X | Scale in condition |
| workload.autoScaler.scaleIn.condition.resource | Body | String | X | Resources based on scale in conditions<ul><li>cpu</li><li>memory</li><li>gpu</li><li>gpu-memory</li></ul> |
| workload.autoScaler.scaleIn.condition.threshold | Body | Integer | X | Scale in condition resource usage (1-100) |
| workload.autoScaler.scaleIn.condition.duration | Body | Integer | X | Scale in condition resource usage hold time (minutes) |
| workload.securityGroups | Body | List | X | Information on SecurityGroups |
| workload.securityGroups.id | Body | String | O | SecurityGroups ID |

<details>
  <summary>Example</summary>

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

### Changing workload parts

You can only modify a portion of your workload.

#### Request

* When using this API, the Content-Type must be set to application/json-patch+json.

```bash
PATCH /ncs/v1.0/appkeys/{appKey}/workloads/{workloadId}
Content-Type: application/json-patch+json
x-nhn-authorization: {token}
```

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| workloadId | URL | String | O | Workload ID |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|
| op | Body | String | O | Operation<ul><li>Add</li><li>Remove</li><li>Replace</li><li>Copy</li><li>Move</li><li>Test</li></ul> |
| path | Body | String | O | Data path to be changed |
| value | Body | String | X | Changed value |
| from | Body | String | X | Existing data path |

<details>
   <summary>Example</summary>

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

#### Response

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| workloads | Body | Object | O | Workload information |
| workload.id | Body | UUID | O | Workload ID |
| workload.name | Body | String | O | Workload name |
| workload.type | Body | String | O | Deployment controller<ul><li>deployment</li><li>statefulset</li></ul> |
| workload.templateId | Body | String | O | Template ID of workloads |
| workload.templateVersion | Body | String | O | Template version of the workload |
| workload.createdAt | Body | String | O | Created time (UTC) |
| workload.desired | Body | Integer | O | Number of workload tasks requested |
| workload.internalLBTimeout | Body | Integer | X | Internal request response latency |
| workload.loadBalancing | Body | Object | O | Information on workload load balancer |
| workload.loadBalancing.enabled | Body | Boolean | O | Whether to use workload load balancer |
| workload.loadBalancing.floatingIp | Body | Boolean | O | Whether to use the workload load balancer floating IP |
| workload.loadBalancing.healthMonitor | Body | Object | X | About checking the health of the load balancer |
| workload.loadBalancing.healthMonitor.delay | Body | Integer | X | How often to check status |
| workload.loadBalancing.healthMonitor.timeout | Body | Integer | X | Maximum response latency |
| workload.loadBalancing.healthMonitor.maxRetries | Body | Integer | X | Maximum number of retries |
| workload.loadBalancing.healthMonitor.httpMethod | Body | String | X | HTTP Methods<ul><li>GET</li></ul> |
| workload.loadBalancing.healthMonitor.expectedCodes | Body | String | X | HTTP status codes<ul><li>200</li><li>200,202</li><li>200-204</li></ul> |
| workload.loadBalancing.healthMonitor.urlPath | Body | String | X | HTTP URL |
| workload.loadBalancing.certificate | Body | String | X | Certificates used by the load balancer when using TERMINATED_HTTPS |
| workload.loadBalancing.privateKey | Body | String | X | Private key used by the load balancer when using TERMINATED_HTTPS |
| workload.loadBalancing.tlsVersion | Body | String | X | TLS version when using TERMINATED_HTTPS<ul><li>SSLv3</li><li>TLSv1.0</li><li>TLSv1.0_2016</li><li>TLSv1.1</li><li>TLSv1.2</li><li>TLSv1.3</li></ul> |
| workload.loadBalancing.containerHref | Body | String | X | Secret container ID<li>If you separately registered a certificate and private key to use in TERMINATED_HTTPS using the Load Balancer API, use it.</li> |
| workload.loadBalancing.ipAclGroupsBinding | Body | List | X | List of IP access control groups to apply to the load balancer |
| workload.loadBalancing.ipAclGroupsBinding.ipAclGroupId | Body | String | O | IP Access Control Group ID |
| workload.schedule | Body | Object | X | About scheduled execution settings |
| workload.schedule.timeZone | Body | String | O | Schedule execution base time<ul><li>Example: Asia/Seoul, UTC</li><li>[List of tz database time zones](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)</li></ul> |
| workload.schedule.cron | Body | String | O | Scheduled Execution Cron Expressions |
| workload.schedule.jobsHistoryLimit | Body | Integer | O | Number of scheduled execution history |
| workload.schedule.concurrencyPolicy | Body | String | O | Concurrency policy<ul><li>Forbid</li><li>Replace</li></ul> |
| workload.schedule.timeOffset | Body | String | O | Offset the scheduled execution base time |
| workload.internalLoadBalancing | Body | Object | X | Internal load balancer information |
| workload.internalLoadBalancing.enalbed | Body | Boolean | O | Whether to use an internal load balancer |
| workload.internalLoadBalancing.type | Body | String | X | How to assign internal load balancer IPs<ul><li>Dynamic: Automatically assigned</li><li>static: Specify an IP</li></ul> |
| workload.internalLoadBalancing.ip | Body | String | X | Specify internal load balancer IP |
| workload.privateDns | Body | Object | X | Determinine whether to register workload working IPs with Private DNS |
| workload.privateDns.ttl | Body | Integer | O | TTL value of a recordset |
| workload.privateDns.zoneId | Body | String | O | The Private DNS Zone ID used by the workload |
| workload.privateDns.domain | Body | String | O | About domains registered with Private DNS |
| workload.activeDeadline | Body | Object | X | Information on workload scheduling end  |
| workload.activeDeadline.timeZone | Body | String | O | Scheduling end base time<li>Example: Asia/Seoul, UTC</li> |
| workload.activeDeadline.timeOffset | Body | String | O | Offset the scheduling end base time |
| workload.activeDeadline.time | Body | String | O | Scheduling end time |
| workload.autoScaler | Body | Object | X | Information on AutoScaler settings |
| workload.autoScaler.scaleOut | Body | Object | O | Information on ScaleOut |
| workload.autoScaler.scaleOut.enabled | Body | Boolean | O | Whether to use ScaleOut |
| workload.autoScaler.scaleOut.maxReplicas | Body | Integer | X | Maximum number of jobs for autoscaling |
| workload.autoScaler.scaleOut.coolDownMinute | Body | Integer | X | Wait time after scaling up |
| workload.autoScaler.scaleOut.condition | Body | List | X | Scale out condition |
| workload.autoScaler.scaleOut.condition.resource | Body | String | X | Resources based on scale out conditions<ul><li>cpu</li><li>memory</li><li>gpu</li><li>gpu-memory</li></ul> |
| workload.autoScaler.scaleOut.condition.threshold | Body | Integer | X | Scale out condition resource usage (1-100) |
| workload.autoScaler.scaleOut.condition.duration | Body | Integer | X | Scale out condition resource usage hold time (minutes) |
| workload.autoScaler.scaleIn | Body | Object | X | Information on ScaleIn |
| workload.autoScaler.scaleIn.enabled | Body | Boolean | O | Whether to use ScaleIn |
| workload.autoScaler.scaleIn.minReplicas | Body | Integer | X | Minimum number of jobs for autoscaling |
| workload.autoScaler.scaleIn.coolDownMinute | Body | Integer | X | Wait time after scale in |
| workload.autoScaler.scaleIn.condition | Body | List | X | Scale in condition |
| workload.autoScaler.scaleIn.condition.resource | Body | String | X | Resources based on scale in conditions<ul><li>cpu</li><li>memory</li><li>gpu</li><li>gpu-memory</li></ul> |
| workload.autoScaler.scaleIn.condition.threshold | Body | Integer | X | Scale in condition resource usage (1-100) |
| workload.autoScaler.scaleIn.condition.duration | Body | Integer | X | Scale in condition resource usage hold time (minutes) |
| workload.securityGroups | Body | List | X | Information on SecurityGroups |
| workload.securityGroups.id | Body | String | O | SecurityGroups ID |

<details>
  <summary>Example</summary>

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

### Stop Workload
Stops the workload.

```bash
POST /ncs/v1.0/appkeys/{appKey}/workloads/{workloadId}/pause
x-nhn-authorization: {token}
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| workloadId | URL | String | O | Template ID |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|

#### Response
This API only responds with common information.

### Restart Workload
Restarts a workload that is stopped.

```bash
POST /ncs/v1.0/appkeys/{appKey}/workloads/{workloadId}/resume
x-nhn-authorization: {token}
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| workloadId | URL | String | O | Workload ID |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|

#### Response
This API only responds with common information.

### Restart Workload
Restarts the workload job.

```bash
POST /ncs/v1.0/appkeys/{appKey}/workloads/{workloadId}/tasks/{taskId}/restart
x-nhn-authorization: {token}
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| workloadId | URL | String | O | Workload ID |
| taskId | URL | String | O | Task ID |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|

#### Response
This API only responds with common information.

### Delete Workload

Deletes a workload.

```bash
DELETE /ncs/v1.0/appkeys/{appKey}/workloads/{workloadId}
x-nhn-authorization: {token}
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| workloadId | URL | String | O | Workload ID |
| token | Header | String | O | NHN Cloud Token ({token_type} {access_token})|

#### Response

This API only responds with common information.


## Response code
| resultCode | resultMessage | Description |
| --- | --- | --- |
| 200 | SUCCESS | Successful request |
| 10001 | Authentication error. | Invalid Appkey. |
| 10002 | Bad Request. | An invalid value is requested. |
| 10003 | An error occurred while parsing the request body. | An error occurred while parsing the request body. |
| 10004 | Internal server error. | Internal server error. |
| 10005 | No permissions. | Unauthorized. |
| 10006 | You have exceeded the maximum number of {{.Resource}} that can be created. Please contact the Customer Center to increase the limit. | Exceeded the maximum number of {{.Resource}} that can be created. |
| 10041 | Could not find the template. | Could not find the template. |
| 10042 | Could not use the ECR. | Could not use the ECR. |
| 10043 | The network information does not exist. | The network information does not exist. |
| 10044 | The template in use by the workload cannot be deleted. | The template in use by the workload cannot be deleted. |
| 10045 | Duplicate container port exists in the template. | Duplicate container port exists in the template. |
| 10046 | Template with the same name already exists. | Template with the same name already exists. |
| 10047 | Resource {{.gpuFlavor}} is not available. If you want to use, please contact the Customer Center. | Resource {{.gpuFlavor}} is not available. |
| 10048 | Failed to download ConfigMaps. | ConfigMap download failed. |
| 10049 | ConfigMaps can only use the object storage from the same organization. | Configmaps can only use Object Storage from the same organization. |
| 10050 | The total size of the template's ConfigMap cannot exceed 1 MiB. | The total size of the template's configmap cannot exceed 1 MiB. |
| 10051 | Failed to download secrets from Secure Key Manager. | Secure Key Manager failed to download a secret. |
| 10052 | Could not create a template that consists only of init containers. | Could not create a template that consists only of an initialization container. |
| 10053 | The {{.Resource}} of an init container must be less than the sum of the normal containers. | The {{.Resource}} of the initialization container must be less than the sum of the normal containers. |
| 10054 | Could not set the GPU type of an init container differently than a regular container. | Could not set the GPU type of an init container differently than a regular container. |
| 10061 | Could not find the workload. | Could not find the workload. |
| 10062 | Task does not exist. | The task doesn't exist. |
| 10063 | You cannot use the load balancer because the container port is not specified in the template. | You cannot use the load balancer because the container port is not specified in the template. |
| 10064 | A workload with the same name already exists. | A workload with the same name already exists. |
| 10065 | Invalid container specifications in the template. | Invalid container specifications in the template. |
| 10066 | Could not create a workload due to insufficient resources. | Could not create a workload due to insufficient resources. |
| 10067 | You cannot change the workload name. | You cannot change the workload name. |
| 10068 | You cannot change to the template that uses a different network. | You cannot change to the template that uses a different network. |
| 10069 | In the template, you must set the same container port as the port used by the load balancer. | In the template, you must set the same container port as the port used by the load balancer. |
|	10070 | You cannot use the load balancer in legacy network environments. | Load Balancers are not available in Legacy network environments. |
|	10071 | UDP protocol cannot use load balancer. | The UDP protocol cannot use the load balancer. |
|	10072 | If the workload runs less than {{.Minutes}} minutes, you cannot use the load balancer. | If the workload execution interval is {{.Minutes}} minutes or less, the load balancer is not available. |
|	10073 | Invalid cron expression. | Invalid cron expression. |
|	10074 | Invalid time zone. | Invalid Timezone. |
|	10075 | You cannot change the load balancer to Use while the workload is stopped. | You cannot change the load balancer to Enabled while the workload is stopped. |
|	10076 | The certificate and private key are invalid. To use TERMINATED_HTTPS, you must register a valid certificate and private key. | The certificate and private key are invalid. You must enroll for a valid certificate and private key to use TERMINATED_HTTPS. |
|	10077 | Only one security group can be used. | Only one Security Groups can be used. |
|	10078 | The actions of all groups must be the same. | The groups must all have the same action. |
|	10079 | Invalid Private DNS zone. | Invalid Private DNS zone. |
|	10080 | Could not change the load balancer to Enabled while the workload is terminated. | You cannot change the load balancer to Enabled while the workload is ended.  |
|	10081 | The task is not in a restartable state. | The task is not in a restartable state. |