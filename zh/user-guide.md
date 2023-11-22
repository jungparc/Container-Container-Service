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
| Container Registry | Registry of Container Image <br><ul><li>For how to use NHN Container Registry (NCR), refer to [NCR Usage Guide](/Container/NCR/zh/user-guide/#check-the-user-access-key-and-secret-key).</li><li>When using Docker Hub or other Registry, you have to select Registry Type.</li></ul> |
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
| Life Cycle Hook | You can set commands to run when the container is created and terminated.<br>If the command entered immediately after creation fails, the container is restarted.<br>If the container exits before the command runs, the command might not run.<ul><li>The GracePeriodSeconds for the workload job is 30 seconds.</li></ul>Immediately after creation (postStart) Example) bash,-c,curl $URL/postStart <br>Just before exit (preStop) Example) bash,-c,curl $URL/preStop |
| File | Files uploaded to Object Storage are available by mounting them in the container directory. <ul><li>Appkey: Enter the appkey for the Object Storage service in your project that will use the file data.</li><li>User Access Key: Enter the user access key of the user accessing the Object Storage service. You can create and check the User Access Key on the Account > **API Security Settings** page of NHN Cloud Console.</li><li>User Secret Key: Enter the secret access key of the user accessing the Object Storage service. You can create and check the Secret Access Key on the Account > **API Security Settings** page of NHN Cloud Console.</li><li>Object URL: Enter the URL to download the object. </li><li>Container mount path: Enter the mount path for the container.<ul><li>The file will be mounted at the path you entered.</li></ul></li></ul> |
| Confidential Data | You can use the confidential data files you store in Secure Key Manager by mounting them in a container directory. <ul><li>Confidential data ID: Enter the confidential data ID for the Secret Key Manager service.</li><li>Container mount path: Enter the mount path for the container.<ul><li>The file will be mounted at the path you entered.</li></ul></li></ul> |
| NAS Storage | Enter the NAS storage you want to connect to the container.<ul><li>Name: Storage Name, which can be entered lowercase letters, numbers, and '-' maximum 63 characters.</li><li>NAS connection path: Enter the connection information of the NAS storage. <ul><li>If you're using NAS storage, go to **Storage** > **NAS** page, enter the connection information for the NAS storage you want to connect to. For instructions, see the [NAS User Guide](/Storage/NAS/zh/console-guide/).</li><li>If you're using a separately deployed NFSv3 server, enter the mount point of the NFS server.</li></ul></li><li>Container connection path: Enter the mount path for the container.<ul><li>Mounted to `$container connection path`/`$storage name`.</li></ul></li></ul>Only NAS storage that uses the same VPC as the template can be used. |
| Health Check | You can configure commands to check the health of a container.<ul><li>Activated or not (LivenessProbe): Determines whether the container is working.</li><li>Started or not (startupProbe): Determines whether the application inside the container has started.</li></ul>If the health check fails, the container is restarted. |
| Subnet | Subnets defined in VPC that connect to Instances |
| DNS | Set the DNS servers used by the workload.<br>If not set, use 8.8.8.8. |

Enter the required information and click **Create Template** button to create Template.

> [Caution]
> When a container (task) is restarted due to an NCS environment check or a temporary error, the created logs and data inside the container are initialized. For data that must be maintained after restart, use NAS storage.

> [Note]
> You can add only one port of the same protocol to Template.
> TCP and HTTP, HTTPS, and TERMINATED_HTTPS cannot use the same port.
> Using the HTTP and TERMINATED_HTTPS protocols adds an X-Forwarded-For Header that allows the load balancer to identify the Client IP.

> [Note]
> Each container is provided with 20 GB of ephemeral storage. If you exceed the provided usage, the container is restarted and the ephemeral storage is reset.

> [Note]
> The file and confidential data use the information from when the template was created. If the original file or secret data is modified, the information in the already created template is not affected.
> Files are only accessible to the project Object Storage within the same organization. 
> Confidential data utilizes Secure Key Manager in the same project. To use confidential data, you must first enable the Secure Key Manager service.

### Retrieve Template

You can retrieve Templates you created on **Template** tab of page **Container > NHN Container Service (NCS)**. List of Templates displays the total of Container resources.

#### Basic Information

You can check specific Template to view details from **Basic Information** tab.

| Items | Descriptions |
| --- | --- |
| Name | Template name and ID |
| Descriptions | Template Description |
| Container | Number of Containers defined in Template |
| CPU | Number of CPUs in Containers defined in the template added |
| Date of Creation | Creation date of Template |
| VPC | VPC set to Template |
| Subnet | Subnet set to Template |

#### Container

After clicking specific Template, you can go to **Container** tab to check the list of Containers have added to Template. You can select a specific container from the container list to check its details.

| Items | Descriptions |
| --- | --- |
| Containers Name | Container Name |
| Image URL | Container Image Information |
| Memory | Memory assigned to Container |
| CPU | Number of CPU assigned to Container |
| GPU | Information on GPU assigned to Container |
| Port | Ports used in Containers |
| Command | Command to run when container starts |
| Task Directory | Task Directory of Container |
| Environment Variables | Environment variables set in Container |
| Storage | Storage connected to Container |
| Life Cycle Hook | Lifecycle hooks set on a container |
| File | Object files and mount paths associated with the container |
| Confidential Data | Confidential data associated with a container and its mount path |
| Health Check | Check on the health of a container |

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
| Number of tasks requested. | Number of templates to run, you can enter values between 1 and 100. |
| Scheduled execution | You can set up time-based schedules to schedule workload execution.<ul><li>Base time: Specify the timezone for the scheduled task time.</li><li>Cron expression: You can enter the scheduled repeat cycle as a cron expression.</li><li>Number of scheduled execution histories: Enter the maximum number of archived ended scheduled tasks. Archived tasks can check logs, events, and container information.</li><li>Concurrency policy: Specify a concurrency policy for tasks generated by task repeat cycles.<ul><li>Forbid: Do not run a new task unless the existing task completes.</li><li>Replace: The existing task is replaced with a new task. When replaced, no history of the existing task is left behind.</li></ul></li></ul>When you schedule workloads, the number of task requests is fixed at 1.<br>If the workload execution interval is 10 minutes or less, you cannot enable the load balancer. |
| Load Balancer | The Enable button is enabled only if a port is specified in the template's container information.<ul><li>Floating IP: To use floating IP, Internet Gateway have to be connected to the configured Subnet.<ul><li>To access your container from the outside, you need to use a floating IP. Using a floating IP adds a domain URL.</li></ul></li><li>Health check: The load balancer attempts to check the health of the workload.</li><li>IP access control groups: You can apply access control groups to the load balancer.</li><li>If your container uses the TERMINATED_HTTPS protocol, you must register an SSL certificate.</li><li>The load balancer's ports and protocols use the ports and protocols defined in the template's container.</li></ul>Load Balancers are not available in Legacy network environments. |
| Security Group | You can specify a security group for your workloads.<br>If you selected a workload security group, you must create security rules for the container ports.<br>If you do not select a workload security group, the security group created by NCS is applied and the security rules for the container ports are automatically created. |

Enter the required information and click **Create Workload** button to create Workload.

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
| Number of tasks requested. | Number of Templates to run |
| Number of tasks executed | Number of Templates executed |
| Created date | Date of Workload creation |
| VPC | VPC set to Workload |
| Subnet | Subnet set to Workload |
| Security Group | Name of Security Group set to Workload |
| Load Balancer | Change whether a workload uses a load balancer, floating IP, health check, SSL certificate or not |

> [Note]
> Workload status is determined by considering condition of all Containers and Load Balancers included. You can see the status of individual Containers on **Running Container** tab.

#### Running Container

You can view Container details by clicking a specific workload and then clicking Container on **Running Container** tab.

| Items | Descriptions |
| --- | --- |
| Containers Name | Container Name |
| Image URL | Container Image Information |
| IP | IP assigned to Container |
| Status | Status of Container |
| The number of Restart | The number of time that Container restarted. |
| Memory | Memory assigned to Container |
| CPU | Number of CPU assigned to Container |
| GPU | Information on GPU assigned to Container |
| Port | Ports used in Containers |
| Command | Command to run when container starts |
| Task Directory | Task Directory of Container |
| Environment Variables | Environment variables set in Container |
| Storage | Storage connected to Container |
| Life Cycle Hook | Lifecycle hooks set on a container |
| File | Object files and mount paths associated with the container |
| Confidential Data | Confidential data associated with a container and its mount path |
| Health Check | Check on the health of a container |
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
| Ephemeral Storage Usage | % | Temporary storage utilization is provided on a per-job basis for workloads. |

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
> Detailed reasons for the current and last state of the container can also be found in the events.

#### Log

After clicking a specific workload, you can view logs in Container from **Log** tab. If you do not specify time, the log is retrieved 5 minutes before the current time.

> [Note]
> Logs are kept for 2 months.

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
> If you use a load balancer to make changes to the template while the workload is in service, downtime may occur.
> [Note]
> If a workload is in the Pending state, you cannot make changes to the load balancer.
> If the workload change fails (for example, an image error), the change attempt is terminated and no job replacement occurs.

### Stop/Restart Workload
If you stop a workload, all tasks in the workload will end.

If you restart a workload, the container IP and load balancer IP are changed while the floating IP and URL are maintained.

> [Caution]
> Logs, events, and ephemeral storage are initialized when the workload is stopped.
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

* NCS service is only available in Korea (Pangyo) and Korea (Gwangju) regions.

### Resource Provision Policy
* Refer to [ NHN Container Service Resource Provision Policy](/nhncloud/zh/resource-policy/#nhn-container-servicencs).

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
* The file and confidential data use the information from when the template was created. If the original file or confidential data is modified, the information in the already created template is not affected.
    * To update the contents of a file or confidential data and reflect those changes, you need to create a new template and run the workload.

### GPU

* Provides MIG(Multi Instance GPU of A100 40GB Card.
* For details, refer to the following links.
    * [https://www.nvidia.cn/technologies/multi-instance-gpu/](https://www.nvidia.cn/technologies/multi-instance-gpu/)
    * [https://www.nvidia.cn/data-center/a100/](https://www.nvidia.cn/data-center/a100/)
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
| Exceeded the number of {{.Resource}} that can be created. Please contact the Customer Center to increase the limit. | Exceeded the NCS quota for the project.<br>For more details, refer to [NHN Container Service(NCS) Resource Provision Policy](/nhncloud/zh/resource-policy/#nhn-container-servicencs). |
