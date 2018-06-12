# Memcached Operator

[![](https://images.microbadger.com/badges/image/ianmlewis/memcached-operator:v0.0.2.svg)](https://hub.docker.com/r/ianmlewis/memcached-operator/ "memcached-operator on Docker hub") [![Go Report Card](https://goreportcard.com/badge/github.com/ianlewis/memcached-operator)](https://goreportcard.com/report/github.com/ianlewis/memcached-operator) [![](https://travis-ci.org/ianlewis/memcached-operator.svg?branch=master)](https://travis-ci.org/ianlewis/memcached-operator)

memcached-operator is a Kubernetes [Operator](https://coreos.com/blog/introducing-operators.html) for deploying and managing a cluster of [Memcached](https://memcached.org/) instances.

memcached-operator provides a single Service endpoint that memcached client applications can connect to to make use of the memcached cluster. It provides this via a memcached proxy which is automatically updated whenever memcached instances are added or removed from the cluster.

memcached-operator supports sharded and replicated pools of servers as well as combinations of both strategies.

![diagram](docs/design.png)

See the [documentation](docs/README.md) for more information.

## Project Status

**Project status:** *alpha* 

memcached-operator is still under active development and has not been extensively tested yet. Use at your own risk. Backward-compatibility is not supported for alpha releases.

## Prerequisites

* Version >= 1.10 of Kubernetes.

memcached-operator relies on [subresources for CRDs](https://github.com/kubernetes/kubernetes/pull/55168) which is in 1.10+

## Quickstart

You can install the memcached-operator using the included helm chart. Check out the git repository and run this in the root directory.

    $ helm install --name memcached-operator charts/memcached-operator

The easiest way to create a memcached cluster is using the [memcached helm chart](https://github.com/kubernetes/charts/tree/master/stable/memcached):

    $ helm install --name sharded stable/memcached

You can then create a memcached proxy to connect to the cluster.

[embedmd]:# (docs/sharded-example.yaml yaml /apiVersion/ $)
```yaml
apiVersion: ianlewis.org/v1alpha1
kind: MemcachedProxy
metadata:
  name: sharded-example
spec:
  rules:
    type: "sharded"
    service:
      name: "sharded-memcached"
      port: 11211
```

    $ kubectl apply -f docs/sharded-example.yaml

You can then access your memcached cluster via the`sharded-memcached`service. Check the [documentation](docs/README.md) for more information.

## Removal

You can remove the memcached-operator by deleting the helm release.

    $ helm delete --purge memcached-operator

## Development

Check out memcached-operator to your `GOPATH`

### Building

memcached-operator can be built using the normal Go build tools. This will build a binary dynamically linked to glibc.

    $ go build

You can build a fully statically linked binary as well:

    $ make build

[//]: # (TODO: Include dependencies for running tests for vendored libraries in vendor)
[//]: # (TODO: Create end-to-end tests and instructions)

# Similar projects

- [mcrouter-kubernetes-provisioner](https://github.com/mlaccetti/mcrouter-kubernetes-provisioner)
- [KubeStack memcached operator](https://github.com/kbst/memcached)
- [KubeDB memcached operator](https://github.com/kubedb/memcached)

## Disclaimers

This is not an official Google product
