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

refer to the [official documentation](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

## check installation

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

## windows

refer to the [official documentation](https://kubernetes.io/docs/tasks/tools/install-kubectl/#optional-kubectl-configurations)

## kubectl configuration file default location

```bash
ls -al $HOME/.kube/
```

the default location for the kubectl configuration file is `$HOME/.kube/config`.

if the `$HOME/.kube/config` file doesn't exist it probably means that you didn't configure any cluster yet.

```bash
total 136
drwxr-xr-x    8 charlesbreteche  staff    256  6 sep 10:05 .
drwxr-xr-x+  66 charlesbreteche  staff   2112  5 sep 14:03 ..
drwxr-xr-x    3 charlesbreteche  staff     96 10 mai 11:30 cache
-rw-------@   1 charlesbreteche  staff  52330  5 sep 15:22 config
```

## check kubectl configuration files

```bash
echo ${KUBECONFIG}
```

if the `KUBECONFIG` environment variable doesn't exist, the default `$HOME/.kube/config` file is used.

multiple configuration files path are separated by `:`.

```bash
/Users/charlesbreteche/.kube/cluster1:/Users/charlesbreteche/.kube/cluster2
```

## set first kubectl configuration file

```bash
export KUBECONFIG=$HOME/.kube/cluster1
echo ${KUBECONFIG}
```

this adds `$HOME/.kube/cluster1` file to kubectl.

the `KUBECONFIG` environment variable should be set in `.bash_profile` so that it is initialized automatically every time a shell is opened.

```bash
/Users/charlesbreteche/.kube/cluster1
```

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

replace `CLUSTER_NAME`, `CLUSTER_SERVER` and `CLUSTER_CA_DATA` with your own cluster values.

```yaml
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

replace `USER_NAME` and `USER_TOKEN` with your own user values.

```yaml
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

## kubectl config set-context

```bash
CONFIG_FILE=$HOME/.kube/cluster1
CONTEXT_NAME=context1
CONTEXT_CLUSTER=cluster1
CONTEXT_USER=user1

kubectl config --kubeconfig=${CONFIG_FILE} set-context ${CONTEXT_NAME} --cluster=${CONTEXT_CLUSTER} --user=${CONTEXT_USER}

cat $CONFIG_FILE
```

replace `CONTEXT_NAME`, `CONTEXT_CLUSTER` and `CONTEXT_USER` with your own context values.

```yaml
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: dG90bwo=
    server: https://cluster-1.com
  name: cluster1
contexts:
- context:
    cluster: cluster1
    user: user1
  name: context1
current-context: ""
kind: Config
preferences: {}
users:
- name: user1
  user:
    token: token1
```

## view configuration

```bash
kubectl config view
```

outputs the configuration loaded by kubectl.

when the configuration is loaded from multiple files, all files are merged together to produce the final configuration.

```yaml
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://cluster-1.com
  name: cluster1
contexts:
- context:
    cluster: cluster1
    user: user1
  name: context1
current-context: ""
kind: Config
preferences: {}
users:
- name: user1
  user:
    token: token1
```

## view configuration contexts

```bash
kubectl config get-contexts
```

outputs the available contexts.

```bash
CURRENT   NAME       CLUSTER    AUTHINFO   NAMESPACE
          context1   cluster1   user1      
```

## set current context

```bash
kubectl config use-context context1
kubectl config get-contexts
```

the current context is set to `context1`, notice the `*` in the `CURRENT` column.

```bash
CURRENT   NAME       CLUSTER    AUTHINFO   NAMESPACE
*         context1   cluster1   user1      
```

## create a second configuration file

```bash
CONFIG_FILE=$HOME/.kube/cluster2
CLUSTER_NAME=cluster2
CLUSTER_SERVER=https://cluster-2.com
CLUSTER_CA_DATA=toto
USER_NAME=user2
USER_TOKEN=token2
CONTEXT_NAME=context2
CONTEXT_CLUSTER=cluster2
CONTEXT_USER=user2

kubectl config --kubeconfig=${CONFIG_FILE} set-cluster ${CLUSTER_NAME} --server=${CLUSTER_SERVER}
kubectl config --kubeconfig=${CONFIG_FILE} set clusters.${CLUSTER_NAME}.certificate-authority-data $(echo ${CLUSTER_CA_DATA} | base64)
kubectl config --kubeconfig=${CONFIG_FILE} set-credentials ${USER_NAME} --token ${USER_TOKEN}
kubectl config --kubeconfig=${CONFIG_FILE} set-context ${CONTEXT_NAME} --cluster=${CONTEXT_CLUSTER} --user=${CONTEXT_USER}

cat $CONFIG_FILE
```

```yaml
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: dG90bwo=
    server: https://cluster-2.com
  name: cluster2
contexts:
- context:
    cluster: cluster2
    user: user2
  name: context2
current-context: ""
kind: Config
preferences: {}
users:
- name: user2
  user:
    token: token2
```

## add the second configuration file to KUBECONFIG

```bash
export KUBECONFIG=${KUBECONFIG}:$HOME/.kube/cluster2
echo ${KUBECONFIG}
```

this will append the new `$HOME/.kube/cluster2` configuration file to the `KUBECONFIG` environment variable.

```bash
/Users/charlesbreteche/.kube/cluster1:/Users/charlesbreteche/.kube/cluster2
```

## view kubectl merged configuration

```bash
kubectl config view
```

the configuration loaded by kubectl is the result of the merge of both `$HOME/.kube/cluster1` and `$HOME/.kube/cluster2` configuration files.
```yaml
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://cluster-1.com
  name: cluster1
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://cluster-2.com
  name: cluster2
contexts:
- context:
    cluster: cluster1
    user: user1
  name: context1
- context:
    cluster: cluster2
    user: user2
  name: context2
current-context: context1
kind: Config
preferences: {}
users:
- name: user1
  user:
    token: token1
- name: user2
  user:
    token: token2
```

## check kubectl contexts

```bash
kubectl config get-contexts
```

```bash
CURRENT   NAME       CLUSTER    AUTHINFO   NAMESPACE
*         context1   cluster1   user1      
          context2   cluster2   user2      
```

## switch context

```bash
kubectl config use-context context2
kubectl config get-contexts
```

the current context has changed from context1 to context2.
```bash
CURRENT   NAME       CLUSTER    AUTHINFO   NAMESPACE
          context1   cluster1   user1      
*         context2   cluster2   user2      
```
