# Containerized Standalone Flink Cluster
This is the containerized standalone Apache Flink Cluster.

## Requirements

- Docker
- Docker Compose

## Quick Start Usage

Flink cluster starts with 1 JobManager container, and 1 TaskManager container on Session Mode.

### 1. Launch Flink Cluster

Launch the container cluster using Docker Compose in background mode

```bash
docker compose up -d
```

### 2. Submit Flink Job

Submit to run [a sample Flink batch job (WordCount.jar)](https://github.com/apache/flink/tree/master/flink-examples/flink-examples-batch/src/main/java/org/apache/flink/examples/java/wordcount/) which is included in the JobManager container

```bash
JOBMANAGER_CONTAINER=$(docker ps --filter name=jobmanager --format={{.ID}})

docker container exec -it $JOBMANAGER_CONTAINER flink run examples/batch/WordCount.jar
```

### 3. Access Dashboard of Flink Cluster

Access the Apache Flink Dashboard to check the result of the submitted job execution

- URL: <http://localhost:8081/>

### 4. Shutdown Flink Cluster

Stop and remove the container cluster with all of the images.

```bash
docker compose down --rmi all
```

## [Optional] Customize Flink Cluster

### Scale the TaskManagers Up or Down in the Flink Cluster

```bash
# Scale Up to 3 TaskManagers in the Cluster
docker compose up -d --scale taskmanager=3
```

### Adjust the Number of Task Slots for Each TaskManager when the Flink Cluster is launched

```bash
# Adjust to 1 Task Slot per TaskManager
EACH_TASK_SLOTS=1 docker compose up -d
```

## See Also

- [Apache Flink Official Documentation (v1.17.0)](https://nightlies.apache.org/flink/flink-docs-release-1.17/)
- [Official Examples of Flink Job (GitHub)](https://github.com/apache/flink/tree/master/flink-examples)
