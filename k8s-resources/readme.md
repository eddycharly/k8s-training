# k8s-training / k8s-resources

`kubernetes` defines resources that we use to deploy and manage workloads inside a cluster.

there are several ways to instruct `kubernetes` about the actions we want to apply on the resources of a cluster.
in this training we will cover the use of `kubectl` to manage cluster resources.

`kubectl` can be used to manage cluster resources in two different flavors : imperative and declarative.
while imperative can be useful sometimes, we will mostly work with declarative aproach as it's more modern and easyer to follow.

all the trainings below assume you have a running `microk8s` cluster.

open a new terminal and set the `KUBECONFIG` environment variable to point to your `microk8s` config file.

```bash
export KUBECONFIG=$HOME/.kube/microk8s
```

## Topics

- [namespaces](namespaces/readme.md)
- [pods](pods/readme.md)
