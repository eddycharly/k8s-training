# k8s-training / k8s-resources / namespaces

in this training we'll cover kubernetes `namespaces`.

## relevant documentation
- [kubernetes namespaces](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)

## presentation

in a nutshell, namespaces provide a scope for names.

names of resources need to be unique within a namespace, but not across namespaces.

namespaces can not be nested inside one another and each Kubernetes resource can only be in one namespace.

effectively, namespaces are a way to divide cluster resources between multiple users.

most resources are bound to a namespace but not all. for example nodes or persistent volumes are not.

## kubectl get namespaces

```bash
kubectl get namespaces
```

shows the list of namespaces that exist inside a cluster.

```bash
NAME          STATUS   AGE
default       Active   5m10s
kube-public   Active   5m10s
kube-system   Active   5m10s
```

## kubectl create namespace

```bash
kubectl create namespace scratch
```

creates a namespace named `scratch`.

this uses the imperative aproach, below is the equivalent declarative aproach.

```bash
namespace/scratch created
```

## kubectl delete namespace

```bash
kubectl delete namespace scratch
```

deletes the namespace named `scratch`.

when a namespace is deleted, all resources bound to the deleted namespace are also deleted.

```bash
namespace "scratch" deleted
```

## kubectl create

```bash
cat <<EOT > namespace.yaml
apiVersion: v1
kind: Namespace
metadata:
  name: scratch
EOT
```

```bash
kubectl create -f namespace.yaml
```

creates a namespace named `scratch`.

this uses the declarative aproach, resources are declared in files using yaml syntax.

kubernetes will take actions to reach the desired state declared in the yaml manifests.

```bash
namespace/scratch created
```

## kubectl apply

```bash
cat <<EOT > namespace.yaml
apiVersion: v1
kind: Namespace
metadata:
  name: scratch
EOT
```

```bash
kubectl apply -f namespace.yaml
```

updates namespace named `scratch` using the declarative aproach.

```bash
namespace/scratch configured
```

## kubectl delete

```bash
kubectl delete -f namespace.yaml
```

deletes the namespace named `scratch` using the declarative aproach.

```bash
namespace "scratch" deleted
```
