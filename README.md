# Service Monitoring with Prometheus

## Prerequisites

* Application with metrics instrumented
* Application which already exposing prometheus metrics on path `/metrics` on application port
* Kubernetes cluster
* Helm v3


## Usage

### Before you begin

Change `<YOUR_NAMESPACE>` in every file in `kube-manifests/` dir with the name of your namespace.

### 1. Deploy prometheus operator

```bash
helm upgrade --install prometheus-operator prometheus -f prometheus/custom-values.yaml --namespace prometheus-operator --create-namespace
```

Switch to your Namespace

```
kubectl config set-context --current --namespace=NAMESPACE
```

### 2. Create service for your application deployment

* Create `service.yaml` file 
* Add namespace value
* Add port number of app

```yaml
apiVersion: v1
kind: Service
metadata:
  name: app-service
  labels:
   ## Replace with you namespace
    prometheus: <YOUR_NAMESPACE>

spec:
  selector:
    ## your application pods labels
    app: myapp
  ports:
  - port: <PORT>
    name: http
    targetPort: <Target Port>
```

Deploy the Service

```bash
kubectl apply -f service.yaml -n NAMESPACE
```


### Create Service monitor resource

```
kubectl apply -f kube-manifests/service-monitor.yaml -n NAMESPACE
```


### Create service account, role, & rolebinding

```
kubectl apply -f kube-manifests/serviceaccount.yaml -n NAMESPACE
kubectl apply -f kube-manifests/role.yaml -n NAMESPACE
kubectl apply -f kube-manifests/rolebinding.yaml -n NAMESPACE
```

### Create Prometheus Custom resource

```
Kubectl apply -f kube-manifests/ -n NAMESPACE
```