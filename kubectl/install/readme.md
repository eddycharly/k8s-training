# k8s-training / kubectl / install

in this training we'll cover kubectl install.

## documentation
- [kubectl installation procedure](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
- [kubectl shell autocompletion](https://kubernetes.io/docs/tasks/tools/install-kubectl/#optional-kubectl-configurations)

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
