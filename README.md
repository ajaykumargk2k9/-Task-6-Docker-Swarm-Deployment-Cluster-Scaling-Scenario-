# Task-6-Docker-Swarm-Deployment-Cluster-Scaling-Scenario-

Currently our application looks like this everything runs on one machine 

![image alt](https://github.com/ajaykumargk2k9/-Task-6-Docker-Swarm-Deployment-Cluster-Scaling-Scenario-/blob/main/Images/Existing%20application.PNG?raw=true)

Imagine instead of 100 users we suddenly have 50,000 users

One Employee Service container becomes overloaded.

Eventually Employee Service

❌ Crashes

Our application becomes unavailable.

---

# Traditional Docker

Docker only runs containers.

If it crashes

Container

❌ Dead

Docker won't automatically maintain the desired number of running instances unless you've configured restart policies and it won't manage multiple replicas or load balancing.

---

# Docker Swarm

Swarm introduces container orchestration

Instead of

1 Container

Swarm runs

Employee Service with replicas

Replica 1

Replica 2

Replica 3

Users are automatically distributed across these replicas.

If one replica dies

Replica 2

❌

Swarm notices and immediately creates

Replica 4 without we doing anything.

This is called Desired State

We tell Docker Keep 3 replicas running.

Swarm continuously works to maintain exactly three healthy replicas.

---

# Initialize Docker Swarm

# Step 1 – Open Terminal

Go to our project directory terminal where Docker is available.

---

# Step 2 – Initialize Swarm

Run: docker swarm init

Swarm initialized: current node is now a manager.

We will also see a worker join token.

![image alt](https://github.com/ajaykumargk2k9/-Task-6-Docker-Swarm-Deployment-Cluster-Scaling-Scenario-/blob/main/Images/docker%20swarm%20init.PNG?raw=true)

---

# Step 3 – Verify the Swarm

Run: docker info

We should now see:

Swarm: active

instead of:

Swarm: inactive

![image alt](https://github.com/ajaykumargk2k9/-Task-6-Docker-Swarm-Deployment-Cluster-Scaling-Scenario-/blob/main/Images/docker%20info.PNG?raw=true)

---

# Step 4 – View the Nodes

Run: docker node ls

![image alt](https://github.com/ajaykumargk2k9/-Task-6-Docker-Swarm-Deployment-Cluster-Scaling-Scenario-/blob/main/Images/docker%20node%20ls.PNG?raw=true)

---

# Create an Overlay Network

In Swarm services may run on different nodes so we need an overlay network.

![image alt](https://github.com/ajaykumargk2k9/-Task-6-Docker-Swarm-Deployment-Cluster-Scaling-Scenario-/blob/main/Images/Overlay%20Network.PNG?raw=true)

---

# Step 1 – View Existing Networks

Run: docker network ls

We should see something like:

bridge
host
none
ingress
docker_gwbridge

![image alt](https://github.com/ajaykumargk2k9/-Task-6-Docker-Swarm-Deployment-Cluster-Scaling-Scenario-/blob/main/Images/docker%20network%20ls.PNG?raw=true)

---

# Step 2 – Create Your Own Overlay Network

Run: docker network create --driver overlay employee-network

![image alt](https://github.com/ajaykumargk2k9/-Task-6-Docker-Swarm-Deployment-Cluster-Scaling-Scenario-/blob/main/Images/docker%20network%20create.PNG?raw=true)

---

# Step 3 – Verify the Network

Run: docker network ls

We should now see something similar to:

NETWORK ID       NAME               DRIVER
xxxx             employee-network   overlay
xxxx             ingress            overlay

![image alt](https://github.com/ajaykumargk2k9/-Task-6-Docker-Swarm-Deployment-Cluster-Scaling-Scenario-/blob/main/Images/docker%20network%20ls%20verify.PNG?raw=true)

---

# Step 4 – Inspect the Network

Run: docker network inspect employee-network

![image alt](https://github.com/ajaykumargk2k9/-Task-6-Docker-Swarm-Deployment-Cluster-Scaling-Scenario-/blob/main/Images/docker%20network%20inspect.PNG?raw=true)

![image alt](https://github.com/ajaykumargk2k9/-Task-6-Docker-Swarm-Deployment-Cluster-Scaling-Scenario-/blob/main/Images/docker%20netowrk%20inspect%20extension.PNG?raw=true)

---

# Deploy our First Swarm Service

# Step 1 – Stop the Existing Employee Service Container

Run: docker stop employee-service

Verify: docker ps

![image alt](https://github.com/ajaykumargk2k9/-Task-6-Docker-Swarm-Deployment-Cluster-Scaling-Scenario-/blob/main/Images/docker%20stop%20employee-service.PNG?raw=true)

---

# Step 2 – Deploy Employee Service as a Swarm Service

Run:  docker service create --name employee-service --network employee-network --publish 3001:3001 --replicas 3 employee-management-system-employee-service:latest

# Step 3 – Check the Service

Run: docker service ls

Expected:

NAME               MODE        REPLICAS
employee-service   replicated  3/3

![image alt](https://github.com/ajaykumargk2k9/-Task-6-Docker-Swarm-Deployment-Cluster-Scaling-Scenario-/blob/main/Images/docker%20service%20create%20ls.PNG?raw=true)

---

# Step 4 – View Running Tasks

Run: docker service ps employee-service

# Step 5 – View the Containers

Run: docker ps

![image alt](https://github.com/ajaykumargk2k9/-Task-6-Docker-Swarm-Deployment-Cluster-Scaling-Scenario-/blob/main/Images/docker%20service%20ps.PNG?raw=true)

---

# Scaling & Load Balancing

# Step 1 – Scale to 5 Replicas

Run: docker service scale employee-service=5

Expected output:

overall progress: 5 out of 5 tasks
verify: Service converged

# Step 2 – Verify

docker service ls

Expected:

employee-service

5/5

![image alt](https://github.com/ajaykumargk2k9/-Task-6-Docker-Swarm-Deployment-Cluster-Scaling-Scenario-/blob/main/Images/docker%20service%20scale.PNG?raw=true)

---

# Step 3 – Inspect Tasks

docker service ps employee-service

Expected:

employee-service.1

employee-service.2

employee-service.3

employee-service.4

employee-service.5

# Step 4 – Check Containers

docker ps

You should now see five Employee Service containers.

![image alt](https://github.com/ajaykumargk2k9/-Task-6-Docker-Swarm-Deployment-Cluster-Scaling-Scenario-/blob/main/Images/docker%20service%20ps%20scale.PNG?raw=true)
