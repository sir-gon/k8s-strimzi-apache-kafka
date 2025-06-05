# k8s-strimzi-apache-kafka

Install Strimzi operator:

```bash
kubectl create namespace kafka
kubectl create namespace dev
kubectl create namespace uat
kubectl create namespace prod

kubectl create -f strimzi-operator.yml --namespace=kafka
kubectl create -f strimzi-operator-role-bindings.yml
```

## DESTROY

```bash

kubectl -n kafka delete $(kubectl get strimzi -o name -n kafka)
kubectl -n dev delete $(kubectl get strimzi -o name -n dev)
kubectl -n uat delete $(kubectl get strimzi -o name -n uat)
kubectl -n prod delete $(kubectl get strimzi -o name -n prod)

kubectl delete -f strimzi-operator.yml --namespace=kafka
kubectl delete -f strimzi-operator-role-bindings.yml
```
