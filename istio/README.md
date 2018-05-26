# Istio

## WARNING

The official Istio Helm chart is currently broken. Don't install Istio via this README.

* Our helper [Makefile](Makefile)
* [Installation instructions](https://istio.io/docs/setup/kubernetes/helm-install.html)

## Install Istio Via Helm Chart

```sh
make install
```

### Ingress Gateway

Once Istio is installed, ingress will be available on node ports:

* 31380 for HTTP
* 31390 for HTTPS
* 31400 for TCP

## Uninstall Istio

```sh
make uninstall
```
