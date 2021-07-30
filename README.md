# ckad
CKAD preparation material

**Overview**
1. Cluster is collectoin of nodes.
2. Nodes - Worker nodes and Master node
3. Worker nodes include the software to run container managed by k8s control plane
4. Master node runs the control plane
5. The control plane is set of APIs and software thats k8s users interact with.
6. APIs and software are referred to as master components. 

![Diagram of Kubernetes cluster with all components tied together!](https://d33wubrfki0l68.cloudfront.net/2475489eaf20163ec0f54ddc1d92aa8d4c87c96b/e7c81/images/docs/components-of-kubernetes.svg)

### Kubernetes Components
#### Control Plane:  
The container orchestration layer that exposes the API and interfaces to define, deploy, and manage the lifecycle of containers. The control plane manages the worker nodes and the Pods in the cluster. In production environments, the control plane usually runs across multiple computers and a cluster usually runs multiple nodes, providing fault-tolerance and high availability.

#### Control Plane Components
1. etcd: The primary data store of all cluster state  
1. kube-apiserver: The REST API server for managing the Kubernetes cluster
1. kube-controller-manager: Manager of all of the controllers in the cluster that monitor and change the cluster state when necessary  
1. cloud-controller-manager: A Kubernetes control plane component that embeds cloud-specific control logic. The cloud controller manager lets you link your cluster into your cloud provider's API, and separates out the components that interact with that cloud platform from components that only interact with your cluster.
1. kube-scheduler: Control plane process which assigns Pods to Nodes 

#### Node Components
1. kubelet: An agent that runs on each node in the cluster. It makes sure that containers are running in a Pod.
1. kube-proxy: kube-proxy is a network proxy that runs on each node in your cluster, implementing part of the Kubernetes Service concept. kube-proxy maintains network rules on nodes. These network rules allow network communication to your Pods from network sessions inside or outside of your cluster.
 

### Pods
1. Pods are basic building block.
2. One or more containers in a Pod.
3. Pod container all share a container network.
4. One IP address per pod.  
Command to create Pod  
`kubectl create -f <Pod Definition File>`
Pod Definition file

### Service
1. Service defines networking rules for accessing pods in the cluster and from internet.
1. Use labels to select a group of pods.
1. Service has a fixed IP address
1. It distributes requests across pods in group.
1. Service expose pods via static IP address.
1. NodePort service allows access from outside the cluster.

### Multi Container Pods

#### Namespaces
1. Sepearates resources according to users, environment or application.
1. Roles based access control(RBAC) to secure access per namespace.
1. Using namespace is a best practice.
1. `kubectl create -f <POD FILE> -n <namespace>` 
1. Container can communicate over localhost within a pod.
1. `kubectl logs` to view logs
1. Logs record what is written to standard output and standard error.

### Service Discovery
Service Discovery Mechanisms  
| Environment Variables | DNS |  
| ---- | --- |  
| Services address are automatically injected in containers | DNS records automatically created in clusters' DNS. |
| Environment variables follow naming convention based on service name | Containers automatically configured to query clusters' DNS. | 

### Deployments  
1. Represents multiple replica for a pod.
1. Describes a desired state that k8s needs to achieve.
1. Deployment Controller master component converges actual state to the desired state.

### Auto Scaling
1. Scale automatically based on CPU utilization or custom metrics.
1. Set target CPU along with minimum and maximum replicas.
1. Target CPU is expressed as % of the pods' CPU request.
1. Autoscaling depends on metrics being collected.
1. Metrics server is one solution for collecting metrics.

### Rolling Updates and Rollbacks
1. Rollouts update the deployment.
1. Any change to a deployment's template triggers a rollout.
1. Different rollout strategies are available.
1. Rolling updates  
    1. Default rollout strategy
    1. Updates in group rather than all at once.
    1. Both old and new version is running for some time.
    1. Alternative is the recreate strategy.
    1. Scaling is not a rollout.
    1. kubectl had commands to check, pause, resume and rollback(undo) rollouts.  
1. Rollouts can be paused, resumed and undone.  
  
   
### Probes(Also referred to as Healthchecks)  
#### Readiness Probe
1. Used to check when a pod is ready to serve traffic/handle requests.
1. Useful after startup to check external dependencies.
1. Readiness probes set the pod's ready condition. Service only send traffic to ready pods.
 
#### Liveness Probe
1. Detects when a pod enters a broken state.
1. K8s will restart the pod for you.
1. Declared in same way as rediness probe.

#### Declaring Probes
1. Probes can be declared in pod's containers.
1. All container probes must pass for the pod to pass
1. Probe actions can be command that run in the container such as an HTTP GET request, or opening a TCP socket.
1. Probes check the pod every 10 seconds by default. 

### Init Containers  
1. Motivation for Init Containers
    1. Sometimes you need to wait for a service, download, dynamic or decisions before starting a pod's containers.
    1. Prefer to separate initialization wait logic from the container image.
    1. Initialization is tightly coupled to the main application.
    1. Init containers allow you to run initialization tasks before starting the main container(s).
1. Pods can declare any number of init containers.
1. Init containers run in order and to completion.
1. use their own images.
1. Easy way to block or delay starting an application.
1. Runs every time a pod is created.
1. Don't support readiness probe.

### Volumes
K8s uses 2 high level storage types  
1. Volumes  
2. Persistent volumes  

Used by mounting directory in one of the container in a pod.

#### Volumes
1. Tied to a pod and their lifecycle
1. Share data between container and tolerate container restarts.
1. Use for non durable storage that is deleted with the pod.
1. Default volume type is emptyDir.
1. State is lost if Pod is rescheduled on a different node.

#### Persistent Volumes
1. Independent of Pod’s lifetime.
1. Pods make Persistent Volume claims to use throughout their lifetime.
1. Can be mounted by multiple Pods on different Nodes if underlying storage supports it.
1. Can be provisioned statically in advance or dynamically on demand. 

#### Persistent Volume Claims
1. Describe a pod’s request for persistent volume storage.
1. Includes how much storage, type of storage, and access mode.
1. Access mode can be read-write one, read only many, or read-write many
1. PVC stays pending if no PV can satisfy it and dynamic provisioning is not enabled.
1. Connects to pod through a Volume of type PVC.  

### ConfigMaps and Secrets
1. Separate configuration from Pod specs.
1. Results in easier to manage and more portable manifests.
1. Both are similar but Secrets are specifically for sensitive data.
1. There are specialised types of Secrets for storing Docker registry credentials and TLS certs.

#### Using ConfigMaps and Secrets
1. Data stored in key-value pairs.
1. Pods must reference ConfigMaps and Secrets to use their data.
1. References can be made by mounting Volumes or setting environment variables

### **Kubernetes Ecosystem**  
### Helm 
1. Kubernetes’ package manager  
1. Packages are called charts and are installed on your cluster using Helm CLI.
1. Helm charts make it easy to share complete applications.
1. Search Helm Hub for public charts.

### Helm Examples
1. Use Helm for our application’s Redis data tier
    1. Redis charts available
    1. Highly available Redis chart avoids a single point of failure.
    1. Install with a single command.
2. Create a chart for the entire sample application
     1. Share it with anyone

### Kustomize .io
1. Customize YAML manifets in k8s  
1. Helps you manage the complexity of your applications.
1. Works by using a customisation.yaml file that declares customisation rules  
1. Original manifests are untouched and remain usable.  

#### Kustomize examples
1. Generate ConfigMaps and Secrets from files.
1. Configure common fields across multiple resources.
1. Apply patches to any field in a manifest.
1. Use overlays to customise base groups of resources.

Kustomize is directly integrated with kubectl.

Include the —kustomize or -k option to kubectl create or apply

### Prometheus
1. Open source monitoring and alerting system.
1. A server for pulling in time series metric data and storing it.
1. Inspired by an internal monitoring tool at Google called borgmon.
1. De facto standard solution for monitoring Kubernetes.

#### Prometheus + Kubernetes
1. Kubernetes components supply all their own metrics in Prometheus format.  
    1. Many more metrics than Metrics Server
1. Adapter available to autoscale using metrics in Prometheus rather than CPU utilisation.
1. Commonly paired with Grafana for visualisations.
1. Define alert rules and send notifications.
1. Easily installed with Helm chart.

### Kubeflow
1. Makes deployment of machine learning workflows on Kubernetes simple, scalable and portable.
1. A complete machine learning stack.
1. Leverage Kubernetes to deploy anywhere, autoscale, etc.

### Knative
1. Platform for building, deploying and managing server less workloads on Kubernetes.
1. Can be deployed anywhere with Kubernetes, avoiding vendor lock-in
1. Supported by Google, IBM and SAP.  

### **Kubernetes Patterns**

### Multi-Container Patterns
Why Pods?  
1. Kubernetes needs additional information.
2. Simplifies using different underlying container runtimes.
3. Co-locate tightly coupled containers without packaging them as a single image.
  
### Patterns

#### Sidecar Pattern
1. Uses a helper container to assist a primary container.
1. Commonly used for logging, file syncing, watchers.
1. Benefits include leaner main container, failure isolation, independent update cycles.
	
#### Ambassador Pattern
1. Ambassador container is a proxy for communicating to and from the primary 		container.
1. Commonly used for communicating with databases.
1. Streamlined development experience, potential to reuse ambassador across 	   	languages. 
     		
#### Adapter pattern
1. Adapters present a standardised interface across multiple pods
1. Commonly used for normalising output logs and monitoring data.
1. Adapts third-party software to meet your needs.
  
### Networking
Each pod is assigned unique IP address. Containers within pod communicate with each other through localhost.

#### Network Policy
1. Rules for controlling network access to pods.
1. Similar to security groups controlling access to virtual machines.
1. Scoped to namespace.
1. Kubernetes network plugin must support network policy for it to apply.  
e.g. Calico, Romana

### Service Accounts
1. Provide an identity to pods in the cluster.
1. Stored in and managed by the cluster.
1. Scoped to namespace.

### Access Control
1. Service accounts are compatible with Role Based Access Control(RBAC).
1. Pods have a token mounted on a volume that can be used to authenticate requests.
1. Default service account token has no additional permissions than an unauthorised user.

### Cheat Sheet  

Enable autocompletion for the Kubernetes cluster manager utility kubectl:  
```
source <(kubectl completion bash)
```  
With completions, you can press tab twice to get a list of valid ways to complete any of the kubectl commands.  













    
