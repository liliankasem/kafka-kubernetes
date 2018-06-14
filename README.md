# Running Kafka on Kubernetes

Based on these existing repos:

[https://github.com/kubernetes/charts/tree/master/incubator/kafka](https://github.com/kubernetes/charts/tree/master/incubator/kafka)

[https://github.com/Yolean/kubernetes-kafka](https://github.com/Yolean/kubernetes-kafka)

## Prerequites
- [Kubernetes Cluster](https://kubernetes.io/docs/setup/)
- [Helm](https://helm.sh/)

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