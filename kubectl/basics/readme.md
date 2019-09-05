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

run the following command to check install

```bash
kubectl version
```

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



## working with multiple clusters

