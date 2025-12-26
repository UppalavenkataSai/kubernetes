Overview

Kubernetes is an open-source container orchestration platform. On AWS, EKS (Elastic Kubernetes Service) manages Kubernetes clusters.

eksctl → Cluster setup & management

kubectl → Resource & workload management inside clusters

eksctl vs kubectl
Tool	Purpose	When to Use
eksctl	Create/manage EKS clusters & nodegroups	First step, cluster-level operations
kubectl	Interact with Kubernetes resources & apps	After cluster exists, manage pods, deployments, services

Recommended Flow:

eksctl → create cluster

kubectl → deploy & manage workloads

Cluster Setup with eksctl
# Create cluster
eksctl create cluster \
  --name my-cluster \
  --region us-east-1 \
  --nodes 2 \
  --node-type t3.medium

# List clusters
eksctl get cluster

# Associate IAM OIDC provider
eksctl utils associate-iam-oidc-provider --region us-east-1 --cluster my-cluster --approve

# Delete cluster
eksctl delete cluster --name my-cluster --region us-east-1


AWS CLI Alternative:

aws eks create-cluster \
  --name my-cluster \
  --region us-east-1 \
  --kubernetes-version 1.28 \
  --role-arn arn:aws:iam::123456789012:role/EKSClusterRole \
  --resources-vpc-config subnetIds=subnet-abc123,subnet-def456,securityGroupIds=sg-0123456789abcdef0

Basic kubectl Commands
Cluster Info
kubectl cluster-info
kubectl get nodes
kubectl get pods
kubectl get all -A
kubectl config current-context

Deployments & ReplicaSets
kubectl apply -f deployment.yaml
kubectl get deployments
kubectl describe deployment my-deployment
kubectl scale deployment my-deployment --replicas=3
kubectl delete deployment my-deployment

Pods
kubectl get pods
kubectl describe pod <pod-name>
kubectl logs <pod-name>
kubectl exec -it <pod-name> -- /bin/bash
kubectl delete pod <pod-name>

Services
kubectl get svc
kubectl describe svc <svc-name>
kubectl delete svc <svc-name>

Namespaces
kubectl get ns
kubectl create ns dev
kubectl get all -n dev

Kubernetes Manifest Structure
Field	Type	Purpose	Example & Best Practices
apiVersion	string	API version	v1 for Pod/Service, apps/v1 for Deployment
kind	string	Resource type	Pod, Deployment, Service
metadata	object	Name, labels, namespace	Use meaningful names & labels
spec	object	Desired state	Define replicas, containers, ports, resource limits

Example Deployment YAML:

apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
        resources:
          limits:
            cpu: "500m"
            memory: "256Mi"
          requests:
            cpu: "250m"
            memory: "128Mi"

Namespaces

A Namespace logically isolates resources inside a cluster.

kubectl get ns
kubectl create ns dev
kubectl apply -f app.yaml -n dev


Benefits:

Isolation (dev/staging/prod)

Resource management (ResourceQuotas)

Access control (RBAC)

ReplicaSet & Deployment Flow

Flow: Deployment → ReplicaSet → Pods → Containers

Deployment defines blueprint & desired replicas

ReplicaSet ensures correct number of Pods

Pods run the containers

Containers execute the application

Visual Analogy:

Deployment (boss) 
  ↓
ReplicaSet (team leader) 
  ↓
Pods (workers) 
  ↓
Containers (tools)

Services

Expose Pods to internal/external traffic.

Type	Description
ClusterIP	Internal only
NodePort	Exposes via <NodeIP>:<NodePort>
LoadBalancer	External LoadBalancer via cloud provider
ExternalName	Maps to external DNS name

NodePort Example:

Client → NodeIP:NodePort → ClusterIP → Pods

Troubleshooting Commands
kubectl logs <pod-name>
kubectl exec -it <pod-name> -- /bin/bash
kubectl top nodes
kubectl get events
kubectl get pods --watch
kubectl port-forward <pod-name> 8080:80


✅ Pro Tips:

Always validate YAML before applying:

kubectl apply --dry-run=client -f file.yaml


Use namespaces for environment separation

Use labels consistently

Store YAML in Git for GitOps
