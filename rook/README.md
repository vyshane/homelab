# Ceph Cluster via Rook Operator v0.7

* [Rook v0.7 Documentation](https://rook.io/docs/rook/v0.7/)

## Prerequisites

We only need to run this once before first install:

```sh
make prerequisites
```

## Deploy the Rook Operator via Helm

```sh
make install
```

## Create Ceph Cluster

```sh
make createcephcluster
```

## Uninstall

First, remove any persistent volumes that you have created that are backed by Ceph.

Then:

```sh
uninstall-ihavealreadyremovedvolumes
```
