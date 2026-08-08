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

---

# Step 3 – Verify the Swarm

Run: docker info

We should now see:

Swarm: active

instead of:

Swarm: inactive

---

# Step 4 – View the Nodes

Run: docker node ls
