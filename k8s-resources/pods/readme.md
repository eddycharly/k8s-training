# k8s-training / k8s-resources / pods

in this training we'll cover kubernetes `pods`.

## relevant documentation
- [pod overview](https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/)

## presentation

a pod is the basic unit that Kubernetes deals with, a group of containers.
it represents processes running on your Cluster.

a pod encapsulates one or more containers, storage resources, a unique network ip, and options that govern how the container(s) should run.
a Pod represents a unit of deployment: a single instance of an application in kubernetes, which might consist of either a single container or a small number of containers that are tightly coupled and that share resources.

when a pod encapsulates an application composed of multiple containers, the containers are automatically co-located and co-scheduled on the same physical or virtual machine in the cluster.
the containers can share resources and dependencies, communicate with one another, and coordinate when and how they are terminated.

pods provide two kinds of shared resources for their constituent containers: networking and storage.

**Networking**
each pod is assigned a unique ip address.
every container in a pod shares the network namespace, including the ip address and network ports.
containers inside a Pod can communicate with one another using localhost.
when containers in a pod communicate with entities outside the pod, they must coordinate how they use the shared network resources (such as ports).

**Storage**
a pod can specify a set of shared storage volumes.
all containers in the pod can access the shared volumes, allowing those containers to share data.
volumes also allow persistent data in a pod to survive in case one of the containers within needs to be restarted.

## kubectl get pods

```bash
kubectl get pods --all-namespaces
```

shows the list of pods that exist inside a cluster.

flag `--all-namespaces` allows to list all pods regardless of the namespace they are bound to.

```bash
NAMESPACE     NAME                                              READY   STATUS        RESTARTS   AGE
default       default-http-backend-855bc7bc45-rdf8s             1/1     Running       0          12h
default       nginx-ingress-microk8s-controller-lq8q2           1/1     Running       0          12h
kube-system   heapster-v1.5.2-64874f6bc6-2jgqq                  4/4     Running       0          12h
kube-system   hostpath-provisioner-599db8d5fb-tsg7p             1/1     Running       0          12h
kube-system   kube-dns-6ccd496668-t8t2r                         3/3     Running       0          12h
kube-system   kubernetes-dashboard-654cfb4879-87hhf             1/1     Running       0          12h
kube-system   monitoring-influxdb-grafana-v4-6679c46745-tltn9   2/2     Running       0          12h
```

## deploy pod manifest

```bash
cat <<EOT >> pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: mariadb
  namespace: scratch
spec:
  containers:
    - name: mariadb
      image: mariadb
      env:
        - name: MYSQL_ROOT_PASSWORD
          value: rootpassword
        - name: MYSQL_DATABASE
          value: db
        - name: MYSQL_USER
          value: user
        - name: MYSQL_PASSWORD
          value: password
      ports:
        - containerPort: 3306
EOT
```

```bash
kubectl apply -f ./pod.yaml
```

this will create a manifest for a pod and deploy it using `kubectl apply`.

the pod contains a single container named mariadb and running the mariadb docker image.
the container will be passed the needed environment variables to run.
it will also allow trafic on the port 3306.
the pod will be bound to the `scratch` namespace.

```bash
pod/mariadb created
```

## pod labels

```bash
cat <<EOT >> pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: mariadb
  namespace: scratch
  labels:
    app: mariadb
spec:
  containers:
    - name: mariadb
      image: mariadb
      env:
        - name: MYSQL_ROOT_PASSWORD
          value: rootpassword
        - name: MYSQL_DATABASE
          value: db
        - name: MYSQL_USER
          value: user
        - name: MYSQL_PASSWORD
          value: password
      ports:
        - containerPort: 3306
EOT
```

```bash
kubectl apply -f ./pod.yaml
```

this will deploy the same pod as previously but will add labels to it.

labels are identiying information, they are used to identify a set of resources.

## pod annotations

```bash
cat <<EOT >> pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: mariadb
  namespace: scratch
  annotations:
    deployed-by: someone
spec:
  containers:
    - name: mariadb
      image: mariadb
      env:
        - name: MYSQL_ROOT_PASSWORD
          value: rootpassword
        - name: MYSQL_DATABASE
          value: db
        - name: MYSQL_USER
          value: user
        - name: MYSQL_PASSWORD
          value: password
      ports:
        - containerPort: 3306
EOT
```

```bash
kubectl apply -f ./pod.yaml
```

this will deploy the same pod as previously but will add annotations to it.

annotations are non identiying information, they are used to attach metadata to resources.

## pod with two containers

```bash
cat <<EOT >> pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: mariadb
  namespace: scratch
spec:
  containers:
    - name: mariadb
      image: mariadb
      env:
        - name: MYSQL_ROOT_PASSWORD
          value: rootpassword
        - name: MYSQL_DATABASE
          value: db
        - name: MYSQL_USER
          value: user
        - name: MYSQL_PASSWORD
          value: password
      ports:
        - containerPort: 3306
    - name: nginx
      image: nginx
      ports:
        - containerPort: 80
EOT
```

```bash
kubectl apply -f ./pod.yaml
```

this will deploy a pod with two containers.
one container running mariadb and another container running nginx.
these containers will always be co-located and co-scheduled.
one can communicate with another by using `localhost`.

## delete pod

```bash
kubectl delete -f ./pod.yaml
```

deletes resources in the manifest.
