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
kubectl apply -f kafka-cluster-tatooine.yml --namespace=dev  \
kubectl apply -f kafka-cluster-naboo.yml --namespace=uat \
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

Expose (minikube) Kafka clusters

Using cluster exposition as Service (type: NodePort)

```bash
echo 'Port forwarding (dev: tatooine) ports... ' \
& kubectl port-forward svc/tatooine-dual-role-external-0 31090:9094 -n dev \
& kubectl port-forward svc/tatooine-dual-role-external-1 31091:9094 -n dev \
& kubectl port-forward svc/tatooine-dual-role-external-2 31092:9094 -n dev
```

```bash
echo 'Port forwarding (uat: naboo) ports... ' \
& kubectl port-forward svc/naboo-dual-role-external-0 31080:9095 -n uat \
& kubectl port-forward svc/naboo-dual-role-external-1 31081:9095 -n uat \
& kubectl port-forward svc/naboo-dual-role-external-2 31082:9095 -n uat
```

```bash
echo 'Port forwarding (prod: coruscant) ports... ' \
& kubectl port-forward svc/coruscant-dual-role-external-0 31070:9096 -n prod \
& kubectl port-forward svc/coruscant-dual-role-external-1 31071:9096 -n prod \
& kubectl port-forward svc/coruscant-dual-role-external-2 31072:9096 -n prod
```

Test services listening from outside:

```bash
echo 'testing (dev: tatooine) ports... '
; nc -vz localhost 31090 \
; nc -vz localhost 31091 \
; nc -vz localhost 31092
```

```bash
echo 'testing (uat: naboo) ports... '
; nc -vz localhost 31080 \
; nc -vz localhost 31081 \
; nc -vz localhost 31082
```

```bash
echo 'testing (prod: coruscant) ports... '
; nc -vz localhost 31070 \
; nc -vz localhost 31071 \
; nc -vz localhost 31072
```

## DESTROY

```bash
kubectl -n kafka delete $(kubectl get strimzi -o name -n kafka) \
kubectl -n dev delete $(kubectl get strimzi -o name -n dev) \
kubectl -n uat delete $(kubectl get strimzi -o name -n uat) \
kubectl -n prod delete $(kubectl get strimzi -o name -n prod) \

kubectl delete -f strimzi-operator.yml --namespace=kafka \
kubectl delete -f strimzi-operator-role-bindings.yml
```
