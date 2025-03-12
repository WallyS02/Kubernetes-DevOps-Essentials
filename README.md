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
## Manifests
Manifest are YAML or JSON \(usually YAML\) configuration files that define desired state of resources in cluster. Use them to manage resources in cluster.

Manifest consists of sections:
* **apiVersion** - specifies the Kubernetes API version that the manifest applies to
* **kind** - specifies resource type
* **metadata** - contains resource metadata like name, labels and annotations
* **spec** - contains desired resource state, its structure depends on a resource type
## Pods
Pods are the most basic unit in Kubernetes. It consists of one or more containers. Containers share namespace, volumes, IP address, port and other resources inside pod. Each pod has a unique inside cluster. Pods are short-lived and can be deleted, replaced and moved inside the cluster, k8s does not fix malfunctioning pods, instead it deletes it and creates a new one.

Example of pod manifest:
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
Best practises:
* avoid using pods directly, better use Deployments and similar higher abstraction resource types
* use multi-container pods only if containers need to closely cooperate
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
## Gateway

## Replica Sets
## Replication Controllers
## Autoscalers
## Deployments
## Stateful Sets
## Config Maps
## Secrets
## Network Policies
## Namespaces
## Jobs and CronJobs
## Daemon Sets
## Events
## Volumes
## Roles
## Helm
## Kustomize
