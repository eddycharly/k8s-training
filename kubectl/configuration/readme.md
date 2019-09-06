# k8s-training / kubectl / configuration

in this training we'll cover kubectl configuration.

## relevant documentation
- [kubectl configuration](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/)

## default configuration file location

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

kubectl config --kubeconfig=${CONFIG_FILE} set-cluster ${CLUSTER_NAME} --server=${CLUSTER_SERVER}
kubectl config --kubeconfig=${CONFIG_FILE} set clusters.${CLUSTER_NAME}.certificate-authority-data $(echo ${CLUSTER_CA_DATA} | base64)
kubectl config --kubeconfig=${CONFIG_FILE} set-credentials ${USER_NAME} --token ${USER_TOKEN}
kubectl config --kubeconfig=${CONFIG_FILE} set-context ${CONTEXT_NAME} --cluster=${CLUSTER_NAME} --user=${USER_NAME}

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

## add the second kubectl configuration file

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
