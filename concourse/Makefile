# Concourse Deployment to Kubernetes

install:
	helm install stable/concourse \
		--name concourse \
		--namespace concourse \
		--set worker.replicas=3

upgrade:
	helm upgrade concourse \
		stable/concourse \
		--namespace concourse \
		--set worker.replicas=3

uninstall:
	helm delete --purge concourse
	kubectl delete pvc -l app=concourse-worker --namespace=concourse
	@echo Note that the concourse Kubernetes namespace was not deleted

