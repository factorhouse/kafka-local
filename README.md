# Run a local SASL authenticated Kafka Cluster with Docker Compose

This repository contains configuration to run a locak, SASL authenticated 3-node Kafka (v2.5.0) Cluster.

## Prerequisites

Our local cluster runs with Docker Compose, so you will need to [install Docker](https://www.docker.com/).

## Cluster Actions

### Start the Kafka cluster

```bash
HOME=./ docker compose up

[+] Running 5/2
 ⠿ Network kafka-local_default      Created                                                                                                                3.3s
 ⠿ Container zookeeper              Created                                                                                                                0.1s
 ⠿ Container kafka-local_kafka-2_1  Created                                                                                                                0.1s
 ⠿ Container kafka-local_kafka-3_1  Created                                                                                                                0.1s
 ⠿ Container kafka-local_kafka-1_1  Created                                                                                                                0.1s
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
 ⠇ Container kafka-local_kafka-3_1  Stopping                                                                                                              10.9s
 ⠇ Container kafka-local_kafka-1_1  Stopping                                                                                                              10.9s
 ⠿ Container kafka-local_kafka-2_1  Stopped                                                                                                               5.1s
```

Then stop/clear the Docker Compose resources

```bash
HOME=./ docker compose down
[+] Running 5/5
 ⠿ Container kafka-local_kafka-1_1  Removed                                                                                                                0.0s
 ⠿ Container kafka-local_kafka-3_1  Removed                                                                                                                0.0s
 ⠿ Container kafka-local_kafka-2_1  Removed                                                                                                                0.0s
 ⠿ Container zookeeper              Removed                                                                                                                0.0s
 ⠿ Network kafka-local_default      Removed                                                                                                                2.4s
 ``` 
