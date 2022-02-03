Følger guide:
https://blog.devgenius.io/kafka-on-kubernetes-using-strimzi-part-2-71a8ba8e9605

Først installerer minikube:

```
brew install minikube
which minikube
minikube start

brew install helm
```

Installing Strimzi using Helm is pretty easy:

//add helm chart repo for Strimzi
```
helm repo add strimzi https://strimzi.io/charts/

kubectl create namespace kafka

helm install dev-strimzi-release strimzi/strimzi-kafka-operator --namespace kafka

helm install my-strimzi-release strimzi/strimzi-kafka-operator --namespace kafka --version 0.28.0
```

To confirm that the Strimzi Operator had been deployed, check it’s Pod (it should transition to Running status after a while)

```
kubectl get pods -l=name=strimzi-cluster-operator -n kafka
```

NAME                                        READY   STATUS    RESTARTS   AGE
strimzi-cluster-operator-5c66f679d5-69rgk   1/1     Running   0          43s
Check the Custom Resource Definitions as well:

```
kubectl get crd | grep strimzi
```

Create a Kafka cluster

touch /kafka/kafka-resource.yaml:

```
apiVersion: kafka.strimzi.io/v1beta2
kind: Kafka
metadata:
  name: kafka-cluster
spec:
  kafka:
    replicas: 1
    listeners:
      - name: plain
        port: 9092
        type: internal
        tls: false
      - name: external
        port: 9094
        type: loadbalancer
        tls: true
    storage:
      type: jbod
      volumes:
      - id: 0
        type: persistent-claim
        size: 100Gi
        deleteClaim: false
    config:
      offsets.topic.replication.factor: 1
      transaction.state.log.replication.factor: 1
      transaction.state.log.min.isr: 1
      default.replication.factor: 1
  zookeeper:
    replicas: 1
    storage:
      type: persistent-claim
      size: 100Gi
      deleteClaim: false
  entityOperator:
    topicOperator: {}
    userOperator: {}
```

```
kubectl apply -f kafka-resource.yaml -n kafka
```

Create a Topic:
```
cat << EOF | kubectl create -n kafka-project -f -
apiVersion: kafka.strimzi.io/v1beta2
kind: KafkaTopic
metadata:
  name: my-topic
  labels:
    strimzi.io/cluster: "kafka-cluster"
spec:
  partitions: 1
  replicas: 1
EOF
```

Opprette fil kafka-topic.yaml:
```
kubectl create namespace kafka-project
```

```
apiVersion: kafka.strimzi.io/v1beta2
kind: KafkaTopic
metadata:
  name: sensor
  labels:
    strimzi.io/cluster: "kafka-cluster"
spec:
  partitions: 1
  replicas: 1
```

```
kubectl apply -f kafka-sensor-topic.yaml -n kafka-project
```

Producer:

```
kubectl exec -i -t kafka-cluster-kafka-0 -n kafka -- /bin/bash
```

```
bin/kafka-console-producer.sh --bootstrap-server localhost:9092 --topic sensor
Hello
My name is Amarendra
```

```
bin/kafka-console-producer.sh --broker-list bootstrap.strimzi.local:443 --producer-property security.protocol=SSL --producer-property ssl.truststore.password=password --producer-property ssl.truststore.location=./truststore.jks --topic sensor
```

Consumer:
```
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic sensor --from-beginning

bin/kafka-console-consumer.sh --bootstrap-server --group ppr localhost:9092 --topic sensor --from-beginning


bin/kafka-console-consumer.sh --bootstrap-server localhost:9094 --topic sensor --from-beginning


bin/kafka-console-consumer.sh --bootstrap-server --group consumer-group-a localhost:9092 --topic sensor --from-beginning

bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic sensor --from-beginning --partition 0

```

```
bin/kafka-console-consumer.sh --bootstrap-server --group consumer-group-a localhost:9092 --topic sensor --from-beginning

bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic sensor --from-beginning

```

Slette alle pods i namespace:
```
kubectl delete --all pods --namespace=kafka
```

If you have altered a topic and want to view the topic configuration the following command will be helpful

```
bin/kafka-topics.sh --bootstrap-server localhost:9092 --describe --topics-with-overrides
```

alternativt:
bin/kafka-topics.sh --bootstrap-server localhost:9092 --describe --topic sensor

Flere:

```
kubectl get all -A

kubectl delete all --all --namespace=kafka

kubectl delete namespace kafka


```

bootstrapServers = os.Getenv("KAFKA_BOOTSTRAP_SERVERS")
caLocation = os.Getenv("CA_CERT_LOCATION")
topic = os.Getenv("KAFKA_TOPIC")

    config := &kafka.ConfigMap{"bootstrap.servers": bootstrapServers, "security.protocol": "SSL", "ssl.ca.location": caLocation}

$KAFKA_HOME/bin/kafka-console-producer.sh --broker-list $LOADBALANCER_PUBLIC_IP:9094 --topic $TOPIC_NAME --producer.config client-ssl.properties

TODO:
Generere client-ssl.properties
Opprette python-script for å lese inn temperatur, starte med mock data og registry.
Finne ut hvordan arkitektur for løsning med schema-registry må være
Opprette en java consume-client som leser topic, kanskje genere json?

Sertifikat:
cd robipozzi-raspberry-sensors/Part1

export CLUSTER_NAME=kafka-cluster

we need to generate PEM files in order for a Python program to securely connect to the Kafka cluster:

FOR PYTHON:
kubectl get secret $CLUSTER_NAME-cluster-ca-cert -n kafka -o jsonpath='{.data.ca\.crt}' | base64 --decode > certs/ca.crt
keytool -import -trustcacerts -alias root -file certs/ca.crt -keystore certs/truststore.jks -storepass password -noprompt

And then generate PEM files in order for a Python program to securely connect to Kafka. The jkstopem.sh script is provided to do the job, run the following:
./jkstopem.sh certs truststore.jks password root kafka/certs

```
minikube tunnel
```
// export BOOTSTRAP_SERVER=10.0.0.23:9094
// KAFKA_BROKER=$BOOTSTRAP_SERVER SSL=true TOPIC=sensor python sensor.py
// KAFKA_BROKER=localhost:55758 SSL=true TOPIC=sensor python sensor.py

KAFKA_BROKER=10.0.0.22:31172 SSL=false TOPIC=sensor python sensor.py (det er denne som skal brukes)

----
kubectl get configmap/kafka-cluster-kafka-config -o yaml -n kafka

FOR JAVA?:
kubectl get secret $CLUSTER_NAME-cluster-ca-cert -n kafka -o jsonpath='{.data.ca\.crt}' | base64 --decode > ca.crt
kubectl get secret $CLUSTER_NAME-cluster-ca-cert -o jsonpath='{.data.ca\.password}' | base64 --decode > ca.password


-----------
Begynne på nytt med cluster:

rm ca.crt
rm ca.password
rm truststore.jks

kubectl delete all --all --namespace=kafka
kubectl delete namespace kafka

kubectl create namespace kafka
helm install dev-strimzi-release strimzi/strimzi-kafka-operator --namespace kafka
kubectl get pods -l=name=strimzi-cluster-operator -n kafka
kubectl apply -f kafka-resource.yaml -n kafka

kubectl get all -n kafka

(helm install my-strimzi-release strimzi/strimzi-kafka-operator --namespace kafka --version 0.28.0)

-----------