**Q.Create and Consume a Secret using a Volume**

    Create a secret named app-secret in the yqe Namespace that stores key-value pair of password=abnaoieb2073xsj
    
    Create a Pod that consumes the app-secret Secret using a Volume that mounts the Secret in the /etc/app directory. The Pod should be named app and run a memcached container.

**Solution**    

Create secret
```
kubectl create secret generic app-secret --from-literal=password=abnaoieb2073xsj -n yqe
```

Create pod from file with name secret-pod.yaml. Find sample file at https://kubernetes.io/docs/concepts/configuration/secret/
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: app
  labels:
    name: secret-test
spec:
  volumes:
  - name: secret-volume
    secret:
      secretName: app-secret
  containers:
  - name: secret-app-container
    image: memcached
    volumeMounts:
    - name: secret-volume
      readOnly: true
      mountPath: "/etc/app"
```

Create pod from file in the same namespace as secret.
```
kubectl create -f secret-pod.yaml -n yqe
```

**Q. Update Deployment with New Service Account**

    A Deployment named secapp has been created in the app namespace and currently uses the default ServiceAccount. Create a new ServiceAccount named secure-svc in the app namespace and then use it within the existing secapp Deployment, ensuring that the replicas now run with it.
    
**Solution**

Create service account using kubectl create command
```
kubectl create serviceaccount secure-svc -n app
```

Create a file named patch.yaml and configure the replicas serviceAccountName to be secure-svc
```YAML
spec:
  template:
    spec:
      serviceAccountName: secure-svc
```

Patch the existing Deployment
```
kubectl patch deployment -n app secapp --patch "$(cat patch.yaml)"
```

**Q. Pod Security Context Configuration**

    Create a pod named secpod in the dnn namespace which includes 2 containers named c1 and c2. Both containers must be configured to run the bash image, and should execute the command /usr/local/bin/bash -c sleep 3600. Container c1 should run as user ID 1000, and container c2 should run as user ID 2000. Both containers should use file system group ID 3000.
    
**Solution**

Start by generating a Pod manifest template specifying as many parameters possible when using kubectl run - redirect and save the output into a file named pod.yaml 
```
(kubectl run -n dnn --image=bash secpod --dry-run=client -o yaml -- /usr/local/bin/bash -c 'sleep 3600') > pod.yaml
```
pod.yaml contains
```YAML
apiVersion: v1
kind: Pod
metadata:
 creationTimestamp: null
 labels:
  env: prod
 name: secpod
 namespace: dnn
spec:
 containers:
  - args:
  - /usr/local/bin/bash
  - -c
  - sleep 3600
  image: bash
  name: secpod
  resources: {}
 dnsPolicy: ClusterFirst
 restartPolicy: Always
status: {} 
```

Modify the pod.yaml template, first duplicate the container section to create containers c1 and c2, and then add in the required security context configuration
```YAML
apiVersion: v1
kind: Pod
metadata:
 creationTimestamp: null
 labels:
  env: prod
 name: secpod
 namespace: dnn
spec:
 securityContext:
  fsGroup: 3000
 containers:
 - name: c1
   securityContext:
    runAsUser: 1000
   args:
   - /usr/local/bin/bash
   - -c
   - sleep 3600
   image: bash
   resources: {}
 - name: c2
   securityContext:
    runAsUser: 2000
   args:
   - /usr/local/bin/bash
   - -c
   - sleep 3600
   image: bash
   resources: {}
 dnsPolicy: ClusterFirst
 restartPolicy: Always
status: {}
```

Using kubectl apply - create the pod resource within the cluster
```
kubectl apply -f pod.yaml
```

Confirm that the pod has been successfully created
```
kubectl get pods -n dnn
```

Confirm the the pod contains 2 containers c1 and c2 and that they have been configured with the required security context
```
kubectl exec -it -n dnn secpod -c c1 -- id
uid=1000 gid=0(root) groups=3000

kubectl exec -it -n dnn secpod -c c2 -- id
uid=2000 gid=0(root) groups=3000
```

**Q. Pod Resource Constraints**
    Create a new Pod named web1 in the ca100 namespace using the nginx image. Ensure that it has the following 2 labels env=prod and type=processor. Configure it with a memory request of 100Mi and a memory limit at 200Mi. Expose the pod on port 80.
    
**Solution**

Create a new pod using the kubectl run command
```
kubectl run -n ca100 --image=nginx web1 -l env=prod -l type=processor --requests='memory=100Mi' --limits='memory=200Mi' --port=80
```

**Q.Create a Pod with Config Map Environment Vars**
    Create a new ConfigMap named config1 in the ca200 namespace. The new ConfigMap should be created with the following 2 key/value pairs:
    
    COLOUR=red
    SPEED=fast
    Launch a new Pod named redfastcar in the same ca200 namespace, using the image busybox. The redfastcar pod should expose the previous ConfigMap settings as environment variables inside the container. Configure the redfastcar pod to run the command:  /bin/sh -c "env | grep -E 'COLOUR|SPEED'; sleep 3600"

**Solution**

Create a new ConfigMap using the kubectl create command with the required key/value pairs
```
kubectl create configmap config1 -n ca200 --from-literal COLOUR=red --from-literal SPEED=fast
```