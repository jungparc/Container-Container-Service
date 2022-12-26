## Container > NHN Container Service(NCS) > User Guide

## Template

Template is a service that defines resources, such as Containers, Networks, etc., which needed to run Workload.

### Create Template

You have to create Template before you can use NHN Container Service (NCS). Go to **Container > NHN Container Service (NCS)** page, click **Template** tab, and then click **Create Template** button. The following items are required to create Template.

| Items | Descriptions |
| --- | --- |
| Template Name | Template Name, you can only enter the name of Template, lowercase letters within maximum 32 characters, numbers, and '-' . |
| Descriptions | Template Description, you c |
| Containers Name | Containers Name. You can enter lowercase letters, numbers, and '-' within maximum 253 characters. |
| Container Registry | Registry of Container Image <br><ul><li>For how to use NHN Container Registry (NCR), refer to [NCR Usage Guide](https://nhnent.dooray.com/Container/NCR/ko/user-guide/#user-access-keysecret-key).</li><li>When using Docker Hub or other Registry, you have to select Registry Type.</li></ul> |
| Registry Type | You can select from type of Registry, Public or Private. |
| Image URL | Information about Container image, can be entered maximum 255 characters of alphabetic characters and numbers, only (`-`, `_`, `.`, `,` `/`, `@`, and `:`. |
| Registry ID | ID used for Private Registry authentication |
| Registry Password | Password used for Private Registry authentication |
| Memory | Memory to assigned to Container |
| Port | Ports used in Containers |
| CPU | Enter the number of CPUs that you want to assign to Container, the number can be entered in units of 0.25 and between 0.25 and 16. |
| GPU | Enter the number of GPUs you want to assign to Container, the number can be entered in units of 1, and the number between 0 and 2. |
| Order | Commands to be executed when Container is started, override ENTRYPPOINT specified in the image. |
| Task Directory | Task Directory of Container takes precedence over the WORKDIR specified in the image. |
| Environment Variables | Environment variables to set in Container |
| Storage | Storage connected to Container |
| Storage Name | Storage Name, which can be entered lowercase letters, numbers, and '-' maximum 63 characters. |
| NAS Storage Connection Path | Connection information to be connected to Container, Storage is to be mounted on Container.  `/mnt/$Storage_name`.<br><ul><li>Use of NAS Storage, from page **Storage> NAS**, enter Connection Information for NAS storage to connect to.<ul><li>For instructions on How to use NAS storage, refer to [NAS Usage Guide](https://nhnent.dooray.com/Storage/NAS/ko/console-guide/).</li></ul></li><li>Use separately established NFS server, enter Mount point for NFS server.<ul><li>You can use NFS v3 version only. </li></ul></li></ul> |
| Subnet | Subnets defined in VPC that connect to Instances |

Enter the required information and click **Create Template** button to create Template.

> [Note]
> When creating Template to run workloads quickly, it creates Network Interface in subnet band in advance.

> [Note]
> You can add only one port of the same protocol to Template.

> [Note]
> Only NAS storage that uses the same VPC as the template can be used.
> You can connect maximum three Storages.

### Retrieve Template

You can retrieve Templates you created on **Template** tab of page **Container > NHN Container Service (NCS)**. List of Templates displays the total of Container resources.

### Basic Information

You can check specific Template to view details from **Basic Information** tab.

| Items | Descriptions |
| --- | --- |
| Name | Template name and ID  |
| Descriptions | Template Description |
| Container | Number of Containers defined in Template |
| CPU | Number of CPUs in Containers defined in the template added |
| GPU | Number of GPUs in Containers defined in the template added |
| Date of Creation | Creation date of Template  |
| VPC | VPC set to Template |
| Subnet | Subnet set to Template |

### Container

After clicking specific Template, you can go to **Container** tab to check the list of Containers have added to Template. You can select a specific container from the container list to check its details.

| Items | Descriptions |
| --- | --- |
| Containers Name | Container Name |
| Image URL | Container Image Information  |
| Memory | Memory assigned to Container |
| CPU | Number of CPU assigned to Container |
| GPU | Number of GPU assigned to Container |
| Port | Ports used in Containers |
| Command | Command to run when container starts |
| Task Directory | Task Directory of Container |
| Environment Variables | Environment variables set in Container |
| Storage | Storage connected to Container |

### Delete Template

Select the template you want to delete and click **Delete Template** button to delete it.

> [Note]
> You cannot delete Template if there is a workload using the template.

## Workload

Service that executes containers using the Template that you defined.

### Create Workload

Go to **Container > NHN Container Service (NCS) ** page, click **Workload** tab, and then click **Create Workload** button. The following items are required to create workloads.

| Items | Descriptions |
| --- | --- |
| Template | Template Name<br><ul><li>Click **Select Template** button to select from Templates created.</li><li>Click **Create Template** button to create and select new Template.</li></ul> |
| Name | Workload Name, you can only enter lowercase letters, numbers, and '-' withi maximum 32 characters. |
| Descriptions | Description of Workload. You can enter maximum 255 characters. |
| Number of tasks requested.  | Number of templates to run, you can enter values between 1 and 100. |
| Whether to use Load Balancer or not | The Use button is active only when the port is specified in Container information in Template. |
| Whether to use Floating IP or not | To use floating IP, Internet Gateway have to be connected to the configured Subnet.<br>Floating IP have to be used to access containers from outside.<br>With floating IP, the domain URL is added. |

Enter the required information and click **Create Workload** button to create Template.

> [Note]
> Load Balancers are not available in Legacy network environments.

### Retrieve Workload

You can check Workloads you create on **Workloads** tab on **Container > NHN Container Service (NCS)**.

### Basic Information

You can click on specific Workload to view details from the **Basic Information** tab.

| Items | Descriptions |
| --- | --- |
| Name | Workload name and ID |
| Descriptions | Workload description |
| Template | Name of template used |
| Number of tasks requested.  | Number of Templates to run |
| Number of tasks executed | Number of Templates executed |
| Created date | Date of Workload creation |
| VPC | VPC set to Workload |
| Subnet | Subnet set to Workload |
| Security Group | Name of Security Group set to Workload |
| Load Balancer | Whether to use Load Balancer or not |

> [Note]
> Workload status is determined by considering condition of all Containers and Load Balancers included. You can see the status of individual Containers on **Running Container** tab. 

> [Note]
> Load Balancer may not be active for one to two minutes immediately after Subnet creation.

#### **Running Container**

You can view Container details by clicking a specific workload and then clicking Container on **Running Container** tab.

| Items | Descriptions |
| --- | --- |
| Containers Name | Container Name |
| Image URL | Container Image Information  |
| IP | IP assigned to Container |
| Status | Status of Container |
| The number of Restart | The number of time that Container restarted.  |
| Memory | Memory assigned to Container |
| CPU | Number of CPU assigned to Container |
| GPU | Number of GPU assigned to Container |
| Port | Ports used in Containers |
| Command | Command to run when container starts |
| Task Directory | Task Directory of Container |
| Environment Variables | Environment variables set in Container |
| Storage | Storage connected to Container |
| Date of Restart | Date Container restarted |

#### **Event**

After clicking on specific Workload, you can view Event information from Container on **Event** tab. You can view events by status of Events by clicking Select Event Status.

| Items | Descriptions |
| --- | --- |
| Status | Status of Event |
| Type | Type of Event |
| Descriptions | Event Description |
| Date of first occurrence of event | Date of first occurrence of event |
| Date of last occurrence of event | Date of last occurrence of event |
| Number of occurrences | Number of times event occurred |

> [Note]
> Events are only kept for maximum 1 hour, so information from 1 hour earlier cannot be checked.

#### **Log**

After clicking a specific workload, you can view logs in Container from **Log** tab. If you do not specify time, the log is retrieved 5 minutes before the current time.

> [Note]
> Logs are kept for maximum two months.

### Delete Workload

Select a workload you want to delete and click **Delete Workload** button to proceed with the deletion.

## Other considerations

### Region

* NCS service is only available in KR1 region.

### Limitation of Numbers

* Template can describe maximum 10 number of containers.
* Workload can describe maximum 10 number of executions.

### Template/Container

* Containers described in Template must be configured to use different container ports.
* If you connect directly to Container Port, make sure that Security Group is well set up.
* You cannot create Load Balancer if you use Template that does not specify Container Port.
* You can create containers by utilizing Container images that exist in the following Private Registry.
* Containers can be created by using container images that exist in private registries. The supported source registry types, URLs, Access IDs, and Access Secrets are as follows.

| Source Registry Type | Access ID | Access Secret |
| --- | --- | --- |
| Azure Container Registry | Access Key User Name | Access Key Password |
| Google Cloud Container Registry | \_json\_key | Private key of service account (JSON type) |
| Docker Hub | Username | Password |
| Harbor | Username | Password |
| Quay | json\_file | {<br>"account\_name": "$User account",<br>"docker\_cli\_password": "Encrypted Password generated by $Quay"<br>} |

* Within Container image, you must match Container Port in the Template with the Port that the Container decides to use for service.
    * If you use default nginx Container image that is designated to serve 80 ports, you have to specify 80 for the Container Port. If you change the content of Container image and set it up to serve another Port, you have to specify that Port number.

### GPU

* Provides MIG(Multi Instance GPU of A100 40GB Card.
* For details, refer to the following links.
    * [https://www.nvidia.com/ko-kr/technologies/multi-instance-gpu/](https://www.nvidia.com/ko-kr/technologies/multi-instance-gpu/)
    * [https://www.nvidia.com/ko-kr/data-center/a100/](https://www.nvidia.com/ko-kr/data-center/a100/)

## Guide to Problem Solving

Explain how to solve various problems that may occur while using the NCS service

### Workload

* A container event with **FailedCreatePodSandBox**/**CNITimedOutWaitingForVIFs** type occurs when the workload is created normally, and the container is running properly
    * This event occurs when Network Interface for use in NCS is not created. This is a temporary event that occurs when a workload is created immediately after template creation or when a workload is created in bulk. The event does not occur after a certain amount of time has elapsed.
* A container event with **IpAddressGenerationFailure** type occurs when the workload is pending
    * This event occurs when there are no more IP addresses available in the specified subnet. You must create Template by changing the CIDR of the subnet or using other subnet.