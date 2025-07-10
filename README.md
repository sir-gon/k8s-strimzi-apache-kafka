# k8s-strimzi-apache-kafka

## Pre-requirements

Give enough resources to minikube

```bash
minikube config set memory 8192
minikube config set cpus 4
```

Start Minikube

```bash
minikube start --kubernetes-version=v1.32.1
minikube dashboard &
```

## Strimzi

Install Strimzi operator:

```bash
kubectl create namespace kafka
kubectl create namespace dev
kubectl create namespace uat
kubectl create namespace prod
```

Minikube requiere some fixes about RBAC to run strimzi operator

```bash
kubectl apply -f strimzi-minikube-rbac.yml
```

Install Strimzi operator

```bash
kubectl create -f strimzi-operator.yml --namespace=kafka
```

## Kafka Clusters

Create clusters

```bash
kubectl apply -f kafka-cluster-tatooine.yml --namespace=dev
kubectl apply -f kafka-cluster-naboo.yml --namespace=uat
kubectl apply -f kafka-cluster-coruscant.yml --namespace=prod
```

Create Kafka UI

```bash
kubectl apply -f kafka-ui.yml --namespace=kafka
```

Expose Kafka UI

```bash
minikube service kafka-ui --namespace=kafka kafka --url &
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
