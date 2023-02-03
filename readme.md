# Linkedin course
using docker, minikube, kubectl and busybox

# commands
minikube start
minikube delete

## Manage namespaces (prod/dev/...)
kubectl get namespaces
kubectl apply -f ./03_02_Begin/namespace.yaml
kubectl delete -f ./03_02_Begin/namespace.yaml

## Deploy application container (with # of instances to deploy)
kubectl apply -f ./03_03/deployment.yaml
kubectl get deployment -n development
kubectl get pods -n development -o wide

## Manage pods
kubectl delete pod pod-info-deployment-757cb75bbb-b2dtg -n development

kubectl describe pod pod-info-deployment-757cb75bbb-b88xn -n development

## Troubleshoot pods with busybox (from within the k8s cluster)
kubectl exec -it busybox-6b95744666-bhbtn -- /bin/sh
/ # wget 10.244.0.3:3000
/ # cat index.html

## Troubleshoot pods with logs
kubectl logs pod pod-info-deployment-757cb75bbb-b88xn - development

## Make the cluster available from outside
(term2) minikube tunnel
kubectl apply -f 04_01/service.yaml
kubectl get services -n development

# Control plane (not visible in managed clusters)
## api server
handles all communication in the cluster

kubectl api-resources

## file storage
etcd : file system

kubectl logs etcd-minikube -n kube-system | jq

## kube scheduler

## controller manager
checks resources are available

## cloud controller manager
interface to external world

# Worker nodes (min of 3)
Worker nodes = 
    kubelet : ensures containers in pod are health and communicates with api server
    container runtime : different engines are ContainerId, CRI-O, kata containers, aws firecracker (support for dockershim was removed in version 1.24)
        CRI (Interface) used by kubelet to create containers
    kube-proxy : ensures communication between pods and services. Comm directly with api server

# hardening deployments
        securityContext:
          allowPrivilegeEscalation: false
          runAsNonRoot: true
          capabilities:
            drop:
              - ALL
          readOnlyRootFilesystem: true

# security scan of yaml files
snyk iac test ./04_02_End/deployment.yaml 

# subscribe to cvs updates for kubernetes
https://www.cvedetails.com/vulnerability-list/vendor_id-15867/product_id-34016/Kubernetes-Kubernetes.html

# download the kubernetes hardening guide from us defense 
https://media.defense.gov/2022/Aug/29/2003066362/-1/-1/0/CTR_KUBERNETES_HARDENING_GUIDANCE_1.2_20220829.PDF
