VERSION?=0.8.0

install: clean get
	helm install workspace/istio/install/kubernetes/helm/istio \
		--name istio \
		--namespace istio-system \
		--set ingressgateway.service.type=NodePort

upgrade: clean get
	helm upgrade istio \
		workspace/istio/install/kubernetes/helm/istio \
		--namespace istio-system \
		--set ingressgateway.service.type=NodePort

uninstall:
	helm delete --purge istio
	@echo Note that the istio-system Kubernetes namespace was not deleted

get:
	wget -P workspace/ https://github.com/istio/istio/releases/download/$(VERSION)/istio-$(VERSION)-linux.tar.gz
	tar -xzf workspace/istio-$(VERSION)-linux.tar.gz -C workspace/
	mv workspace/istio-$(VERSION) workspace/istio

clean:
	rm -rf workspace/
