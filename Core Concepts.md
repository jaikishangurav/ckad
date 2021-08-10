**Q. Create and Configure a Basic Pod**  

    Create a Pod in the cre Namespace with the following configuration:
    The Pod is named basic
    The Pod uses the nginx:stable-alpine-perl image for its only container
    Restart the Pod only OnFailure
    Ensure port 80 is open to TCP traffic

**Solution**

Create a Pod in the cre Namespace with the following configuration: the Pod is named basic, the Pod uses the nginx:stable-alpine-perl image for its only container, restart the Pod only OnFailure, ensure port 80 is open to TCP traffic

```
kubectl run -n cre --image=nginx:stable-alpine-perl --restart=OnFailure --port=80 basic
```

**Q. Create a new Namespace named workers and within it launch a Pod with the following configuration:**

    The Pod is named worker
    The Pod uses the busybox image for its only container
    Restart the Pod Never
    Command: /bin/sh -c "echo working... && sleep 3600"
    Label 1: company=acme
    Label 2: speed=fast
    Label 3: type=async
    
**Solution**

Create a new Namespace named workers and then create a Pod within it using the following configuration: the Pod is named worker, the Pod uses the busybox image for its only container, the Pod has the labels company=acme, speed=fast, type=async, the Pod runs the command /bin/sh -c "echo working... && sleep 3600"
```
kubectl create ns workers
kubectl run -n workers worker --image=busybox --labels="company=acme,speed=fast,type=async" -- /bin/sh -c "echo working... && sleep 3600"
```

**Q. Update the Label on a Running Pod**

    The ca200 namespace contains a running Pod named compiler. Without restarting the pod, update and change it's language label from java to python.
    
**Solution**

```
kubectl label --overwrite pods compiler language=python -n=ca200
# Edit and save pod - this will update the pod without restarting it
kubectl edit -n ca200 compiler
```

**Q. Get the Pod IP Address using JSONPath**

    Discover the Pod IP address assigned to the pod named ip-podzoid running in the ca300 namespace using JSONPath (hint: use -o jsonpath). Once you've established the correct kubectl command to do this, save the command (not the result) into a file located here: /home/ubuntu/podip.sh
    
**Solution**

```
kubectl get pods ip-podzoid  -n ca300 -o jsonpath="{.status.podIP}"
# Step 1 - examine the json structure for the pod in question, in particular look to see where the pod assigned ip address is located - it is in the .status.podIP field
kubectl get pods -n ca300 ip-podzoid -o json
    
# Step 2 - using the information from the previous step, build out the actual jsonpath based expression to return only the pod ip address
kubectl get pods -n ca300 ip-podzoid -o jsonpath={.status.podIP}
    
# Step 3 - write out the working kubectl command to the file /home/ubuntu/podip.sh
echo "kubectl get pods -n ca300 ip-podzoid -o jsonpath={.status.podIP}" > /home/ubuntu/podip.sh
```

**Q. Generate Pod YAML Manifest File**

    Generate a new Pod manifest file which contains the following configuration:
    
    Pod name: borg1
    Namespace to launch in: core-system
    Container image: busybox
    Command: /bin/sh -c "echo borg.running... && sleep 3600"
    Restart policy: Always
    Pod label: platform=prod
    Environment variable: system=borg
    Save the resulting manifest to the following location: /home/ubuntu/pod.yaml
    
**Solution**

```
kubectl run -n core-system --image=busybox --restart=Always --labels="platform=prod" --env="system=borg" -- /bin/sh -c "echo borg.running... && sleep 3600" borg1 --dry-run=client -o yaml -f /home/ubuntu/pod.yaml
kubectl get pod borg1 -n core-system -o yaml -f /home/ubuntu/pod.yaml
# Use kubectl run command specifically with the --dry-run=client and -o yaml parameters - and then redirect the output to file
kubectl run -n core-system borg1 --image=busybox --restart=Always --labels="platform=prod" --env="system=borg" -o yaml --dry-run=client -- /bin/sh -c "echo borg.running... && sleep 3600" > /home/ubuntu/pod.yaml
```

**Q. Launch Pod and Configure it's Termination Shutdown Time**

    Launch a new web server Pod in the sys2 namespace with the following configuration:
    
    Pod name: web-zeroshutdown
    Container image: nginx
    Restart policy: Never
    Ensure the pod is configured to terminate immediately when requested to do so by configuring it's terminationGracePeriodSeconds setting.
    
**Solution**
```
kubectl run web-zeroshutdown -n=sys2 --image=nginx --restart=Always --grace-period=0
# Step 1 - Use kubectl run command specifically with the --dry-run=client and -o yaml parameters - and then redirect the output to file
kubectl run web-zeroshutdown -n sys2 --image=nginx --restart=Never --port=80 -o yaml --dry-run=client > pod-zeroshutdown.yaml
    
# Step 2 - use kubectl explain pod.spec to check the syntax and how the terminationGracePeriodSeconds attribute should be configured
kubectl explain pod.spec
    
# Step 3 - use vim and edit and update the pod-zeroshutdown.yaml file, adding in -> terminationGracePeriodSeconds: 0
    apiVersion: v1
    kind: Pod
    metadata:
     creationTimestamp: null
     labels:
      run: web-zeroshutdown
     name: web-zeroshutdown
     namespace: sys2
    spec:
     terminationGracePeriodSeconds: 0
     containers:
     - image: nginx
       name: web-zeroshutdown
       ports:
       - containerPort: 80
       resources: {}
       dnsPolicy: ClusterFirst
       restartPolicy: Never
    status: {}
    
# Step 4 - use kubectl apply -f pod-zeroshutdown.yaml to create the pod resource within the cluster
kubectl apply -f pod-zeroshutdown.yaml
```
