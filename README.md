# ckad
CKAD preparation material

**Overview**
1. Cluster is collectoin of nodes.
2. Nodes - Worker nodes and Master node
3. Worker nodes include the software to run container managed by k8s control plane
4. Master node runs the control plane
5. The control plane is set of APIs and software thats k8s users interact with.
6. APIs and software are referred to as master components. 

**Pods**
1. Pods are basic building block.
2. One or more containers in a Pod.
3. Pod container all share a container network.
4. One IP address per pod.  
Command to create Pod  
`kubectl create -f <Pod Definition File>`
Pod Definition file

**Service**
1. Service defines networking rules for accessing pods in the cluster and from internet.
1. Use labels to select a group of pods.
1. Service has a fixed IP address
1. It distributes requests across pods in group.
1. Service expose pods via static IP address.
1. NodePort service allows access from outside the cluster.

**Multi Container Pods**  

Namespaces
1. Sepearates resources according to users, environment or application.
1. Roles based access control(RBAC) to secure access per namespace.
1. Using namespace is a best practice.
1. `kubectl create -f <POD FILE> -n <namespace>` 
1. Container can communicate over localhost within a pod.
1. `kubectl logs` to view logs
1. Logs record what is written to standard output and standard error.

**Service Discovery**  
Mechanisms  
| Environment Variables | DNS |  
| ---- | --- |  
| Services address are automatically injected in containers | DNS records automatically created in clusters' DNS. |
| Environment variables follow naming convention based on service name | Containers automatically configured to query clusters' DNS. | 

**Deployments**  
1. Represents multiple replica for a pod.
1. Describes a desired state that k8s needs to achieve.
1. Deployment Controller master component converges actual state to the desired state.

**Auto Scaling**
1. Scale automatically based on CPU utilization or custom metrics.
1. Set target CPU along with minimum and maximum replicas.
1. Target CPU is expressed as % of the pods' CPU request.
1. Autoscaling depends on metrics being collected.
1. Metrics server is one solution for collecting metrics.

**Rolling Updates and Rollbacks**
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
  
   
**Probes(Also referred to as Healthchecks)**  
Readiness Probe
1. Used to check when a pod is ready to serve traffic/handle requests.
1. Useful after startup to check external dependencies.
1. Readiness probes set the pod's ready condition. Service only send traffic to ready pods.
 
Liveness Probe  
1. Detects when a pod enters a broken state.
1. K8s will restart the pod for you.
1. Declared in same way as rediness probe.

Declaring Probes
1. Probes can be declared in pod's containers.
1. All container probes must pass for the pod to pass
1. Probe actions can be command that run in the container such as an HTTP GET request, or opening a TCP socket.
1. Probes check the pod every 10 seconds by default. 










    
