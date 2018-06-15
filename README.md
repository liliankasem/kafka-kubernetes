# Running Kafka on Kubernetes Basic

Based on these existing repos:

[https://github.com/kubernetes/charts/tree/master/incubator/kafka](https://github.com/kubernetes/charts/tree/master/incubator/kafka)

[https://github.com/Yolean/kubernetes-kafka](https://github.com/Yolean/kubernetes-kafka)

## Setup

To install the chart with the release name my-kafka in the default namespace:

`helm repo add incubator http://storage.googleapis.com/kubernetes-charts-incubator`

`helm install --name my-kafka incubator/kafka`

Or with values:

`helm install --name my-kafka -f values.yml incubator/kafka`

You can connect to Kafka by running a simple pod in the K8s cluster with a configuration like the one in `kafka-pod.yml`:

`kubectl create -f kafka-pod.yml`

## Create and View Topics

To create a new topic:

`kubectl -n default exec testclient -- ./bin/kafka-topics.sh --zookeeper my-kafka-zookeeper:2181 --topic test1 --create --partitions 1 --replication-factor 1`

List all kafka topics with:

`kubectl -n default exec testclient -- ./bin/kafka-topics.sh --zookeeper my-kafka-zookeeper:2181 --list`

## Console Producer and Consumer

To listen for messages on a topic:

`kubectl -n default exec -ti testclient -- ./bin/kafka-console-consumer.sh --bootstrap-server my-kafka:9092 --topic test1 --from-beginning`

To start an interactive message producer session:

`kubectl -n default exec -ti testclient -- ./bin/kafka-console-producer.sh --broker-list my-kafka-headless:9092 --topic test1`

To create a message in the above session, simply type the message and press `enter`.

# Running Kafka on Kubernetes [Yolean/kubernetes-kafka]

** Check the Yolean/kubernetes-kafka for most up to date manifest and docs **

## Setup

Clone repo: [https://github.com/Yolean/kubernetes-kafka](https://github.com/Yolean/kubernetes-kafka)

`cd kubernetes-kafka`

For the storage, this is based on your choice of provider. Yolean provides the following manifests to choose from:

- AKS
- AWS
- Docker
- GKE
- MiniKube

`kubectl apply -f ./configure/PROVIDER-storageclass-broker ... .yml`

`kubectl apply -f ./configure/PROVIDER-storageclass-zookeeper ... .yml`

`kubectl create -f 00-namespace.yml`

`kubectl apply -f ./rbac-namespace-default`

`kubectl apply -f ./zookeeper`

`kubectl apply -f ./kafka`

## Access Kafka Manager

`kubectl apply -f ./yahoo-kafka-manager`

`kubectl proxy &`

Kafka Manager:
http://localhost:8001/api/v1/proxy/namespaces/kafka/services/kafka-manager:80/

You have to configure a new cluster and connect zookeeper:

`Cluster -> Add Cluster -> Cluster Zookeeper Hosts = zookeeper.kafka:2181 -> Version 0.11.0.0`

Learn more here: [https://github.com/yahoo/kafka-manager](https://github.com/yahoo/kafka-manager)

## Producer and Consumer

### Deploying within the cluster

The host/broker will be the bootstrap service's Cluster IP (or 'service-name.namespace') and the port number; based on the previous setup steps, it will most likely look this: `bootstrap.kafka:9092`

### Accessing Kafka from outside the cluster

//TODO

## Delete Everything

If you want to remove all the resources created:

`kubectl delete ns kafka`