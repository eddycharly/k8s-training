# k8s-training / kubectl / basic-commands

in this training we'll cover kubectl basic commands.

## relevent documentation
- [kubectl cheatsheet](https://kubernetes.io/fr/docs/reference/kubectl/cheatsheet/)

## kubectl cluster-info

```bash
kubectl cluster-info
```

displays addresses of the master and services.

```bash
Kubernetes master is running at https://api.k8s.dev.agriconomie.com
CoreDNS is running at https://api.k8s.dev.agriconomie.com/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
kubernetes-dashboard is running at https://api.k8s.dev.agriconomie.com/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:https/proxy
```

## kubectl cluster-info dump

```bash
kubectl cluster-info dump
```

dumps current cluster state to stdout.

```bash
Kubernetes master is running at https://api.k8s.dev.agriconomie.com
CoreDNS is running at https://api.k8s.dev.agriconomie.com/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
kubernetes-dashboard is running at https://api.k8s.dev.agriconomie.com/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:https/proxy
```

## kubectl get nodes

```bash
kubectl get nodes
```

outputs the list of nodes in the cluster along with their status, role age and kubelet version.

```bash
NAME                                            STATUS     ROLES    AGE     VERSION
ip-10-40-19-152.eu-central-1.compute.internal   Ready      node     7d15h   v1.12.8
ip-10-40-20-102.eu-central-1.compute.internal   Ready      node     35d     v1.12.8
ip-10-40-21-192.eu-central-1.compute.internal   Ready      node     35d     v1.12.8
ip-10-40-22-20.eu-central-1.compute.internal    Ready      node     7d14h   v1.12.8
ip-10-40-24-98.eu-central-1.compute.internal    Ready      node     35d     v1.12.8
ip-10-40-26-61.eu-central-1.compute.internal    Ready      node     7d15h   v1.12.8
ip-10-40-27-105.eu-central-1.compute.internal   NotReady   node     10d     v1.12.8
ip-10-40-27-227.eu-central-1.compute.internal   Ready      master   35d     v1.12.8
ip-10-40-27-228.eu-central-1.compute.internal   Ready      master   35d     v1.12.8
ip-10-40-27-52.eu-central-1.compute.internal    Ready      master   35d     v1.12.8
ip-10-40-28-231.eu-central-1.compute.internal   Ready      node     7d15h   v1.12.8
ip-10-40-30-215.eu-central-1.compute.internal   Ready      node     31d     v1.12.8
```

## kubectl cordon

```bash
kubectl cordon ip-10-40-27-105.eu-central-1.compute.internal
```

mark node as unschedulable, the kubernetes scheduler will not schedule pods on the given node.

```bash
node/ip-10-40-27-105.eu-central-1.compute.internal cordoned
```

## kubectl uncordon

```bash
kubectl uncordon ip-10-40-27-105.eu-central-1.compute.internal
```

mark node as schedulable, the kubernetes scheduler will schedule pods on the given node.

```bash
node/ip-10-40-27-105.eu-central-1.compute.internal uncordoned
```

## kubectl drain

```bash
kubectl drain ip-10-40-27-105.eu-central-1.compute.internal --ignore-daemonsets --delete-local-data
```

drains a node, the node should be cordonned first to avoid scheduling new pods on the node being drained.

```bash
node/ip-10-40-27-105.eu-central-1.compute.internal already cordoned
WARNING: ignoring DaemonSet-managed Pods: kube-system/calico-node-fn84r, monitoring/prometheus-operator-prometheus-node-exporter-s4fjz, monitoring/prometheus-process-exporter-djcl9
evicting pod "api-5546d4667c-cg5js"
evicting pod "admin-7484c47fbb-m5sqs"
evicting pod "cart-processor-notifications-86bb9d568c-249zz"
evicting pod "catalog-processor-commands-7b8c4fcc79-mn9x5"
evicting pod "catalog-processor-649d6d7dcb-8rcv9"
evicting pod "admin-9d7d98fbd-sthtm"
...
```

## kubectl get services

```bash
kubectl get services
kubectl get services --all-namespaces
kubectl get services -n admin-master
```

list services.
`--all-namespaces` flag allows to list services in all namespaces.
`-n <namespace>`argument alows to list services in a specific namespace.

```bash
NAME                TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                                          AGE
admin               ClusterIP   100.64.236.194   <none>        80/TCP                                           10d
rabbitmq            ClusterIP   100.64.114.210   <none>        4369/TCP,5672/TCP,25672/TCP,15672/TCP,9419/TCP   10d
rabbitmq-headless   ClusterIP   None             <none>        4369/TCP,5672/TCP,25672/TCP,15672/TCP            10d
```

## kubectl get pods

```bash
kubectl get pods
kubectl get pods --all-namespaces
kubectl get pods -n admin-master
```

list pods.
`--all-namespaces` flag allows to list pods in all namespaces.
`-n <namespace>`argument alows to list pods in a specific namespace.

```bash
NAME                     READY   STATUS    RESTARTS   AGE
admin-776999f899-x4mr2   2/2     Running   0          19h
rabbitmq-0               2/2     Running   0          3d21h
```
