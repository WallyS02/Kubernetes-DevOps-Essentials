# Kubernetes-DevOps-Essentials
## If I'll find any other feature to be useful - it'll surely be added!
## What is it?
Container orchestration platform that automates their managing. It provides many benefits - allows for high availability services, auto-scaling containers, eases managing high quantities of containers and many more.
## Different options
Apart of standard k8s there is several other choices:
* **Minikube** - a tool for running a local, single-node Kubernetes cluster on a single machine, good for local learing, experiments, development and testing
* **k3s** - a lightweight Kubernetes distribution designed to run on edge computing and in resource-constrained environment, removes some components to reduce resource consumption
* **k3d** - a tool for running k3s clusters in Docker containers, it allows you to quickly create and destroy Kubernetes clusters on your local machine
* **k0s** - a minimal Kubernetes distribution that is easy to install and manage, runs both small and large clusters
* **MicroK8s** - lightweight Kubernetes distribution, designed for small-scale development, testing, and deployment, runs on a single node and is easy to configure
* **Kind** - a tool for running Kubernetes clusters in Docker containers, mainly for testing and development
* **EKS, AKS, GKE** - a managed Kubernetes service from cloud providers \(in order - Amazon, Azure, Google\)
## Architecture
Kubernetes cluster consists of multiple nodes that can be of 2 types:
* **Master Node** - manages k8s cluster
* **Worker Node** - does the cluster work, runs containers

Master Nodes are consisted of components:
* **kube-apiserver** - component that exposes an API of the cluster which allows for it's managing, kubectl uses it to execute commands
* **kube-scheduler** - schedules newly created pods to nodes for them to run on
* **kube-controller-manager** - runs controllers that maintain cluster state, e.g. Node Controller for monitoring nodes and alerting it's malfunctions
* **etcd** - key-value store that stores all cluster data, e.g. it's configuration and state
* **cloud-controller-manager** - runs cloud-specific controllers that integrate cluster with cloud

Worker nodes are consisted of components:
* **kubelet** - agent that manages containers running in pods and is responsible for communicating with master nodes
* **kube-proxy** - network proxy that maintains network rules which allow network communication of pods
* **Container Runtime** - software that allows running containers and manages their's lifecycle, e.g. Docker or containerd, absolute base of a cluster
## Kubectl
CLI tool for managing k8s cluster.\
To avoid typing kubectl everytime use standard alias:
```
alias k='kubectl'
```
To persist alias use commands:
```
cd ~
echo "alias k='kubectl'" >> .bashrc
source ~/.bashrc
```
Before using kubectl with cluster you need to configure cluster, user and context information:
```
kubectl config set-cluster <cluster_name> --server=<cluster_address>
kubectl config set-credentials <username> --token=<token>
kubectl config set-context <context_name> --cluster=<cluster_name> --user=<username>
kubectl config use-context <context_name>
```
### Essential commands
* **kubectl cluster-info** - displays addresses of the control plane and cluster services
  * **kubectl cluster-info dump** - displays detailed information about the cluster
* **kubectl get** - displays information about cluster resources
  * **kubectl get all** - displays information about all cluster resources
  * **kubectl get \<resource_type\>** - displays information about type-specified cluster resources, e.g. pods, services, deployments, etc.
* **kubectl describe \<resource_type\> \<resource_name\>** - displays detailed information about specified resource
* **kubectl create -f \<manifest_file\>** - creates resource from manifest file
* **kubectl run \<pod_name\> --image=\<image_name\>** - runs pod of specified name and image
* **kubectl expose \<resource_type\> \<resource_name\>** - exposes a resource as a new k8s service
  * **kubectl expose \<resource_type\> \<resource_name\> --type** - option that allows specifying service type, e.g. ClusterIP, NodePort, LoadBalancer, ExternalName
  * **kubectl expose \<resource_type\> \<resource_name\> --port** - option that allows specifying port number
* **kubectl apply -f \<manifest_file\>** - applies configuration to existing resource by manifest file, if resource does not exists it acts like kubectl create
* **kubectl set \<k8s_object\>** - configures specified features of object
  * **kubectl set resources \<resource_type\> \<resource_name\> --limits=cpu=\<cpu_limit\>,memory=\<memory_limit\> --requests=cpu=\<cpu_limit\>,memory=\<memory_limit\>** - configures compute resource requirements of a resource, --limits stands for maximum compute resource requirements, --requests stands for minimum compute resource requirements
  * **kubectl set image \<resource_type\>/\<resource_name\> \<container_name\>=\<new_image\>** - sets new image for specified container
* **kubectl edit \<resource_type\> \<resource_name\>** - allows for live editing of resources within the cluster, from the default editor
* **kubectl delete \<resource_type\> \<resource_name\>** - deletes specified resource
  * **kubectl delete -f \<manifest_file\>** - deletes specified resource by manifest file
* **kubectl logs \<pod_name\>** - displays logs of a container in specified pod
  * **kubectl logs \<pod_name\> -c \<container_name\>** - displays logs of a specified container in specified pod
* **kubectl exec \<pod_name\> -c \<container_name\> -- \<command\>** - executes command in specified container in specified pod
  * **kubectl exec \<pod_name\> -c \<container_name\> -it -- /bin/bash** - opens interactive shell in specified container in specified pod
* **kubectl attach \<pod_name\> -c \<container_name\>** - allows for attaching to running, specified container in specified pod, similar to kubectl exec
* **kubectl port-forward \<pod_name\> \<local_port\>:\<pod-port\>** - forwards one or more local ports to specified pod
* **kubectl proxy --\<port_number\>** - provides proxy server for accessing Kubernetes API services at specified port
* **kubectl cp** - copies files and directories to and from specified container and user's local system
  * **kubectl cp /local/path/to/file/or/directory \<namespace_name\>/\<pod_name\>:/container/path -c \<container_name\>** - copies files and directories from specified container to user's local system
  * **kubectl cp \<namespace_name\>/\<pod_name\>:/container/path -c \<container_name\> /local/path/to/file/or/directory** - copies files and directories from user's local system to specified container
* **kubectl auth** - inspects authorization
  * **kubectl auth whoami** - displays current user and her/his attributes
  * **kubectl auth can-i \<action\> --namespace=\<namespace_name\>** - displays if specified action is allowed for current user in specified namespace
* **kubectl debug --it \<pod_name\> --image=busybox** - allows for debugging resources using interactive debugging containers
* **kubectl rollout** - manages the rollout of one or many resources
  * **kubectl rollout restart \<resource_type\>/\<resource_name\>** - restarts a resource
  * **kubectl rollout undo \<resource_type\>/\<resource_name\>** - roll backs resource to previous rollout
  * **kubectl rollout status \<resource_type\>/\<resource_name\>** - displays status of the rollout
  * **kubectl rollout pause \<resource_type\>/\<resource_name\>** - marks specified resource as paused
  * **kubectl rollout resume \<resource_type\>/\<resource_name\>** - resumes specified resource
  * **kubectl rollout history \<resource_type\>/\<resource_name\>** - displays previous rollout revisions and configurations of specified resource
* **kubectl scale \<resource_type\> \<resource_name\> --replicas=\<number_of_replicas\>** - sets the new number of replicas of a specified resource
  * **kubectl scale -f \<manifest_file\> --replicas=\<number_of_replicas\>** - sets the new number of replicas of a specified resource by manifest file
* **kubectl autoscale \<resource_type\> \<resource_name\> --min=\<minimal_number_of_replicas\> --max=\<maximal_number_of_replicas\>** - creates an autoscaler that automatically chooses and sets the number of pods that run in a specified resource
  * **kubectl autoscale -f \<manifest_file\> --min=\<minimal_number_of_replicas\> --max=\<maximal_number_of_replicas\>** - creates an autoscaler that automatically chooses and sets the number of pods that run in a specified resource by manifest file
* **kubectl certificate** - modifies certificate resources
  * **kubectl certificate approve \<certificate_file_name\>** - approves a certificate signing request
  * **kubectl certificate deny \<certificate_file_name\>** - denies a certificate signing request
* **kubectl top \<nodes_or_pods\>** - displays CPU and memory consumption of nodes or pods
* **kubectl cordon \<node_name\>** - marks node as unschedulable
* **kubectl uncordon \<node_name\>** - marks node as schedulable
* **kubectl drain \<node_name\>** - enables the safe evacuation of all pods from a node before maintance or other actions, it moves running pods to other nodes
* **kubectl taint nodes \<node_name\> \<key\>=\<value\>:\<effect\>** - updates the taints on node, taints are used to repel workloads from a node unless the workload tolerates the taint, this is useful for ensuring that only specific types of workloads are scheduled on particular nodes, possible effects: NoSchedule, PreferNoSchedule, NoExecute
* **kubectl diff -f \<manifest_file\>** - allows for comparing differences between specified version of resource and current version of resource
* **kubectl patch \<resource_type\> \<resource_name\> -p '\{"spec"\: \{"field"\: "value"\}\}'** - updates specified fields of specified resource
* **kubectl replace -f \<manifest_file\>** - replaces specified resource with specified manifest file
* **kubectl wait --for-condition=\<condition\> \<resource_type\> \<resource_name\>** - pauses execution until specified condition is fulfilled, e.g. Ready
* **kubectl label \<resource_type\> \<resource_name\> \<key\>=\<value\>** - updates the labels on a specified resource
* **kubectl annotate \<resource_type\> \<resource_name\> \<key\>=\<value\>** - similar to kubectl label, updates the annotations \(arbitrary, non-identifying metadata\) on a specified resource
* **kubectl api-resources** - displays the supported resources by cluster
* **kubectl api-versions** - displays the supported versions of resources by cluster
* **kubectl config view** - displays merged kubeconfig settings or a specified kubeconfig file
* **kubectl config set-context --current --namespace=\<namespace_name\>** - switches context to specified namespace
## Manifests
Manifest are YAML or JSON \(usually YAML\) configuration files that define desired state of resources in cluster. Use them to manage resources in cluster.

Manifest consists of sections:
* **apiVersion** - specifies the Kubernetes API version that the manifest applies to
* **kind** - specifies resource type
* **metadata** - contains resource metadata like name, labels and annotations
* **spec** - contains desired resource state, its structure depends on a resource type
## Pods
Pods are the most basic unit in Kubernetes. It consists of one or more containers. Containers share namespace, volumes, IP address, port and other resources inside pod. Each pod has a unique inside cluster. Pods are short-lived and can be deleted, replaced and moved inside the cluster, k8s does not fix malfunctioning pods, instead it deletes it and creates a new one.

Example of Pod manifest:
```
apiVersion: <api_version>
kind: Pod
metadata:
  name: <pod_name>
  labels:
    <label_name>: <label_value>
spec:
  containers:
  - name: <container_name>
    image: <container_image>
    ports:
    - containerPort: <port_number>
    command: ["command", "to", "execute"]
    restartPolicy: <Always_OnFailure_or_Never>
  - name: <container_name>
    image: <container_image>
    ports:
    - containerPort: <port_number>
    command: ["command", "to", "execute"]
    restartPolicy: <Always_OnFailure_or_Never>
```
Avoid using pods directly, better use Deployments and similar higher abstraction resource types.\
Use multi-container pods only if containers need to closely cooperate.
## Services
Services are resources that exposes network application working in one or more pods in cluster. They provide stable endpoint for pod communication that masks their location in cluster.

Services types:
* **ClusterIP** - exposes service inside the cluster, its IP address is reachable only within the cluster, can be used for internal cluster communication
* **NodePort** - exposes service on static port of every node in the cluster, can be accessed from outside the cluster
* **LoadBalancer** - exposes service externally using an external load balancer, it must be provided or cluster can be integrated with cloud provider
* **ExternalName** - maps service on external DNS name, does not create endpoint inside the cluster, can be used for integration with external services

The spec section of Service manifest consists of:
* **selector** - specifies pods which will be part of service by label
* **ports** - specifies service ports, port stands for service port, targetPort stands for container port
* **type** - specifies service type

Example of ClusterIP, NodePort or LoadBalancer Service manifest:
```
apiVersion: <api_version>
kind: Service
metadata:
  name: <service_name>
spec:
  selector:
    <label_name>: <label_value>
  ports:
  - protocol: <TCP_UDP_or_SCTP>
    port: <port_number>
    targetPort: <port_number>
  type: <ClusterIP_NodePort_or_LoadBalancer>
```
Example of ExternalName Service manifest:
```
apiVersion: <api_version>
kind: Service
metadata:
  name: <service_name>
spec:
  type: ExternalName
  externalName: <external_dns_name>
```
## Ingresses
Ingresses are resources that manage external access to services in cluster, usually through HTTP/HTTPS. It works as a routing layer that directs traffic to services based on rules defined in manifest. Ingresses support SSL/TLS certificates for sercure connection.

In order for an Ingresses to work in cluster, there must be an Ingress Controller that implements Ingresses rules.\
Popular Ingress Controller are:
* NGINX Ingress Controller
* Traefik
* HAProxy
* Istio Gateway

Example of Ingress manifest:
```
apiVersion: networking.k8s.io/<api_version>
kind: Ingress
metadata:
  name: <ingress_name>
  annotations:
    # Annotations allow for Ingress Controller behaviour customization, in below case - NGINX Ingress Controller
    nginx.ingress.kubernetes.io/rewrite-target: <target_URI_where_traffic_must_be_redirected>
spec:
  rules:
  - host: <hostname>
    http:
      paths:
      - path: <path>
        pathType: <ImplementationSpecific_Exact_or_Prefix>
        backend:
          service:
            name: <service_name>
            port:
              number: <port_number>
  tls:
  - hosts:
    - <hostname>
    secretName: <tls_secret>
```
## Replica Sets
Replica Sets are resources that maintain a stable set of replica pods of specified quantity. If any pod fails, Replica Set will delete it and automaticaly create a new one.

The spec section of Replica Set manifest consists of:
* **selector** - specifies pods which will be part of replica set by label
* **template** - specifies template of pod that will be created with replica set

Example of ReplicaSet manifest:
```
apiVersion: apps/<api_version>
kind: ReplicaSet
metadata:
  name: <replica_set_name>
  labels:
    <label_name>: <label_value>
spec:
  replicas: <number_of_replicas>
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        <label_name>: <label_value>
    spec:
      containers:
      - name: <container_name>
        image: <image_name>
        ports:
        - containerPort: <port_number>
```
Similar to pods, avoid using replica sets directly, better use Deployments and similar higher abstraction resource types.
## Deployments
Deployments are resources managing a set of pods to run application workload, usually one that doesn't maintain state \(for stateful applications check Stateful Sets\).

The spec section of Deployment manifest consists of:
* **replicas** - specifies number of desired replicas of pods
* **selector** - specifies pods which will be part of deployment by label
* **template** - specifies template of pod that will be created with deployment
* **strategy** - specifies update strategy

Strategies types:
* **Recreate** - deletes all existing pods before new ones are created
* **RollingUpdate** - deletes and creates pods gradually, by replacing old instances with new one in a controlled manner
  * **maxUnavailable** - parameter that specifies the maximum number or percentage of pods that can be unavailable during the update
  * **maxSurge** - parameter that specifies the maximum number or percentage of pods that can be created above the desired number of pods

Example of Deployment manifest:
```
apiVersion: apps/<api_version>
kind: Deployment
metadata:
  name: <deployment_name>
  labels:
    <label_name>: <label_value>
spec:
  replicas: <number_of_replicas>
  selector:
    matchLabels:
      <label_name>: <label_value>
  template:
    metadata:
      labels:
        <label_name>: <label_value>
    spec:
      containers:
      - name: <container_name>
        image: <image_name>
        ports:
        - containerPort: <port_number>
  strategy:
    type: <strategy_type>
    # Rolling Update parametrs
    rollingUpdate:
      maxUnavailable: <percentage_or_absolute_number>
      maxSurge: <percentage_or_absolute_number>
```
## Stateful Sets
Stateful Sets are resources managing a set of pods and maintains a sticky identity for each of them, which is useful for managing stateful applications \(for stateless applications check Deployments\). Every pod has unique and stable name \(e.g. \<pod_name\>-0\, \<pod_name\>-1\, ...). Every pod can have own Persistent Volume that is persisted even after pod deletion. Pods are created and deleted in strict order.

The spec section of Stateful Set manifest consists of:
* **replicas** - specifies number of desired replicas of pods
* **minReadySeconds** - specifies minimum number of seconds for which newly created pod should be running and ready before being considered available
* **selector** - specifies pods which will be part of stateful set by label
* **serviceName** - specifies service name that ensures sticky identity for pods
* **template** - specifies template of pod that will be created with stateful set
  * **volumeMounts** - specifies volume mounted to the pod
* **strategy** - specifies update strategy
* **volumeClaimTemplates** - specifies volume claim template

Strategies types are the same as in Deployments.\
If volume is used with Stateful Set, it must be created before Stateful Set for Stateful Set to work properly.

Example of StatefulSet manifest:
```
apiVersion: apps/<api_version>
kind: StatefulSet
metadata:
  name: <stateful_set_name>
  labels:
    <label_name>: <label_value>
spec:
  replicas: <number_of_replicas>
  selector:
    matchLabels:
      <label_name>: <label_value>
  serviceName: "<service_name>"
  template:
    metadata:
      labels:
        <label_name>: <label_value>
    spec:
      containers:
      - name: <container_name>
        image: <image_name>
        ports:
        - containerPort: <port_number>
        volumeMounts:
        - name: <volume_name>
          mountPath: /mount/path/in/container
  strategy:
    type: <strategy_type>
    # Rolling Update parametrs
    rollingUpdate:
      maxUnavailable: <percentage_or_absolute_number>
      maxSurge: <percentage_or_absolute_number>
  volumeClaimTemplates:
  - metadata:
      <label_name>: <label_value>
    spec:
      accessModes: [ "<access_modes>" ] # see Volumes
      resources:
        requests:
          storage: <storage_size>
```
## Horizontal Pod Autoscalers
Horizontal Pod Autoscalers \(HPA\) are resources that automatically scales number of replica pods in Deployment, Replica Set or Stateful Set resources. HPA monitors metrics \(e.g. CPU or memory usage\) to scale number of pod replicas up or down. Metrics are collected by Metrics Server or other source of collected metrics \(like Prometheus\). Other sources of metrics require adapter to put them in use with cluster.

The spec section of HPA manifest consists of:
* **scaleTargetRef** - specifies scaled resource
* **minReplicas** - specifies minimal number of pod replicas
* **maxReplicas** - specifies maximal number of pod replicas
* **metrics** - specifies metrics that will be used in scaling algorithm \(e.g. CPU and memory usage\)

Example of HPA manifest:
```
apiVersion: autoscaling/<api_version>
kind: HorizontalPodAutoscaler
metadata:
  name: <hpa_name>
spec:
  scaleTargetRef:
    apiVersion: apps/<api_version>
    kind: <resource_type>
    name: <resource_name>
  minReplicas: <minimum_number_of_replicas>
  maxReplicas: <maximum_number_of_replicas>
  metrics:
  - type: Resource
    resource:
      name: <cpu_or_memory>
      target:
        type: Utilization
        averageUtilization: <average_resource_consumption>
```
## Config Maps
Config Maps are resources that store application configuration in key-value form. They can contain simple values \(like environment variables\) and more complex data too \(like config files\). Data in Config Maps is not secured, to store confidential data use Secrets.

Config Maps manifests consist of data section, instead of spec section, which stores configuration data.

Example of ConfigMap manifest:
```
apiVersion: <api_version>
kind: ConfigMap
metadata:
  name: <config_map_name>
  labels:
    <label_name>: <label_value>
data:
  # Simple values example
  key=value

  # Config file example
  <config_file_name>: |
    key1=value1
    key2=value2
    key3=value3
```

To use Config Maps you can add sections env, volumeMounts and volume to spec and spec.containers sections:
```
spec:
  containers:
    env:
      - name: <environment_variable_name_in_application>
        valueFrom:
          configMapKeyRef:
            name: <config_map_name>
            key: <environment_variable_name_in_config_map>
    volumeMounts:
    - name: <volume_name>
      mountPath: /mount/path/in/container
      readOnly: true
  volumes:
  -  name: <volume_name>
     configMap:
       name: <config_map_name>
       items:
       - key: <config_file_name_key>
         path: <config_file_name_path>
```
## Secrets
Secrets are resources that store confidential data in encrypted key-value pairs.

Similar to Config Maps, Secrets manifests consist of data section, instead of spec section, which stores confidential data.\
Secrets have types:
* **Opaque** - default secret type, used to store any data
* **kubernetes.io/tls** - used to store SSL/TLS certificates
* **kubernetes.io/dockerconfigjson** - used to store credentials for Docker registries

Example of Secret manifest:
```
apiVersion: <api_version>
kind: Secret
metadata:
  name: <secret_name>
  labels:
    <label_name>: <label_value>
type: <secret_type>
data:
  <key>: <encoded_value> # usually base64 encoding
  <key>: <encoded_value>
```
To use Secret you can do it the same as in Config Maps changing sections configMapKeyRef to secretKeyRef and configMap to secret.
## Network Policies
Network Policies are resources that define rules of network traffic between pods inside the cluster, specifing which pods can communicate with each other.

The spec section of Network Policy manifest consists of:
* **podSelector** - specifies pods which will be affected by network policy using labels
* **policyTypes** - specifies rules type
* **ingress** - specifies rules for incoming network traffic
* **egress** - specifies rules for outgoing network traffic

Example of Network Policy manifest:
```
apiVersion: networking.k8s.io/<api_version>
kind: NetworkPolicy
metadata:
  name: <network_policy_name>
  namespace: <namespace_name>
spec:
  podSelector:
    matchLabels:
      <label_name>: <label_value>
  policyTypes:
  - Ingress
  - Egress
  ingress:
  - from:
    - podSelector: # specifies pods that can communicate with pods affected by policy
        matchLabels:
          <label_name>: <label_value>
    - namespaceSelector: # specifies namespaces that can communicate with pods affected by policy
        matchLabels:
          <label_name>: <label_value>
    ports: # specifies ports allowed for traffic
    - protocol: <protocol>
      port: <port_number>
  egress:
  - to:
    - ipBlock: # specifies particular IP CIDR ranges that can communicate with pods affected by policy
        cidr: <ip/mask>
    ports:
    - protocol: <protocol>
      port: <port_number>
```
## Namespaces
Namespaces are logical units within a cluster that allow you to group and isolate resources. It resembles virtual clusters within a single physical cluster.

Cluster has 4 default namespaces:
* **default** - default namespace that in which resources are created if no other namespace is specified
* **kube-system** - namespace for Kubernetes system resources such as kube-dns, kube-proxy, etc.
* **kube-public** - namespace available to all users, often used to share public information
* **kube-node-lease** - namespace used to store information about node leases

Example of Namespace manifest:
```
apiVersion: <api_version>
kind: Namespace
metadata:
  name: <namespace_name>
```
To make the command apply to the specified namespace add **--namespace=\<namespace_name\>** to any command.\
Namespaces access should be controlled by RBAC, see Roles.
## Jobs and CronJobs
Jobs are resources that manage tasks which are pods that are stopped right after finishing its actions. They can be useful for batch jobs like executing scripts or generating reports.

The spec section of Job manifest consists of:
* **completions** - specifies how many times job needs to be completed successfully
* **parallelism** - specifies how many pods can be run in parallel
* **backoffLimit** - specifies maximum number of retries in case of failure
* **template** - specifies template of pod that will be created with job
* **restartPolicy** - specifies restart politics for pod, Never - pods are not restarted, OnFailure - pods are restarted on failure

Example of Job manifest:
```
apiVersion: batch/<api_version>
kind: Job
metadata:
  name: <job_name>
  labels:
    <label_name>: <label_value>
spec:
  completions: <number_of_completions>
  parallelism: <number_of_parallel_pods>
  backoffLimit: <maximum_number_of_retries>
  template:
    spec:
      containers:
      - name: <container_name>
        image: <image_name>
        command: ["command", "to", "execute"]
      restartPolicy: <restart_policy_type>
```
CronJobs are resources that act like Jobs but are scheduled to execute with cron-format schedule. They automate periodic Jobs.

The spec section of CronJob manifest consists of:
* **schedule** - specifies job's cron-format schedule
* **jobTemplate** - specifies template of job that will be used with cron job
* **concurrencyPolicy** - specifies concurrency policy, Allow - allows for concurrency, Forbid - forbids for concurrency, skips new job run if previous hasn't finished yet, Replace - replaces previous job run with new one if previous hasn't finished yet
* **successfulJobsHistoryLimit** - specifies how many succeeded cron jobs should be stored in history
* **failedJobsHistoryLimit** - specifies how many failed cron jobs should be stored in history

Example of CronJob manifest:
```
apiVersion: batch/<api_version>
kind: CronJob
metadata:
  name: <cron_job_name>
  labels:
    <label_name>: <label_value>
spec:
  schedule: "<cron_schedule>"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: <container_name>
            image: <image_name>
            command: ["command", "to", "execute"]
          restartPolicy: <restart_policy_type>
  concurrencyPolicy: Forbid
  successfulJobsHistoryLimit: <number_of_suceeded_cron_jobs_in_history>
  failedJobsHistoryLimit: <number_of_failed_cron_jobs_in_history>
```
## Daemon Sets
## Events
## Volumes
### Persistent Volumes
### Persistent Volumes Claims
### Storage Classes
## Roles
## Helm
## Kustomize
