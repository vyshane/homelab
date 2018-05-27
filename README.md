# Homelab Kubernetes Cluster

Set up each item in the following order:

1. ✓ First install Ubuntu 16.04 on the master and worker nodes
2. ️✓ [Install Kubernetes](kubernetes/README.md)
3. ✓ [Install Ceph cluster](rook/README.md)
4. [Install Istio](istio/README.md) - Official Helm chart does not currently work
5. [Install Concourse](concourse/README.md)