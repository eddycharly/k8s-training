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

list nodes in the cluster along with their status, role age and kubelet version.

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

drain node, the node should be cordonned first to avoid scheduling new pods on the node being drained.

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

## --selector

```bash
kubectl get node --selector='!node-role.kubernetes.io/master'
kubectl get pods --all-namespaces --selector=app=admin
```

the `--selector` argument allows to query resources against resource labels.

```bash
NAMESPACE                                   NAME                     READY   STATUS    RESTARTS   AGE
admin-feature-allopneus-puchaseorder-push   admin-7484c47fbb-84j4g   2/2     Running   0          17m
admin-feature-cereal-seed                   admin-849dccd4b8-5jkc9   2/2     Running   0          7d14h
admin-feature-club                          admin-54b4879b9-zjmgb    2/2     Running   0          4h23m
admin-feature-composer-upgrade              admin-57b58dbc45-s5bvz   2/2     Running   0          2d5h
admin-feature-dll-extract-nocredit          admin-9d7d98fbd-7xxfq    2/2     Running   0          17m
admin-feature-group                         admin-57f8cdcb85-4pzz6   2/2     Running   0          17m
admin-feature-ms                            admin-7f6bdcd574-c96sp   2/2     Running   0          17m
admin-feature-oad-top-machine-types         admin-56b8c88dc5-htbcc   2/2     Running   0          18h
admin-feature-payment-schema-new            admin-7494b756f6-8crng   2/2     Running   0          2m58s
admin-fix-elasticsearch-bulk-param          admin-7f9f9ff6c7-ldpdp   2/2     Running   0          43h
admin-master                                admin-776999f899-x4mr2   2/2     Running   0          19h
admin-preprod-branch                        admin-7d9b999c4d-wh6xv   2/2     Running   0          7d14h
admin-release-1-64                          admin-57569655bf-2czmb   2/2     Running   0          17m
```

## --field-selector

```bash
kubectl get pods -n admin-master --field-selector=status.phase=Running
```

the `--field-selector` argument allows to query resources against resource field.

```bash
NAME                     READY   STATUS    RESTARTS   AGE
admin-776999f899-x4mr2   2/2     Running   0          20h
rabbitmq-0               2/2     Running   0          3d22h
```

##  -o jsonpath

```bash
kubectl get pods -all-namespaces --selector=app=admin -o jsonpath='{.items[*].metadata.name}'
```

list all pods in all namespaces that have le label `app=admin` and output their name.

the `-o jsonpath` argument allows to customize commands output.

## kubectl logs

```bash
kubectl logs admin-776999f899-x4mr2 -n admin-master -c migrate-db
kubectl logs admin-776999f899-x4mr2 -n admin-master --all-containers=true
```

fetch container logs in pods.

the `-c <container_name>` argument allows to fetch logs from a specific container in the pod.

the `--all-containers=true` argument allows to fetch logs from all containers in a given pod.

```bash
                    Agriconomie Migrations                    
                                                              

WARNING! You have 55 previously executed migrations in the database that are not registered migrations.
    >> 2018-11-02 11:35:07 (20181102113507)
    >> 2018-11-06 13:34:54 (20181106133454)
    >> 2018-11-07 13:23:29 (20181107132329)
    >> 2018-11-13 13:05:50 (20181113130550)
    >> 2018-11-14 10:25:01 (20181114102501)
    >> 2018-11-15 14:32:07 (20181115143207)
    >> 2018-11-27 10:33:40 (20181127103340)
    >> 2018-11-27 10:33:41 (20181127103341)
    >> 2018-11-28 16:22:31 (20181128162231)
    >> 2018-11-28 16:22:32 (20181128162232)
    >> 2018-12-04 08:03:05 (20181204080305)
    >> 2018-12-04 10:54:36 (20181204105436)
    >> 2018-12-11 07:30:30 (20181211073030)
    >> 2018-12-11 07:30:31 (20181211073031)
    >> 2018-12-31 10:00:00 (20181231100000)
    >> 2019-01-03 11:20:10 (20190103112010)
    >> 2019-01-14 13:18:55 (20190114131855)
    >> 2019-01-15 17:25:21 (20190115172521)
    >> 2019-01-21 12:53:14 (20190121125314)
    >> 2019-01-23 14:42:30 (20190123144230)
    >> 2019-01-24 11:27:00 (20190124112700)
    >> 2019-02-05 11:48:01 (20190205114801)
    >> 2019-02-06 13:38:52 (20190206133852)
    >> 2019-02-08 14:21:12 (20190208142112)
    >> 2019-02-14 09:08:43 (20190214090843)
    >> 2019-02-19 05:00:00 (20190218290000)
    >> 2019-02-19 14:23:07 (20190219142307)
    >> 2019-02-20 10:34:47 (20190220103447)
    >> 2019-02-25 17:50:11 (20190225175011)
    >> 2019-03-07 11:34:21 (20190307113421)
    >> 2019-03-14 10:03:47 (20190314100347)
    >> 2019-03-25 09:44:07 (20190325094407)
    >> 2019-04-01 09:10:01 (20190401091001)
    >> 2019-04-03 17:15:44 (20190403171544)
    >> 2019-04-16 15:38:29 (20190416153829)
    >> 2019-04-18 15:10:55 (20190418151055)
    >> 2019-04-29 16:55:07 (20190429165507)
    >> 2019-05-13 13:57:28 (20190513135728)
    >> 2019-05-20 12:32:07 (20190520123207)
    >> 2019-05-22 15:03:00 (20190522150300)
    >> 2019-05-24 08:28:56 (20190524082856)
    >> 2019-05-24 08:28:57 (20190524082857)
    >> 2019-05-29 09:35:20 (20190529093520)
    >> 2019-05-29 11:36:09 (20190529113609)
    >> 2019-05-31 14:52:26 (20190531145226)
    >> 2019-06-03 14:14:14 (20190603141414)
    >> 2019-06-04 16:52:38 (20190604165238)
    >> 2019-06-04 16:52:39 (20190604165239)
    >> 2019-06-05 12:11:28 (20190605121128)
    >> 2019-06-05 13:29:15 (20190605132915)
    >> 2019-06-07 10:39:54 (20190607103954)
    >> 2019-06-10 14:30:51 (20190610143051)
    >> 2019-06-11 10:34:05 (20190611103405)
    >> 2019-06-19 18:10:01 (20190619181001)
    >> 2019-06-21 14:51:48 (20190621145148)
No migrations to execute.

 // Clearing the cache for the preprod environment with debug                   
 // false                                                                       

 [OK] Cache for the "preprod" environment (debug=false) was successfully        
      cleared.                                                                  


 Installing assets as hard copies.

 --- ------------------------------ ---------------- 
      Bundle                         Method / Error  
 --- ------------------------------ ---------------- 
  ✔   WhiteOctoberPagerfantaBundle   copy            
  ✔   FOSJsRoutingBundle             copy            
  ✔   AgriconomieMailingBundle       copy            
  ✔   LexikTranslationBundle         copy            
  ✔   NelmioApiDocBundle             copy            
 --- ------------------------------ ---------------- 

 ! [NOTE] Some assets were installed via copy. If you make changes to these     
 !        assets you have to run this command again.                            

 [OK] All assets were successfully installed.                                   
```

## kubectl exec

```bash
kubectl exec admin-776999f899-x4mr2 -n admin-master -it -c php-fpm -- bash
```

executes a command in a pod container.
the command is specified after `--`.

the `-it` flag allows to open an interactive connection to the container.

the `-c <container_name>` argument allows to execute the command on a specific container in the pod.

## kubectl top

```bash
kubectl top node
kubectl top pod --all-namespaces
kubectl top pod admin-776999f899-x4mr2 -n admin-master
kubectl top pod admin-776999f899-x4mr2 -n admin-master --containers
```

show metrics for nodes, pods and containers.

the `--containers` flag allows to show metrics of containers in pods.

```bash
POD                      NAME      CPU(cores)   MEMORY(bytes)   
admin-776999f899-x4mr2   nginx     0m           3Mi             
admin-776999f899-x4mr2   php-fpm   0m           16Mi            
```

## kubectl run

```bash
kubectl run -it -n admin-master busybox --image=busybox -- sh
```

run a pod.
the command can be specified after `--`.

the `--image` flag allows to specify the image of the container to be run.

the `-it` flag allows to open an interactive connection to the container.

## kubectl delete

```bash
kubectl delete pods admin-776999f899-x4mr2 -n admin-master
```

delete a resource.

```bash
pod "admin-776999f899-x4mr2" deleted
```
