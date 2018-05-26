#VERSION?=0.8.0

install: clean get
	helm install workspace/istio/install/kubernetes/helm/istio \
		--name istio \
		--namespace istio-system \
		--set sidecar-injector.enabled=true \
		--set global.proxy.image=proxyv2 \
		--set global.hub=docker.io/istionightly \
		--set global.tag=circleci-nightly \
		--set ingressgateway.service.type=NodePort

upgrade: clean get
	helm upgrade istio \
		workspace/istio/install/kubernetes/helm/istio \
		--namespace istio-system \
		--set sidecar-injector.enabled=true \
		--set global.proxy.image=proxyv2 \
		--set global.hub=docker.io/istionightly \
		--set global.tag=circleci-nightly \
		--set ingressgateway.service.type=NodePort

uninstall:
	helm delete --purge istio
	@echo Note that the istio-system Kubernetes namespace was not deleted

get:
	#wget -P workspace/ https://github.com/istio/istio/releases/download/$(VERSION)/istio-$(VERSION)-linux.tar.gz
	#tar -xzf workspace/istio-$(VERSION)-linux.tar.gz -C workspace/
	#mv workspace/istio-$(VERSION) workspace/istio
	mkdir workspace
	git clone -b release-0.8 --single-branch git@github.com:istio/istio.git --depth 1 workspace/istio

clean:
	rm -rf workspace/
