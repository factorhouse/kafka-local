# Run local Kafka resources with Docker Compose

This repository contains docker compose configuration to run local Kafka resources with Docker Compose.

We use similar configuration for local development of our Kafka UI and API product, [Kpow for Apache Kafka](https://factorhouse.io/kpow).

This repository uses `confluentinc/cp-kafka:7.5.3` equivalent to `org.apache.kafka/kafka:3.5.2`.

## Prerequisites

The local cluster runs with Docker Compose, so you will need to [install Docker](https://www.docker.com/).

## (Optional) Run with Kpow Community Edition

Start a local Kafka cluster with the configuration in this repository then:

* Get a [free Kpow Community license](https://factorhouse.io/kpow/community/) 
* Enter the license details into [docker/kpow.env](docker/kpow.env)
* Start Kpow Community Edition:

**Start Kpow Community Edition with No Auth Kafka Cluster**

```
docker run --network=kafka-local_default -p 3000:3000 -m2G --env-file ./resources/kpow/no-auth.env factorhouse/kpow-ce:latest
```

**Start Kpow Community Edition with SASL Auth Kafka Cluster**

```
docker run --network=kafka-local_default -p 3000:3000 -m2G --env-file ./resources/kpow/no-auth.env factorhouse/kpow-ce:latest
```

* Navigate to http://localhost:3000

(Your dashboard might look quite empty until you start creating topics and writing data..)

![Kpow UI](/resources/img/kpow-overview.png)

## Kafka Cluster Actions

### Start the Kafka cluster

```bash
HOME=./ docker compose up

[+] Running 5/2
 ⠿ Network kafka-local_default      Created                                                 3.3s
 ⠿ Container zookeeper              Created                                                 0.1s
 ⠿ Container kafka-local_kafka-2_1  Created                                                 0.1s
 ⠿ Container kafka-local_kafka-3_1  Created                                                 0.1s
 ⠿ Container kafka-local_kafka-1_1  Created                                                 0.1s
Attaching to kafka-1_1, kafka-2_1, kafka-3_1, zookeeper
zookeeper  | ===> User
zookeeper  | uid=0(root) gid=0(root) groups=0(root)
zookeeper  | ===> Configuring ...
```

### Stop the Kafka cluster

First, hit ctrl-c in the terminal running the Docker Compose process.

```bash
^C
Gracefully stopping... (press Ctrl+C again to force)
[+] Running 1/3
 ⠇ Container kafka-local_kafka-3_1  Stopping                                                10.9s
 ⠇ Container kafka-local_kafka-1_1  Stopping                                                10.9s
 ⠿ Container kafka-local_kafka-2_1  Stopped                                                 5.1s
```

Then stop/clear the Docker Compose resources

```bash
HOME=./ docker compose down
[+] Running 5/5
 ⠿ Container kafka-local_kafka-1_1  Removed                                                 0.0s
 ⠿ Container kafka-local_kafka-3_1  Removed                                                 0.0s
 ⠿ Container kafka-local_kafka-2_1  Removed                                                 0.0s
 ⠿ Container zookeeper              Removed                                                 0.0s
 ⠿ Network kafka-local_default      Removed                                                 2.4s
```
 
## Bootstrap Configuration

You can connect to this cluster directly on localhost, or from another docker container by specifying the network.

## Localhost Bootstrap

```
bootstrap: 127.0.0.1:9092,127.0.0.1:9093,127.0.0.1:9094
```

## Docker Network Bootstrap

To connect a process within a Docker container to the cluster, specify the network:
 
```
docker run --network=kafka-local_default ...
```

Then use:

```
bootstrap: kafka-1:19092,kafka-2:19093,kafka-3:19094 
```

## Client Configuration

This Kafka cluster requires clients connect with SASL authentication (see: [docker/kafka_jaas.conf](docker/kafka_jaas.conf))

To connect a client to this client use the following connection settings:

```
security.protocol: SASL_PLAINTEXT
sasl.mechanism:    PLAIN
sasl.jaas.config:  org.apache.kafka.common.security.plain.PlainLoginModule required username="client" password="client-secret";
```
