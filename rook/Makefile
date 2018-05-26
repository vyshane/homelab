
prerequisites:
	# Give Tiller additional permissions in RBAC-enabled cluster
	kubectl --namespace kube-system create sa tiller
	kubectl create clusterrolebinding tiller --clusterrole cluster-admin --serviceaccount=kube-system:tiller
	kubectl --namespace kube-system patch deploy/tiller-deploy -p '{"spec": {"template": {"spec": {"serviceAccountName": "tiller"}}}}'
	# We'll install Rook operator from alpha channel
	helm repo add rook-alpha https://charts.rook.io/alpha

install:
	helm install rook-alpha/rook \
		--name rook \
		--namespace rook-system

createcephcluster:
	kubectl create -f ceph-cluster.yaml

uninstall-ihavealreadyremovedvolumes:
	# Delete CRD
	kubectl delete rook rook-ceph -n rook-ceph
	# Remove Ceph cluster namespace
	kubectl delete ns rook-ceph
	# Remove operator
	helm delete --purge rook
	# Remove operator namespace
	kubectl delete ns rook-system
	@echo Remove the dataDirHostPath /var/lib/rook directory on each node
