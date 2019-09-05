# k8s-training / kubectl / basics

in this training we're going to cover kubectl basics.

we will go through:
- kubectl installation
- kubectl configuration
- working with multiple clusters
- basic commands

## kubectl installation

the installation procedure is described [here](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

### linux

```bash
curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
```

given the distribution you're using you can also install using a package manager (apt-get, yum, snap), please refer to the [official documentation](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

### mac

```bash
curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/darwin/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
```

you can also install using a package manager (brew, port), please refer to the [official documentation](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

### windows

refer to the official documentation

## check installation

execute the following command to check install

```bash
kubectl version
```

if the command executes, install was successfull, even if errors appear.
we will have to configure `kubectl` before it order to get it to work.

## kubectl bash auto completion

the installation procedure is described [here](https://kubernetes.io/docs/tasks/tools/install-kubectl/#optional-kubectl-configurations).

### linux

refer to the [official documentation](https://kubernetes.io/docs/tasks/tools/install-kubectl/#optional-kubectl-configurations)

## mac

```bash
brew install bash-completion
kubectl completion bash > $(brew --prefix)/etc/bash_completion.d/kubectl
chmod +x /usr/local/etc/bash_completion.d/kubectl
```

and add the following line in your `.bash_profile`

```bash
[[ -r "/usr/local/etc/profile.d/bash_completion.sh" ]] && . "/usr/local/etc/profile.d/bash_completion.sh"
```

then restart your terminal

### windows

refer to the [official documentation](https://kubernetes.io/docs/tasks/tools/install-kubectl/#optional-kubectl-configurations)

## kubectl configuration

in order to connect to a kubernetes cluster, `kubectl` requires a configuration file.

the default location for the configuration file is `$HOME/.kube/config`, but it can be overriden, or split into several files (usefull when working with multiple clusters).

### kubectl configuration file example

the layout of the configuration file is as follows.

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
current-context: k8s.example.com
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

