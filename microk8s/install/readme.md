# k8s-training / microk8s / install

in this training we'll cover `microk8s` install.

in order to run, `microk8s` requires a linux os, that's why we will install a virtual machine when running on mac or windows.

additionnaly, `microk8s` is distributed through snap so we may have to install snap locally.

## relevant documentation
- [microk8s user guide](https://microk8s.io/docs/)
- [microk8s on macos](https://itnext.io/microk8s-on-macos-98f1de3aa63e)

## install multipass

`multipass` let's you run an ubuntu virtual machine easily.

if trying to install `microk8s`on macos or windows, you will to install `multipass`.

if trying to install `microk8s`on linux, you can try to install directly on your local machine but it's not guaranteed to work out of the box.

dowload and install `multipass` from the [official multipass website](https://multipass.run/#install).

## create an ubuntu virtual machine with multipass

```bash
multipass launch --name microk8s-vm --cpus 2 --mem 4G --disk 40G
```

this will download an ubuntu image locally and start a virtual machine named microk8s-vm, running ubuntu with 4 gigabytes memory and 40 gigabytes disk.

## multipass list

```bash
multipass list
```

list `multipass` virtual machines.

```bash
Name                    State             IPv4             Image
microk8s-vm             Running           192.168.64.2     Ubuntu 18.04 LTS
```

## multipass shell

```bash
multipass shell microk8s-vm
```

opens a shell inside a virtual machine.

## multipass stop

```bash
multipass stop microk8s-vm
```

shutdowns a virtual machine.

## multipass delete

```bash
multipass delete microk8s-vm
multipass purge
```

deletes a virtual machine.

## install microk8s on the multipass virtual machine

```bash
multipass exec microk8s-vm -- sudo snap install microk8s --classic --channel=1.13/stable
multipass exec microk8s-vm -- sudo iptables -P FORWARD ACCEPT
```

this will install `microk8s` locally in the virtual machine and open the necessary firewall rules to communicate with `microk8s`.

## check kubernetes is running

```bash
multipass list
```

to get the ip address of the `multipass` virtual machine running `microk8s`.

```bash
Name                    State             IPv4             Image
microk8s-vm             Running           192.168.64.2     Ubuntu 18.04 LTS
```

browse http://192.168.64.2:8080/api.

this should display something like this :

```json
{
  "kind": "APIVersions",
  "versions": [
    "v1"
  ],
  "serverAddressByClientCIDRs": [
    {
      "clientCIDR": "0.0.0.0/0",
      "serverAddress": "192.168.64.2:6443"
    }
  ]
}
```

## kubectl configuration to access microk8s

```bash
multipass exec microk8s-vm -- /snap/bin/microk8s.config > $HOME/.kube/microk8s
kubectl --kubeconfig=$HOME/.kube/microk8s get all --all-namespaces
```

this will create a `kubectl` configuration file for your `microk8s` instance running in the ubuntu virtual machine.

you can add `$HOME/.kube/microk8s` to the `KUBECONFIG` environment variable so that kubectl automatically loads the configuration.

```bash
NAMESPACE   NAME                 TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE
default     service/kubernetes   ClusterIP   10.152.183.1   <none>        443/TCP   78m
```

## microk8s addons

```bash
multipass exec microk8s-vm -- /snap/bin/microk8s.enable dns dashboard storage ingress
```

this wil enable the dns, dashboard, storage and ingress add-ons.

the list of available add-ons is detailed [here](https://microk8s.io/docs/#list-of-available-add-ons).

use `microk8s.disable <add-on>` to disable an add-on.

```bash
Enabling DNS
Applying manifest
service/kube-dns created
serviceaccount/kube-dns created
configmap/kube-dns created
deployment.extensions/kube-dns created
Restarting kubelet
DNS is enabled
Enabling dashboard
secret/kubernetes-dashboard-certs created
serviceaccount/kubernetes-dashboard created
deployment.apps/kubernetes-dashboard created
service/kubernetes-dashboard created
service/monitoring-grafana created
service/monitoring-influxdb created
service/heapster created
deployment.extensions/monitoring-influxdb-grafana-v4 created
serviceaccount/heapster created
configmap/heapster-config created
configmap/eventer-config created
deployment.extensions/heapster-v1.5.2 created
dashboard enabled
Enabling default storage class
deployment.extensions/hostpath-provisioner created
storageclass.storage.k8s.io/microk8s-hostpath created
Storage will be available soon
Enabling Ingress
deployment.extensions/default-http-backend created
service/default-http-backend created
serviceaccount/nginx-ingress-microk8s-serviceaccount created
configmap/nginx-load-balancer-microk8s-conf created
daemonset.apps/nginx-ingress-microk8s-controller created
Ingress is enabled
```

## connect to the dashboard

```bash
ACCOUNT=$(kubectl --kubeconfig=$HOME/.kube/microk8s get serviceaccount kubernetes-dashboard -n kube-system -o jsonpath='{.secrets[0].name}')
TOKEN=$(kubectl --kubeconfig=$HOME/.kube/microk8s get secret $ACCOUNT -n kube-system -o jsonpath='{.data.token}' | base64 --decode)
kubectl --kubeconfig=$HOME/.kube/microk8s config set-credentials dashboard --token ${TOKEN}
kubectl --kubeconfig=$HOME/.kube/microk8s config set-context dashboard --cluster=microk8s-cluster --user=dashboard
```

this will create a user and a context to connect to the dasboard in the `$HOME/.kube/microk8s` configuration file.

```bash
kubectl --kubeconfig=$HOME/.kube/microk8s config use-context dashboard
kubectl --kubeconfig=$HOME/.kube/microk8s proxy
```

starts the `kubectl` proxy.

browse http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/#!/login to open the dashboard.

select the `$HOME/.kube/microk8s` configuration file and click the `SIGN IN` button.

## stop/start microk8s

```bash
multipass exec microk8s-vm -- sudo microk8s.stop
multipass exec microk8s-vm -- sudo microk8s.start
```

this will stop/start `microk8s`.

the underlying virtual machine is still running though, you need to use `multipass` commands to start/stop the `multipass` virtual machine.

## remove microk8s

```bash
multipass exec microk8s-vm -- sudo microk8s.reset
multipass exec microk8s-vm -- sudo snap remove microk8s
```

this will stop all pods running and remove `microk8s`.

the underlying virtual machine is still running though, you need to use `multipass` commands to start/stop the `multipass` virtual machine.

http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/#!/login