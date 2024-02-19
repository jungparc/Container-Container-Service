## Container > NHN Container Service(NCS) > Release Notes
### February 27, 2024
#### Added Features
* Added the feature to set container arguments (Args).
* Added the feature to select a workload deployment controller.
* Added the internal load balancer feature.
* Added the feature to integrate with Private DNS for workloads.

#### Feature Updates
* Temporary shared storage is provided between containers.

### November 28, 2023
#### Added Features
* Added container settings features.
    * DNS server address settings
    * Health check settings (LivenessProbe, StartupProbe)
    * Lifecycle hook settings (Lifecycle Hook)
    * File settings (ConfigMap)
    * Confidential data settings (Secret)
    * NAS container connection path settings
* Added the feature to monitor GPUs and ephemeral storage.
* You can select a security group when creating a workload.
* Events occurred in a user cluster can be checked in NCS.

#### Feature Updates
* Load Balancer is provided.
    * Load Balancer Instance is no longer supported.
* Added HTTPS, TERMINATED_HTTPS protocols to container ports.
* Improved the Log tab.
* Improved the Events tab to show detailed reasons for the current and last status of containers.

### August 29, 2023
#### Added Features
* Added a feature to schedule workloads.
* Added a feature to stop/restart workloads.

#### Feature Updates
* Improved to identify the cause of NAS storage connection failures on the Event tab.

### July 25, 2023
#### Feature Updates
* Raised the maximum resource size for containers.

### May 30, 2023
#### Feature Updates
* Added a feature to select the GPU type.
* Added HTTP protocols for container ports.
* Added the Quota feature.

### March 28, 2023

#### Feature Updates
* Enhanced service reliability by improving the internal structure.

#### February 28, 2023

### Added Features
* Added a feature to subdivide permissions
* Added a feature to change workloads

### January 31, 2023

#### Added Features
* Added monitoring feature.

### December 27, 2022

#### Release of a New Service
* You can create and manage containers in the console.
