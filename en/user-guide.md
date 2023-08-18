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
| Container Registry | Registry of Container Image <br><ul><li>For how to use NHN Container Registry (NCR), refer to [NCR Usage Guide](/Container/NCR/en/user-guide/#check-the-user-access-key-and-secret-key).</li><li>When using Docker Hub or other Registry, you have to select Registry Type.</li></ul> |
| Registry Type | You can select from type of Registry, Public or Private. |
| Image URL | Information about Container image, can be entered maximum 255 characters of alphabetic characters and numbers, only (`-`, `_`, `.`, `,` `/`, `@`, and `:`. |
| Registry ID | ID used for Private Registry authentication |
| Registry Password | Password used for Private Registry authentication |
| Memory | Memory to assigned to Container |
| Port | Ports used in Containers |
| CPU | Enter the number of CPUs that you want to assign to Container, the number can be entered in units of 0.25 and between 0.25 and 16. |
| Whether to use GPU | Decides whether the container uses a GPU. |
| GPU Type | Decides the type of GPU to assign to a container. |
| Order | Commands to be executed when Container is started, override ENTRYPPOINT specified in the image. |
| Task Directory | Task Directory of Container takes precedence over the WORKDIR specified in the image. |
| Environment Variables | Environment variables to set in Container |
| Storage | Storage connected to Container |
| Storage Name | Storage Name, which can be entered lowercase letters, numbers, and '-' maximum 63 characters. |
| NAS Storage Connection Path | Connection information to be connected to Container, Storage is to be mounted on Container.  `/mnt/$Storage_name`.<br><ul><li>Use of NAS Storage, from page **Storage> NAS**, enter Connection Information for NAS storage to connect to.<ul><li>For instructions on How to use NAS storage, refer to [NAS Usage Guide](/Storage/NAS/en/console-guide/).</li></ul></li><li>Use separately established NFS server, enter Mount point for NFS server.<ul><li>You can use NFS v3 version only. </li></ul></li></ul> |
| Subnet | Subnets defined in VPC that connect to Instances |

Enter the required information and click **Create Template** button to create Template.

> [Caution]
> When a container (task) is restarted due to an NCS environment check or a temporary error, the created logs and data inside the container are initialized. For data that must be maintained after restart, use NAS storage.

> [Note]
> You can add only one port of the same protocol to Template.
> TCP and HTTP cannot use the same port.
> Using the HTTP protocol adds an X-Forwarded-For Header that allows the load balancer to identify the Client IP.

> [Note]
> Only NAS storage that uses the same VPC as the template can be used.
> You can connect maximum three Storages.

> [Note]
>  The ephemeral storage of the container is limited to 20 GB. If more than 20GB is used, the container will be restarted and logs and data created in the ephemeral storage will be initialized.

### Retrieve Template

You can retrieve Templates you created on **Template** tab of page **Container > NHN Container Service (NCS)**. List of Templates displays the total of Container resources.

#### Basic Information

You can check specific Template to view details from **Basic Information** tab.

| Items | Descriptions |
| --- | --- |
| Name | Template name and ID  |
| Descriptions | Template Description |
| Container | Number of Containers defined in Template |
| CPU | Number of CPUs in Containers defined in the template added |
| Date of Creation | Creation date of Template  |
| VPC | VPC set to Template |
| Subnet | Subnet set to Template |

#### Container

After clicking specific Template, you can go to **Container** tab to check the list of Containers have added to Template. You can select a specific container from the container list to check its details.

| Items | Descriptions |
| --- | --- |
| Containers Name | Container Name |
| Image URL | Container Image Information  |
| Memory | Memory assigned to Container |
| CPU | Number of CPU assigned to Container |
| GPU | Information on GPU assigned to Container |
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
| Base time | Specify a time zone for the scheduled task start time. |
| Cron expression | You can enter the scheduled repeat cycle as a cron expression. |
| Number of scheduled execution history | Enter the maximum number of scheduled tasks to keep that have ended.<br>You can check logs, events, and container information of archived tasks. |
| Concurrency policy | Specify a concurrency policy for tasks generated by task repeat cycles.<br>Forbid: Do not run a new task unless the existing task completes.<br>Replace: The existing task is replaced with a new task. |
| Whether to use Load Balancer or not | The Use button is active only when the port is specified in Container information in Template. |
| Whether to use Floating IP or not | To use floating IP, Internet Gateway have to be connected to the configured Subnet.<br>Floating IP have to be used to access containers from outside.<br>With floating IP, the domain URL is added. |

Enter the required information and click **Create Workload** button to create Template.

> [Note]
> Load Balancers are not available in Legacy network environments.
> When setting up a schedule, the number of task requests is fixed at 1.
> The load balancer cannot be activated if the workload execution cycle is less than 10 minutes.
> When an existing task is replaced by a new task due to the concurrency policy (Replace), the history of the existing task is not left behind.

> [Note]
> The meaning of each field in cron expression (\* \* \* \* \*) for the scheduled execution is as follows.
>
> | Field name | Acceptable range of values | Allowed special characters |
> | --- | --- | --- |
> | Minute | 0-59 | `*` `/` `,` `-` |
> | Hour | 0-59 | `*` `/` `,` `-` |
> | Day | 1-31 | `*` `/` `,` `?` |
> | Month | 1-12<br>JAN-DEC | `*` `/` `,` `-` |
> | Day of the week | 0-6<br>SUN-SAT | `*` `/` `,` `?` |

### Retrieve Workload

You can check Workloads you create on **Workloads** tab on **Container > NHN Container Service (NCS)**.

#### Basic Information

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

#### Running Container

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
| GPU | Information on GPU assigned to Container |
| Port | Ports used in Containers |
| Command | Command to run when container starts |
| Task Directory | Task Directory of Container |
| Environment Variables | Environment variables set in Container |
| Storage | Storage connected to Container |
| Date of Restart | Date Container restarted |

#### Monitoring

After clicking a specific workload, you can find the resource usage of containers on the **Monitoring** tab. Container metrics are collected every 15 seconds and kept for up to 1 year.
Items provided with monitoring are as follows.

| Item | Unit | Description |
| --- | --- | --- |
| CPU Usage | % | Usage is provided based on CPU assigned to containers. |
| Memory Usage| % | Usage is provided based on Memory assigned to containers. |
| Network Data Trasmission | bps | Network data trasmission information is provided based on workload operation|
| Network Data Reception | bps | Network data reception information is provided based on workload operation |
| Disk Usage | % | Usage of NAS storage added to the container is provided. |

#### Event

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

#### Log

After clicking a specific workload, you can view logs in Container from **Log** tab. If you do not specify time, the log is retrieved 5 minutes before the current time.

> [Note]
> Logs are limited to a maximum size of 5 GB and kept for 2 months.

#### Workload Execution History

You can check the progress and history of changes to the workload template in the **Workload Execution History** tab by selecting the workload for which you want to view the workload execution history.

| Items | Descriptions |
| --- | --- |
| Template name | Template name that workload is using |
| Execution time | Deployment start time for workloads using the template |
| End time | End time for workload using the template |
| Status | Deployment status<br>succeeded: Deployment completed<br>pending: Deploying<br>terminated: Ended workload |

You can view the **Details of workload execution history** by clicking the retrieved history information.

> [Note]
The history of the running workload does not show an end time and the status remains succeeded.
> [Caution]
You cannot check the workload execution history after deleting the workload.

#### Scheduled Execution History
You can check the list of executed tasks in the **Scheduled Execution History** tab by selecting the workload for which you want to view the scheduled execution history.

| Items | Descriptions |
| --- | --- |
| Task ID | Scheduled task ID that was executed |
| Execution time | Execution time for scheduled task |
| End time | End time for scheduled task |
| Status | Scheduled task status<br>waiting: Preparing schedule<br>running: Scheduled task running<br>completed: Scheduled task normally ended<br>error: Scheduled task abnormally ended |

### Change Workload

You can change the running workload by selecting a workload to change and clicking **Change** at the **Basic Information** tab.

| Items | Descriptions |
| --- | --- |
| Descriptions | Workload Description |
| Template | Change the template of a running workload<br>When changing the template, workload is deployed with zero downtime through the rolling update method.<br>Tasks are replaced one by one, so that during deployment, existing and new tasks can be executed at the same time.<br>You can see the results at the `Workload Execution History` tab. |
| Number of tasks requested. | Change the number of tasks in a running workload<br>Increase tasks requested: Existing tasks are maintained and new tasks are created.<br>Decrease tasks requested: Tasks are ended by the reduced number of tasks. |
| Scheduled execution | Change scheduled execution information<br>The number of scheduled execution history is set from the time of change, and the number of history and setting values may not match until all tasks executed before the change are deleted. |
| Load Balancer | Change whether a workload uses a load balancer or not |

> [Caution]
If you use a load balancer to make changes to the template while the workload is in service, downtime may occur.
> [Note]
If a workload is in the Pending state, you cannot make changes to the load balancer.

### Stop/Restart Workload
If you stop a workload, all tasks in the workload will end.

If you restart a workload, the container IP and load balancer IP are changed while the floating IP and URL are maintained.

> [Caution]
> Logs, events, and ephemeral-storage are initialized when the workload is stopped.
> Even if you stop the workload, the scheduled execution history is deleted during the task repeat cycle.

### Delete Workload

Select a workload you want to delete and click **Delete Workload** button to proceed with the deletion.

## NCS Role
You can set NCS roles to control which roles can access services and resources.
For example, you can set an "NCS administrator" to have the ability to create, view, and manage templates and workloads, while an "NCS user" can only have the view role for templates and workloads.

### Add NCS Execution Role
Set the role to execute NCS in the NHN Cloud Console screen.
1. Select **Manage Member** in the **Project Management** screen.
2. Click a member that you want to change the role.
3. Click **Add Role** to add roles for each service.
  * Select the basic infrastructure service in the left area, then select the role in the right area.
4. You can view the selected roles to add or delete them.
5. Click **Add** to apply the changed roles to the project members.
6. Once a role is added, you can select a member to view the detailed role history.

For more information on how to use role group, see the console guide.

### Details of Role
To use NCS, you need roles for the following resources.
* NCS - Allows you to view, create, and manage resources in the NCS.
* Infrastructure - Allows NCS users to lookup VPC, Subnet resources. This is required when looking up templates and workloads in NCS.
* Load Balancer - Allows NCS users to create and manage Infrastructure Load Balancer resources. This is required when using a Load Balancer for a workload in NCS.
* Security Group - Allows NCS users to create and manage Infrastructure Security Group resources. This is required when using Security Groups for templates in NCS.

### Assign Minimum Roles for NCS
In a production environment, it is recommended to add only the roles you need. The minimum roles to use the NCS service are as follows.

| Features | Infrastructrue NCS ADMIN | Infrastructrue MEMBER | Infrastructrue Security Group ADMIN | Infrastructrue Load Balancer ADMIN |
| --- | --- | --- | --- | --- |
| Retrieve Template |  | O |  |  |
| Create Template | O |  | O |  |
| Delete Template | O |  | O |  |
| Retrieve Workload |  | O |  |  |
| Create and Change Workload | O |  | O | O |
| Delete Workload | O |  | O | O |

## Other considerations

### Region

* NCS service is only available in KR1 region.

### Resource Provision Policy
* Refer to [ NHN Container Service Resource Provision Policy](/nhncloud/en/resource-policy/#nhn-container-servicencs).

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
* GPU CUDA version is 11.7.
* GPU Driver version is 515.86.01.
* Container can only use one type of GPU.
* Available GPU types are as follows.

| Category | Type | Name | RAM | Number of GPUs | MIG Profile Name |
| --- | --- | --- | --- | --- | --- |
| Graphics Optimized | ncs1 | ncs1.g1m5 | 5GB | 1 | MIG 1g.5gb |
| Graphics Optimized | ncs1 | ncs1.g2m10 | 10GB | 2 | MIG 2g.10gb |

## Guide to Problem Solving

Explain how to solve various problems that may occur while using the NCS service

### Workload

* A container event with **FailedCreatePodSandBox**/**CNITimedOutWaitingForVIFs** type occurs when the workload is created normally, and the container is running properly
    * This event occurs when Network Interface for use in NCS is not created. This is a temporary event that occurs when a workload is created immediately after template creation or when a workload is created in bulk. The event does not occur after a certain amount of time has elapsed.
* A container event with **IpAddressGenerationFailure** type occurs when the workload is pending
    * This event occurs when there are no more IP addresses available in the specified subnet. You must create Template by changing the CIDR of the subnet or using other subnet.
* Workloads remain pending after changing the workload template
    * You can check the **Event** of the added task to find why the container is not running.
* An event where creating workloads fails due to insufficient resources

| Error Message | Description |
| --- | --- |
| {{.Resource}} Could not create a workload due to insufficient resources. | Could not create a workload due to insufficient resources for NCS environment.<br>Try again after a while or contact the Customer Center. |
| Exceeded the number of {{.Resource}} that can be created. Please contact the Customer Center to increase the limit. | Exceeded the NCS quota for the project.<br>For more details, refer to [NHN Container Service(NCS) Resource Provision Policy](/nhncloud/en/resource-policy/#nhn-container-servicencs). |
