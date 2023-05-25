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

### 3. Access Web Dashboard of Flink Cluster

Access the Apache Flink Dashboard to check the result of the submitted job execution

- URL: <http://localhost:8081/>

### 4. Shutdown Flink Cluster

Stop and remove the container cluster with all of the images.

```bash
docker compose down --rmi all
```

## [Optional] Submit Your Local Flink Job

You have 3 options to submit your jar file as flink job.

### [1st Option] Put on the Local Mount Point

After putting your jar file on the local mount point of `./data/flink/jobs/`, run as Flink job using the commands in the JobManager container.

```bash
# Put your jar file into the local mount point.
mv /path/to/your-flink-job.jar ./data/flink/jobs/

# Run as Flink job by following commands.
JOBMANAGER_CONTAINER=$(docker ps --filter name=jobmanager --format={{.ID}})
docker container exec -it $JOBMANAGER_CONTAINER flink run /data/flink/jobs/your-flink-job.jar
```

### [2nd Option] Post to the Flink REST API

By Posting your jar file to the Flink REST API, You can run as Flink job through HTTP.

```bash
# Upload your jar file
curl -X POST -H "Expect:" -F "jarfile=@/path/to/flink-job.jar" http://localhost:8081/jars/upload

# See all of the jar-ids which is associated with the uploaded jar files
curl -X GET http://localhost:8081/jars/

# Check the jar-id of your uploaded jar file
curl -X GET http://localhost:8081/jars/ | jq -r '.files[].id' | grep flink-job.jar

# Run as Flink job by posting the jar-id of your uploaded jar file
curl -X POST http://localhost:8081/jars/{jar-id}/run
```

### [3rd Option] Upload to the Flink Web Dashboard

1. Access the submit page of Apache Flink Dashboard.

    URL: <http://localhost:8081/#/submit>

2. Click "Add New" and upload your jar file.

3. Select your jar file.

4. (Optional) Configure the settings to run as Flink job.

5. Click "Submit".

6. See the result of the submitted job execution.

    URL: <http://localhost:8081/#/job/completed>

## [Optional] Customize Flink Cluster

### Scale the TaskManagers Up or Down in the Flink Cluster

```bash
# Scale Up to 3 TaskManagers in the Cluster (default: 1)
docker compose up -d --scale taskmanager=3
```

### Adjust the Number of Task Slots for Each TaskManager when the Flink Cluster is launched

```bash
# Adjust to 1 Task Slot per TaskManager (default: 2)
EACH_TASK_SLOTS=1 docker compose up -d
```

## See Also

- [Apache Flink Official Documentation (v1.17.0)](https://nightlies.apache.org/flink/flink-docs-release-1.17/)
- [Official Examples of Flink Job (GitHub)](https://github.com/apache/flink/tree/master/flink-examples)
