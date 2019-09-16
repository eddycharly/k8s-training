# k8s-training / k8s-resources / services

in this training we'll cover kubernetes `services`.

this is a rather complex beast, services in kubernetes comes in several flavors (ClusterIP, NodePort, LoadBalancer, Headless).
there are also several implementations given the kubernetes version and the platform it is run on.
every implementation has complex technical details.
we will only focus on the abstraction services provide without diving in the details.

for more information you can refer to kubernetes official documentation as a starting point.

## relevant documentation
- [kubernetes services](https://kubernetes.io/docs/concepts/services-networking/service/)

## presentation

a service is an abstract way to expose an application running on a set of pods.
the set of pods targeted by a service is usually determined by a selector.

kubernetes assigns an ip per pod, it also assigns an ip per service.
when pods are added or retired from the pod group behind the service, this should be transparent as the ip of the service itself doesn't change.

the service object acts as a proxy between the client and the backend pods.
all requests will be distributed randomly through the available backends.

additionnaly, kubernetes has a built-in service discovery mechanism based on dns that can be used by pods running inside the cluster.
for accessing services from outside the cluster, kubernetes offers ways to place a network port or load balancer in between your application and the backend pods.

## kubectl get services

```bash
kubectl get services --all-namespaces
```

shows the list of services that exist inside a cluster.

flag `--all-namespaces` allows to list all services regardless of the namespace they are bound to.

```bash
NAMESPACE     NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)         AGE
default       kubernetes   ClusterIP   10.152.183.1    <none>        443/TCP         77m
kube-system   kube-dns     ClusterIP   10.152.183.10   <none>        53/UDP,53/TCP   77m
```

## deploy manifest with a ClusterIP service

```bash
cat <<EOT > svc-training.yaml
apiVersion: v1
kind: Pod
metadata:
  name: svc-training
  namespace: scratch
  labels:
    app: nginx
spec:
  containers:
    - name: nginx
      image: nginx
      ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: svc-training
  namespace: scratch
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
EOT
```

```bash
kubectl apply -f ./svc-training.yaml
```

this will deploy a pod running an nginx container, and a service in front of it.

notice how the target pods are selected using the selector section in the service.
pods have been labeled with a `app=nginx` tag, the service uses this label to match the pods that it will proxy requests to.

this type of service will be accessible only inside the cluster.

## run an alpine container in the cluster

```bash
kubectl run -it alpine --image=alpine -- sh
```

this will run an alpine container in a cluster in interactive mode.

```bash
apk update
apk add curl

curl http://svc-training.scratch
```

this will install curl in the alipne container and will call the url http://svc-training.scratch.
it should output the content of the nginx welcome page.

this illustrates the fact that the service we created was assigned a dns record.
the dns name assigned is in the form `<service name>.<namespace name>[.svc.cluster.local]`.

## deploy manifest with a NodePort service

```bash
cat <<EOT > svc-training.yaml
apiVersion: v1
kind: Pod
metadata:
  name: svc-training
  namespace: scratch
  labels:
    app: nginx
spec:
  containers:
    - name: nginx
      image: nginx
      ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: svc-training
  namespace: scratch
spec:
  type: NodePort
  selector:
    app: nginx
  ports:
    - protocol: TCP
      nodePort: 30100
      port: 8080
      targetPort: 80
EOT
```

```bash
kubectl apply -f ./svc-training.yaml
```

this will deploy a pod running an nginx container, and a service in front of it.

this type of service will map a port on the host (30100) to the service port (8080).
the service will forward trafic on the container port (80).

now you can directly curl the multipass machine from your computer.

```bash
curl http://192.168.64.2:30100
```
