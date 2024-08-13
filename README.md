# K8S-cheat
k8s cheat sheet 


# Kubernetes Cheat Sheet

A quick reference guide for commonly used Kubernetes commands.

## Aliases

### Linux
```bash
alias k=kubectl
```

### Windows
```powershell
Set-Alias -Name k -Value kubectl
```

## Cluster Management

### Get Clusters
```bash
kubectl config get-clusters
```
Example Output:
```
NAME
docker-for-desktop-cluster
foo
```

### Get Cluster Info
```bash
kubectl cluster-info
```
Example Output:
```
Kubernetes master is running at https://172.17.0.58:8443
```

## Contexts

### Get Contexts
```bash
kubectl config get-contexts
```
Example Output:
```bash
CURRENT   NAME                 CLUSTER                      AUTHINFO             NAMESPACE
          docker-desktop       docker-desktop               docker-desktop
*         foo                  foo                          foo                  bar
```

### Get Current Context
```bash
kubectl config current-context
```

### Switch Context
```bash
kubectl config use-context docker-desktop
```

### Set Default Namespace
```bash
kubectl config set-context $(kubectl config current-context) --namespace=my-namespace
```

*Tip:* To easily switch between contexts, consider using [kubectx](https://github.com/ahmetb/kubectx).

## Resource Management

### Get Resources
```bash
kubectl get all
kubectl get namespaces
kubectl get configmaps
kubectl get nodes
kubectl get pods
kubectl get rs
kubectl get svc kuard
kubectl get endpoints kuard
```

#### Useful Flags:
- `-o wide` - Show additional information.
- `--watch` or `-w` - Watch for changes in real-time.

### Namespaces

- To get resources from a specific namespace, use:
```bash
kubectl get pods --namespace=my-namespace
```
- Set the default namespace for the current context:
```bash
kubectl config set-context $(kubectl config current-context) --namespace=my-namespace
```

*Tip:* Use [kubens](https://github.com/ahmetb/kubectx/blob/master/kubens) to quickly switch between namespaces.

## Working with Labels

### Show Labels for Pods
```bash
kubectl get pods --show-labels
```

### Get Pods by Label
```bash
kubectl get pods -l environment=production,tier!=frontend
kubectl get pods -l 'environment in (production,test),tier notin (frontend,backend)'
```

## Resource Inspection

### Describe Resources
```bash
kubectl describe nodes [id]
kubectl describe pods [id]
kubectl describe rs [id]
kubectl describe svc kuard [id]
kubectl describe endpoints kuard [id]
```

## Deleting Resources

### Delete Resources
```bash
kubectl delete nodes [id]
kubectl delete pods [id]
kubectl delete rs [id]
kubectl delete svc kuard [id]
kubectl delete endpoints kuard [id]
```

- Force delete a pod without waiting for graceful shutdown:
```bash
kubectl delete pod-name --grace-period=0 --force
```

## Create vs Apply

- `kubectl create`: Use this command to create new resources.
- `kubectl apply`: Use this command to create or update resources, while retaining any manual changes like pod scaling.

#### Useful Flags:
- `--record` - Record the command that triggered the operation.
- `--recursive` - Recursively apply resources from a directory.

## Pod Management

### Create Pod
```bash
kubectl run kuard --generator=run-pod/v1 --image=gcr.io/kuar-demo/kuard-amd64:1 --output yaml --export --dry-run > kuard-pod.yml
kubectl apply -f kuard-pod.yml
```

### Export YAML for a New Pod
```bash
kubectl run my-cool-app --image=me/my-cool-app:v1 --output yaml --export --dry-run > my-cool-app.yaml
```

### Export YAML for an Existing Object
```bash
kubectl get deployment my-cool-app --output yaml --export > my-cool-app.yaml
```

## Deployment Management

### Create Deployment
```bash
kubectl run kuard --image=gcr.io/kuar-demo/kuard-amd64:1 --output yaml --export --dry-run > kuard-deployment.yml
kubectl apply -f kuard-deployment.yml
```

## Service Management

### Create Service
```bash
kubectl expose deployment kuard --port 8080 --target-port=8080 --output yaml --export --dry-run > kuard-service.yml
kubectl apply -f kuard-service.yml
```

## Logs Management

### View Logs
```bash
kubectl logs -l app=kuard
```

### View Logs for Previous Container
```bash
kubectl logs POD_NAME --previous
```

### Watch Logs in Real-Time
```bash
kubectl attach POD_NAME
```

### Copy Files from Pod
```bash
kubectl cp POD_NAME:/var/log .
```

*Tip:* Use [kail](https://github.com/boz/kail) for advanced log filtering.

## Port Forwarding

### Forward Ports
```bash
kubectl port-forward deployment/kuard 8080:8080
```

## Scaling

### Update Replicas
```bash
kubectl scale deployment nginx-deployment --replicas=10
```

## Autoscaling

### Set Autoscaling Configuration
```bash
kubectl autoscale deployment nginx-deployment --min=10 --max=15 --cpu-percent=80
```

## Rollout Management

### Check Rollout Status
```bash
kubectl rollout status deployment/nginx-deployment
```

### View Rollout History
```bash
kubectl rollout history deployment/nginx-deployment
kubectl rollout history deployment/nginx-deployment --revision=2
```

### Undo a Rollout
```bash
kubectl rollout undo deployment/nginx-deployment
kubectl rollout undo deployment/nginx-deployment --to-revision=2
```

### Pause/Resume a Rollout
```bash
kubectl rollout pause deployment/nginx-deployment
kubectl rollout resume deployment/nginx-deployment
```

## Example Configurations

### Pod Example
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: cuda-test
spec:
  containers:
    - name: cuda-test
      image: "k8s.gcr.io/cuda-vector-add:v0.1"
      resources:
        limits:
          nvidia.com/gpu: 1
  nodeSelector:
    accelerator: nvidia-tesla-p100
```

### Deployment Example
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  namespace: my-namespace
  labels:
    environment: production
    tier: frontend
  annotations:
    key1: value1
    key2: value2
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
          image: nginx:1.7.9
          ports:
            - containerPort: 80
```

## Kubernetes Dashboard

### Enable Proxy
```bash
kubectl proxy
```

## Azure Kubernetes Service (AKS)

[List of az aks commands](https://docs.microsoft.com/en-us/cli/azure/aks?view=azure-cli-latest)

### Get Credentials
```bash
az aks get-credentials --resource-group <Resource Group Name> --name <AKS Name>
```

### Show Dashboard
```bash
az aks browse --resource-group <Resource Group Name> --name <AKS Name>
```

### Upgrade AKS
```bash
az aks get-upgrades --resource-group <Resource Group Name> --name <AKS Name>
```

---
