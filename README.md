# Run local Kafka with Docker Compose

### Versions

| Confluent Container Version   | Kafka Equivalent               |
|-------------------------------|--------------------------------|
| `confluentinc/cp-kafka:7.7.0` | `org.apache.kafka/kafka:3.7.0` |

### Guide

* [Introduction](#introduction)
* [Prequisites](#prerequisites)
  * [Install Docker](#install-docker)
  * [Clone this repository](#clone-this-repository)
  * [Change into the repository directory](#change-into-the-repository-directory)
* [Run Kpow Community Edition (optional)](#run-kpow-community-edition-optional)
* [Run a simple Kafka cluster (no authentication)](#run-a-simple-kafka-cluster-no-authentication)
  * [Start the Kafka cluster](#start-the-kafka-cluster)
  * [Stop the Kafka cluster](#stop-the-kafka-cluster)
  * [Access the Kafka cluster](#access-the-kafka-cluster)
    * [Localhost bootstrap](#localhost-bootstrap)
    * [Docker host bootstrap](#docker-host-bootstrap)
    * [host.docker.internal bootstrap](#hostdockerinternal-bootstrap)
* [Run a SASL Kafka cluster (with authentication)](#run-a-sasl-kafka-cluster-with-authentication)
  * [Client authentication](#client-authentication)
* [License](#license) 

## Introduction

This repository contains docker compose configuration to run a local Kafka cluster with Docker Compose.

We use similar configuration for local development of our Kafka UI and API product, [Kpow for Apache Kafka](https://factorhouse.io/kpow).

Two types of Kafka Cluster are supported, simple (no authentication) and SASL authenticated. 

See [kpow-local](https://github.com/factorhouse/kpow-local) for a more complex local configuration consisting of Kpow, Kafka, Schema, Connect, and ksqlDB.

## Prerequisites

### Install Docker

The local cluster runs with Docker Compose, so you will need to [install Docker](https://www.docker.com/).

Once Docker is installed, clone this repository and run the following commands from the base path.

### Clone this repository

```
git clone git@github.com:factorhouse/kafka-local.git
```

### Change into the repository directory

```
cd kafka-local
```

## Run Kpow Community Edition (Optional)

The community edition of Kpow for Apache Kafka is free to use by individuals and organisations.

You can check it out if you like, but the configuration in this repository doesn't require Kpow in any way.

![Kpow UI](/resources/img/kpow-overview.png)

Start a local Kafka cluster with the configuration in this repository then:

* Get a [free Kpow Community license](https://factorhouse.io/kpow/community/)
* Enter the license details into [resoources/kpow/no-auth.env](resources/kpow/no-auth.env) or [resoources/kpow/sasl-auth.env](resources/kpow/sasl-auth.env)
* Start Kpow Community Edition:

**Start Kpow Community Edition with No Auth Kafka Cluster**

```
docker run --network=kafka-local_default -p 3000:3000 -m2G --env-file ./resources/kpow/no-auth.env factorhouse/kpow-ce:latest
```

**Start Kpow Community Edition with SASL Auth Kafka Cluster**

```
docker run --network=kafka-local_default -p 3000:3000 -m2G --env-file ./resources/kpow/sasl-auth.env factorhouse/kpow-ce:latest
```

* Navigate to http://localhost:3000 (the UI might look empty until you start creating topics and writing data)

## Run a simple Kafka cluster (no authentication)

### Start the Kafka cluster

This command starts a Kafka Cluster that does not require clients to authenticate.

```bash
docker compose -f docker-compose-no-auth.yml up
```

```
[+] Running 5/5
 ✔ Network kafka-local_default      Created0.0s
 ✔ Container zookeeper              Created0.0s
 ✔ Container kafka-local-kafka-3-1  Created0.0s
 ✔ Container kafka-local-kafka-1-1  Created0.0s
 ✔ Container kafka-local-kafka-2-1  Created0.0s
Attaching to kafka-1-1, kafka-2-1, kafka-3-1, zookeeper
zookeeper  | ===> User
zookeeper  | uid=1000(appuser) gid=1000(appuser) groups=1000(appuser)
zookeeper  | ===> Configuring ...
```

### Stop the Kafka cluster

First, hit ctrl-c in the terminal running the Docker Compose process.

```bash
^C
Gracefully stopping... (press Ctrl+C again to force)
[+] Stopping 4/4
 ✔ Container kafka-local-kafka-1-1  Stopped5.8s
 ✔ Container kafka-local-kafka-2-1  Stopped0.7s
 ✔ Container kafka-local-kafka-3-1  Stopped0.7s
 ✔ Container zookeeper              Stopped0.5s
canceled
```

Then stop/clear the Docker Compose resources

```
docker compose -f docker-compose-no-auth.yml down
```

```
[+] Running 5/0
 ✔ Container kafka-local-kafka-1-1  Removed                                                                                                                                                                    0.0s
 ✔ Container kafka-local-kafka-2-1  Removed                                                                                                                                                                    0.0s
 ✔ Container kafka-local-kafka-3-1  Removed                                                                                                                                                                    0.0s
 ✔ Container zookeeper              Removed                                                                                                                                                                    0.0s
 ✔ Network kafka-local_default      Removed
```
 
### Access the Kafka cluster

To access this cluster you can:

1. Connect to the bootstrap on localhost / 127.0.0.1 (most likely non-docker applications)
2. Connect to the bootstrap on the Docker defined hosts (kakfa-1, kafka-2, kafka-3)
3. Connect to the bootstrap using `host.docker.internal` which is similar to (1)

#### Localhost bootstrap

Applications that are external to Docker can access the Kafka cluster via the Localhost bootstrap.

```
bootstrap: 127.0.0.1:9092,127.0.0.1:9093,127.0.0.1:9094
```

#### Docker host bootstrap

Containerized applications can connect to the Kafka cluster via the Docker Host bootstrap.

These docker hosts (kakfa-1, kafka-2, kafka-3) are defined within the comoose.yml.

When starting your Docker container, specify that it should share the `kafka-local_default` network.
 
```
docker run --network=kafka-local_default ...
```

Then connect to the hosts that are running on that network

```
bootstrap: kafka-1:19092,kafka-2:19093,kafka-3:19094 
```

#### host.docker.internal bootstrap

This is a good trick for running a docker container that connects back to a port open on the host machine.

`host.docker.internal` effective routes back to localhost.

```
bootstrap: host.docker.internal:9092,host.docker.internal:9093,host.docker.internal:9094 
```

## Run a SASL Kafka cluster (with authentication)

Use the `docker-compose-sasl-auth.yml` configuration to run a SASL authenticated cluster:

```
docker compose -f docker-compose-sasl-auth.yml up
```

Bootstrap configuration is the same as the simple cluster however clients must authenticate to connect. 

Authentication configuration is specified in [resources/docker/kafka_jaas.conf](resources/docker/kafka_jaas.conf).

### Client authentication

To connect a client to this client use the following connection settings:

```
security.protocol: SASL_PLAINTEXT
sasl.mechanism:    PLAIN
sasl.jaas.config:  org.apache.kafka.common.security.plain.PlainLoginModule required username="client" password="client-secret";
```

## License

This repository is released under the Apache 2.0 License.

Copyright © Factor House.
