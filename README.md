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
## Manifests
## Pods
## Services
## Ingresses
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
