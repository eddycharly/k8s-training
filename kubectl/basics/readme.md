# k8s-training / kubectl / basics

in this training we're going to cover kubectl basics.

we will go through:
- kubectl installation
- kubectl configuration
- working with multiple clusters
- basic commands

## documentation
- [kubectl installation procedure](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
- [kubectl shell autocompletion](https://kubernetes.io/docs/tasks/tools/install-kubectl/#optional-kubectl-configurations)
- [kubectl configuration](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/)

## kubectl installation on linux

```bash
curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
```

given the distribution you're using you can also install using a package manager (apt-get, yum, snap), please refer to the [official documentation](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

## kubectl installation on mac

```bash
curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/darwin/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
```

you can also install using a package manager (brew, port), please refer to the [official documentation](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

## kubectl installation on windows

refer to the official documentation

## check installation

execute the following command to check install

```bash
kubectl version
```

if the command executes, install was successfull, even if errors apear.
we will have to configure `kubectl` before it order to get it to work.

## kubectl bash auto completion on linux

refer to the [official documentation](https://kubernetes.io/docs/tasks/tools/install-kubectl/#optional-kubectl-configurations)

## kubectl bash auto completion on mac

```bash
brew install bash-completion
kubectl completion bash > $(brew --prefix)/etc/bash_completion.d/kubectl
chmod +x /usr/local/etc/bash_completion.d/kubectl
```

add the following line in your `.bash_profile`

```bash
[[ -r "/usr/local/etc/profile.d/bash_completion.sh" ]] && . "/usr/local/etc/profile.d/bash_completion.sh"
```

restart your terminal

### windows

refer to the [official documentation](https://kubernetes.io/docs/tasks/tools/install-kubectl/#optional-kubectl-configurations)

## kubectl config set-cluster

```bash
CONFIG_FILE=$HOME/.kube/cluster1
CLUSTER_NAME=cluster1
CLUSTER_SERVER=https://cluster-1.com
CLUSTER_CA_DATA=toto
kubectl config --kubeconfig=${CONFIG_FILE} set-cluster ${CLUSTER_NAME} --server=${CLUSTER_SERVER}
kubectl config --kubeconfig=${CONFIG_FILE} set clusters.${CLUSTER_NAME}.certificate-authority-data $(echo ${CLUSTER_CA_DATA} | base64)
cat $CONFIG_FILE
```

replace `CLUSTER_NAME`, `CLUSTER_SERVER` and `CLUSTER_CA_DATA` with your own cluster values.

```bash
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: dG90bwo=
    server: https://cluster-1.com
  name: cluster1
contexts: []
current-context: ""
kind: Config
preferences: {}
users: []
```

## kubectl config set-user

```bash
CONFIG_FILE=$HOME/.kube/cluster1
USER_NAME=user1
USER_TOKEN=token1
kubectl config --kubeconfig=${CONFIG_FILE} set-credentials ${USER_NAME} --token ${USER_TOKEN}
cat $CONFIG_FILE
```

replace `USER_NAME` and `USER_TOKEN` with your own user values.

```bash
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: dG90bwo=
    server: https://cluster-1.com
  name: cluster1
contexts: []
current-context: ""
kind: Config
preferences: {}
users:
- name: user1
  user:
    token: token1
```

## kubectl configuration default location

```bash
ls -al $HOME/.kube/
```

the default location for the kubectl configuration file is `$HOME/.kube/config`.

```bash
total 136
drwxr-xr-x    8 charlesbreteche  staff    256  6 sep 10:05 .
drwxr-xr-x+  66 charlesbreteche  staff   2112  5 sep 14:03 ..
drwxr-xr-x    3 charlesbreteche  staff     96 10 mai 11:30 cache
-rw-------@   1 charlesbreteche  staff  52330  5 sep 15:22 config
```

### kubectl configuration file example

```yaml
apiVersion: v1
kind: Config
preferences: {}
clusters:
- name: k8s.example.com
  cluster:
    certificate-authority-data: ...
    server: https://k8s.example.com
contexts:
- name: k8s.example.com
  context:
    cluster: k8s.example.com
    user: k8s.example.com
users:
- name: k8s.example.com
  user:
    token: ...
```

it mainly consists of a three sections:
- clusters, defining the clusters
- users, defining the users
- contexts, used to establish relationship between clusters and users

### view configuration

execute command:

```bash
kubectl config view
```

outputs the configuration loaded by kubectl.

you can use this command to verify the config loaded by kubectl, usefull when trying to troubleshoot configuration problems.

### view configuration contexts

execute command:

```bash
kubectl config get-contexts
```

outputs the available contexts.

```bash
CURRENT   NAME                             CLUSTER                                AUTHINFO                               NAMESPACE
*         k8s.dev.agriconomie.com          k8s.dev.agriconomie.com                k8s.dev.agriconomie.com-token          
          k8s.dev.axereal-appro.com        k8s.dev.axereal-appro.com              k8s.dev.axereal-appro.com-token        
          k8s.dev.piecesmoinscheres.com    k8s.dev.piecesmoinscheres.com          k8s.dev.piecesmoinscheres.com-token    
          k8s.prod.axereal-appro.com       k8s.prod.axereal-appro.com             k8s.prod.axereal-appro.com-token       
          k8s.prod.piecesmoinscheres.com   k8s.prod.piecesmoinscheres.com         k8s.prod.piecesmoinscheres.com-token   
          k8s.shared.agrico.tech           k8s.shared.agrico.tech                 k8s.shared.agrico.tech-token           
```

the current context is indicated the `*` sign in the `CURRENT` column.

### change configuration file location

although `$HOME/.kube/config` is the default configuration file location, you can put your configuration files wherever you want.

to let kubectl know wich files to load, you will need to define the `KUBECONFIG` environment variable.

execute command:

```bash
cp $HOME/.kube/config /tmp/cfg-test

export KUBECONFIG=/tmp/cfg-test

kubectl config get-contexts

rm /tmp/cfg-test
```

outputs the available contexts (using the configuration file `/tmp/cfg-test`).

the `KUBECONFIG` environment variable should be set in your `.bash_profile` so that it is initialized automatically every time a shell is opened.

### verify kubectl is working

once you have a configuration file, exectue commands:

```bash
kubectl cluster-info
```

```bash
kubectl cluster-info dump
```

both commands should connect to cluster and print results in the console.

## working with multiple clusters

the official documentation can be found [here](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/)

### create two configuration files

create `$HOME/.kube/cluster-1` and `$HOME/.kube/cluster-2` confiduration files.

```yaml
apiVersion: v1
kind: Config
preferences: {}
clusters:
- name: cluster-1
  cluster:
    certificate-authority-data: dG90bwo=
    server: https://cluster-1.com
contexts:
- name: cluster-1
  context:
    cluster: cluster-1
    user: cluster-1
users:
- name: cluster-1
  user:
    token: ...
```

```yaml
apiVersion: v1
kind: Config
preferences: {}
clusters:
- name: cluster-2
  cluster:
    certificate-authority-data: dG90bwo=
    server: https://cluster-2.com
contexts:
- name: cluster-2
  context:
    cluster: cluster-2
    user: cluster-2
users:
- name: cluster-2
  user:
    token: ...
```

### set the KUBECONFIG environment variable

```bash
export KUBECONFIG=$HOME/.kube/cluster-1:$HOME/.kube/cluster-2
```

notice that each configuration file is separated by `:`.

### verifiy that both files were loaded by kubectl

```bash
kubectl config view
kubectl config get-contexts
```

clusters, users and contexts from both files should be listed.

```yaml
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://cluster-1.com
  name: cluster-1
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://cluster-2.com
  name: cluster-2
contexts:
- context:
    cluster: cluster-1
    user: cluster-1
  name: cluster-1
- context:
    cluster: cluster-2
    user: cluster-2
  name: cluster-2
current-context: ""
kind: Config
preferences: {}
users:
- name: cluster-1
  user:
    token: '...'
- name: cluster-2
  user:
    token: '...'
```

```bash
CURRENT   NAME        CLUSTER     AUTHINFO    NAMESPACE
          cluster-1   cluster-1   cluster-1   
          cluster-2   cluster-2   cluster-2   
```

### switch between contexts

```bash
kubectl config use-context cluster-1
kubectl config get-contexts
```

the cluster-1 context should be the current context.

```bash
CURRENT   NAME        CLUSTER     AUTHINFO    NAMESPACE
*         cluster-1   cluster-1   cluster-1   
          cluster-2   cluster-2   cluster-2   
```

```bash
kubectl config use-context cluster-2
kubectl config get-contexts
```

the cluster-2 context should be the current context.

```bash
CURRENT   NAME        CLUSTER     AUTHINFO    NAMESPACE
          cluster-1   cluster-1   cluster-1   
*         cluster-2   cluster-2   cluster-2   
```
