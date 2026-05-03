# Distributed Weather System

A distributed systems lab that simulates multiple Weather Stations producing telemetry and a Central Station consuming, processing, and storing it. The system is orchestrated locally with Docker Compose and on Kubernetes with Minikube. The Java services communicate via Kafka and persist to Postgres.

## What is Included
- Java 17+ services built with Maven ([pom.xml](pom.xml))
- Local deployment using Docker Compose ([part_d/docker-compose.yml](part_d/docker-compose.yml))
- Data analysis queries ([part_e/queries.sql](part_e/queries.sql))
- Kubernetes deployment using Minikube ([part_f/k8s-deployment.yaml](part_f/k8s-deployment.yaml))

## Project Structure
```
.
├─ README.md
├─ oldreadme.md
├─ pom.xml
├─ part_d/
│  └─ docker-compose.yml
├─ part_e/
│  └─ queries.sql
├─ part_f/
│  ├─ Dockerfile.central
│  ├─ Dockerfile.weather
│  └─ k8s-deployment.yaml
└─ src/
   └─ main/
      └─ java/
         └─ com/
            └─ weather/
```

## Prerequisites
- Java 17+ and Maven
- Docker
- Minikube and kubectl (for Kubernetes in Part F)

## Build the Java Services
Run from the repository root.
```bash
mvn clean package
```

## Part D: Docker Compose (Local Deployment)
Runs the full system locally: Zookeeper, Kafka, Postgres, Weather Stations, and Central Station.

1) Move to the compose folder:
```bash
cd part_d
```

2) Start the system:
```bash
docker-compose up --build
```

3) Stop and clean up:
```bash
# Stop with Ctrl+C, then
docker-compose down
```

## Part E: Data Analysis
The services from Part D must be running.

1) Open a shell in Postgres:
```bash
docker exec -it weather-db psql -U postgres -d weather_db
```

2) Run the queries from [part_e/queries.sql](part_e/queries.sql) to view:
- Battery status distribution
- Dropped messages count

Expected screenshots are stored in [part_e](part_e).

## Part F: Kubernetes Deployment (Minikube)
Deploys the same system inside a local Kubernetes cluster.

1) Start Minikube and point Docker to its daemon:
```bash
minikube start
minikube docker-env | Invoke-Expression
```

2) Build the Docker images from the repository root:
```bash
docker build -t my-weather-station:v1 -f part_f/Dockerfile.weather .
docker build -t my-central-station:v1 -f part_f/Dockerfile.central .
```

3) Apply the Kubernetes manifest:
```bash
kubectl apply -f part_f/k8s-deployment.yaml
```

4) Verify pod status:
```bash
kubectl get pods
```
Wait for all pods to show `STATUS: Running`.

5) Stop Minikube when finished:
```bash
minikube stop
```

## Troubleshooting
- If Kafka or Postgres containers fail to start, stop the stack and retry with `docker-compose down` followed by `docker-compose up --build`.
- If images do not build inside Minikube, confirm `minikube docker-env | Invoke-Expression` is active in the current PowerShell session.
- If pods are stuck in `ImagePullBackOff`, make sure the image tags match those in [part_f/k8s-deployment.yaml](part_f/k8s-deployment.yaml).

## Notes
- Source code is under [src/main/java/com/weather](src/main/java/com/weather).
- The compiled classes are created in [target](target) after a successful Maven build.
