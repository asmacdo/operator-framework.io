---
title: "How Page"
menu:
    main:
        name: How
        weight: 340
---

### Summary

This section provides a hands-on concise guide to show how to:

TODO why can i not used ul with - layout problem?
1. use the Operator SDK to scaffold a simple Golang-based operator
1. use the operator locally
1. deploy the operator to a cluster
1. package and deploy with OLM

### Prerequisites

To use the commands in this guide:

1. [Install the Operator
   SDK](https://sdk.operatorframework.io/docs/install-operator-sdk/)

### Scaffold with the Operator SDK

The following commands scaffold out a minimal working operator. For a
more in depth explanation of each step, see
the [Golang quickstart](https://sdk.operatorframework.io/docs/golang/quickstart/)
and the [SDK CLI Reference docs](https://sdk.operatorframework.io/docs/cli/).

``` bash
$ operator-sdk new memcached-operator --repo=github.com/example-inc/memcached-operator
$ cd memcached-operator
$ operator-sdk add api --api-version=cache.example.com/v1alpha1 --kind=Memcached
$ operator-sdk generate k8s
$ operator-sdk generate crds
$ operator-sdk add controller --api-version=cache.example.com/v1alpha1 --kind=Memcached
```

Prepare the cluster for the new object type by creating the CRD. 

``` bash
$ kubectl create -f deploy/crds/cache.example.com_memcacheds_crd.yaml
```

### Run operator locally

For development and testing, the operator can run locally against a
cluster. By default, the Operator SDK uses the kubeconfig in `$HOME/.kube/config`.

``` bash
operator-sdk run --local --watch-namespace=default
```

Now the operator is running, but has nothing to do, because there are no
Memcached objects on the cluster.

In a separate terminal session, create a Memcached object, and the operator will do the rest.

``` bash
kubectl apply -f deploy/crds/cache.example.com_v1alpha1_memcached_cr.yaml
```

Now `kubectl get pods` will show 1 `example-memcached-pod`.

### Cluster deployment

Once the operator works locally, it is ready to be deployed to the
cluster. The following commands will build and push the operator image.

Set MY_CONTAINER_IMAGE to `<registry>/<namespace>/<repository>:<tag>`
``` bash
$ MY_CONTAINER_REPO="quay.io/example/memcached-operator:v0.0.1"
$ operator-sdk build $MY_CONTAINER_REPO
$ sed -i "s|REPLACE_IMAGE|$MY_CONTAINER_REPO|g" deploy/operator.yaml
$ docker push $MY_CONTAINER_REPO
```

**NOTE:** Make sure the repository is publicly accessible before proceeding!

Create the prerequisite objects: 

```bash
$ kubectl create -f deploy/service_account.yaml
$ kubectl create -f deploy/role.yaml
$ kubectl create -f deploy/role_binding.yaml
```

Create the operator deployment.

``` bash
$ kubectl create -f deploy/operator.yaml
```

Finally, create a Memcached CR if it doesn't already exist.

``` bash
kubectl apply -f deploy/crds/cache.example.com_v1alpha1_memcached_cr.yaml
```

### Package the operator



very simple Golang-based operator,
answer for another FAQDoc requirements:
  - copy/pastable
  - concise
  - top-level docs entrypoint: lots of links


The Operator Framework is designed to help you quickly create and
manage operators. At a high level, this is the workflow

Section 1: Creating an Operator
- Create an operator
- build and run local

Section 2: packaging and installing operators
- package an operator
- OLM installation on external cluster
- build and run an operator bundle on OLM enabled cluster

Section 3: publish and use the catalog
- publish bundle in OLM catalog
- deploy catalog to OLM and run the operator
