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

---

# Auto Healing (Self-Healing)

Suppose our application has 5 replicas:

Replica 1
Replica 2
Replica 3
Replica 4
Replica 5

Suddenly:

Replica 3 ❌ Crashes

Without Swarm:

Users lose capacity.
An administrator must manually restart the container.

With Swarm:

Replica 3 ❌

↓

Swarm detects failure

↓

Creates a replacement automatically

↓

Replica 6 ✅

The service returns to the desired count without manual intervention.

---

# Step 1 – List Running Containers

docker ps

![image alt](https://github.com/ajaykumargk2k9/-Task-6-Docker-Swarm-Deployment-Cluster-Scaling-Scenario-/blob/main/Images/docker%20ps%20auto-healing.PNG?raw=true)

# Step 2 – Stop the Container

![image alt](https://github.com/ajaykumargk2k9/-Task-6-Docker-Swarm-Deployment-Cluster-Scaling-Scenario-/blob/main/Images/docker%20stop%20auto-healing.PNG?raw=true)

# Step 3 – Immediately Watch the Service

Run: docker service ps employee-service

![image alt](https://github.com/ajaykumargk2k9/-Task-6-Docker-Swarm-Deployment-Cluster-Scaling-Scenario-/blob/main/Images/docker%20service%20ps%20auto-healing.PNG?raw=true)

# Step 4 – Verify Containers

Run: docker ps

We should still see 5 running Employee Service containers even though we manually stopped one.

This proves that Swarm is enforcing the desired state.

![image alt](https://github.com/ajaykumargk2k9/-Task-6-Docker-Swarm-Deployment-Cluster-Scaling-Scenario-/blob/main/Images/docker%20ps%20auto-healing%20extension.PNG?raw=true)

---

# Rolling Updates & Rollback

Imagine our Employee Service is currently running:

Employee Service v1

Replica 1
Replica 2
Replica 3
Replica 4
Replica 5

Your development team fixes a bug and releases:

Employee Service v2

---

# Traditional Deployment

Stop all containers ❌

Deploy new version

Start all containers

Problem:

❌ Application is unavailable during the deployment.

# Docker Swarm Rolling Update

Instead of stopping everything, Swarm updates one replica at a time.

Before

v1 v1 v1 v1 v1

↓

Update Replica 1

v2 v1 v1 v1 v1

↓

Update Replica 2

v2 v2 v1 v1 v1

↓

Update Replica 3

v2 v2 v2 v1 v1

↓

Update Replica 4

v2 v2 v2 v2 v1

↓

Update Replica 5

v2 v2 v2 v2 v2

Users continue accessing the application throughout the update.

# Step 1 – Update app.js

Open:

employee-management-system/

└── employee-service/

    └── app.js

const express = require("express");

const os = require("os");

const app = express();

app.get("/", (req, res) => {

    res.send(`
    
        <h1>Employee Service - Version 2</h1>
        
        <h2>Docker Swarm Rolling Update Demo</h2>
        
        <p>Container: ${os.hostname()}</p>
        
    `);
    
});

app.listen(3001, () => {

    console.log("Employee Service running on port 3001");
});


---

# Step 2 – Build Version 2 Image

Go to:

employee-management-system/employee-service

Build a new image:

docker build -t employee-management-system-employee-service:v2 .

![image alt](https://github.com/ajaykumargk2k9/-Task-6-Docker-Swarm-Deployment-Cluster-Scaling-Scenario-/blob/main/Images/rolling%20build.PNG?raw=true)

---

# Step 3 – Verify

Run: docker images

You should now see:

employee-management-system-employee-service

latest

v2

![image alt](https://github.com/ajaykumargk2k9/-Task-6-Docker-Swarm-Deployment-Cluster-Scaling-Scenario-/blob/main/Images/rolling%20images.PNG?raw=true)

---

# Step 4 – Perform the Rolling Update

Run: docker service update --image employee-management-system-employee-service:v2 employee-service

![image alt](https://github.com/ajaykumargk2k9/-Task-6-Docker-Swarm-Deployment-Cluster-Scaling-Scenario-/blob/main/Images/rolling%20service%20update.PNG?raw=true)

---

# Step 5 – Watch the Update

Run: docker service ps employee-service

We should see:

Running

Shutdown

Running

Shutdown

Swarm replaces replicas one by one until all are using v2.

![image alt](https://github.com/ajaykumargk2k9/-Task-6-Docker-Swarm-Deployment-Cluster-Scaling-Scenario-/blob/main/Images/rolling%20service%20ps.PNG?raw=true)

---

# Step 6 – Verify

Open: http://localhost:3001

We should see:

Employee Service Version 2

Refresh the page several times.

The hostname may change as different replicas serve your requests.

![image alt](https://github.com/ajaykumargk2k9/-Task-6-Docker-Swarm-Deployment-Cluster-Scaling-Scenario-/blob/main/Images/Rolling%20employee%20service.PNG?raw=true)

---

# Enterprise Rolling Updates

Right now our service looks like this:

![image alt](https://github.com/ajaykumargk2k9/-Task-6-Docker-Swarm-Deployment-Cluster-Scaling-Scenario-/blob/main/Images/Current%20rolling%20architecture.PNG?raw=true)

All replicas are running the same image.

# Step 1 – Update the Application

Open

employee-management-system
    │
    
    └── employee-service
            │
            
            └── app.js

Replace our existing app.js with:

const express = require("express");

const os = require("os");

const app = express();

app.get("/", (req, res) => {

    res.send(`
    
        <h1>Employee Service - Version 2</h1>

        <h2>Docker Swarm Rolling Update Demo</h2>

        <p><b>Container:</b> ${os.hostname()}</p>

        <p><b>Date:</b> ${new Date()}</p>

    `);

});

app.listen(3001, () => {

    console.log("Employee Service Version 2 running on port 3001");

});

---

# Why Are We Adding These?

Instead of simply displaying

Employee Service

we'll now display

Employee Service Version 2

plus

Container ID

plus

Current Time

---

# Step 2 – Build Version 2

Navigate to:

cd employee-service

Build:

docker build -t employee-management-system-employee-service:v2 .

![image alt](https://github.com/ajaykumargk2k9/-Task-6-Docker-Swarm-Deployment-Cluster-Scaling-Scenario-/blob/main/Images/rolling%20build.PNG?raw=true)

---

# Step 3 – Verify Images

docker images

![image alt](https://github.com/ajaykumargk2k9/-Task-6-Docker-Swarm-Deployment-Cluster-Scaling-Scenario-/blob/main/Images/rolling%20images.PNG?raw=true)

---

# Step 4 – Rolling Update

Now update the running service.

docker service update --image employee-management-system-employee-service:v2 employee-service

Notice Docker replaces one container at a time instead of stopping everything.

![image alt](https://github.com/ajaykumargk2k9/-Task-6-Docker-Swarm-Deployment-Cluster-Scaling-Scenario-/blob/main/Images/rolling%20service%20update.PNG?raw=true)

---

# Step 5 – Observe the Rolling Update

Run: docker service ps employee-service

You'll notice older tasks marked as Shutdown and new tasks created using the v2 image.

![image alt](https://github.com/ajaykumargk2k9/-Task-6-Docker-Swarm-Deployment-Cluster-Scaling-Scenario-/blob/main/Images/rolling%20service%20ps.PNG?raw=true)

---

# Step 6 – Verify in Browser

Open:

http://localhost:3001

Refresh 10–15 times.

You should notice:

✅ Version 2 displayed.

✅ Different container hostnames appearing.

✅ The page remains available during the update.

![image alt](https://github.com/ajaykumargk2k9/-Task-6-Docker-Swarm-Deployment-Cluster-Scaling-Scenario-/blob/main/Images/Rolling%20update%20enterprise%201.PNG?raw=true)

![image alt](https://github.com/ajaykumargk2k9/-Task-6-Docker-Swarm-Deployment-Cluster-Scaling-Scenario-/blob/main/Images/Rolling%20update%20enterprise%202.PNG?raw=true)

![image alt](https://github.com/ajaykumargk2k9/-Task-6-Docker-Swarm-Deployment-Cluster-Scaling-Scenario-/blob/main/Images/Rolling%20update%20enterprise%203.PNG?raw=true)

![image alt](https://github.com/ajaykumargk2k9/-Task-6-Docker-Swarm-Deployment-Cluster-Scaling-Scenario-/blob/main/Images/Rolling%20update%20enterprise%204.PNG?raw=true)

---

# Docker Stack (Enterprise Deployment)

# Before Docker Stack

Currently we've deployed only one service manually:

docker service create --name employee-service ...

If you have 30 microservices that's 30 commands.

Difficult to manage.

# Docker Stack Solves This

Instead of many commands we create one YAML file.

docker-stack.yml

and deploy everything using one command.

docker stack deploy -c docker-stack.yml employee-management

# Enterprise architecture

Our application will become like this

![image alt](https://github.com/ajaykumargk2k9/-Task-6-Docker-Swarm-Deployment-Cluster-Scaling-Scenario-/blob/main/Images/Enterprise%20architecture%20stack.PNG?raw=true)


# Step 1 – Create docker-stack.yml

Create a new file in the project root.

employee-management-system/

docker-stack.yml

# Paste the following

version: "3.9"

services:

  employee-service:
    image: employee-management-system-employee-service:v2
    networks:
      - employee-network
    ports:
      - "3001:3001"
    deploy:
      replicas: 3
      restart_policy:
        condition: any

  department-service:
    image: employee-management-system-department-service:latest
    networks:
      - employee-network
    ports:
      - "3002:3002"
    deploy:
      replicas: 2

  payroll-service:
    image: employee-management-system-payroll-service:latest
    networks:
      - employee-network
    ports:
      - "3003:3003"
    deploy:
      replicas: 2

  attendance-service:
    image: employee-management-system-attendance-service:latest
    networks:
      - employee-network
    ports:
      - "3004:3004"
    deploy:
      replicas: 2

  nginx:
    image: employee-management-system-nginx:latest
    networks:
      - employee-network
    ports:
      - "80:80"
    deploy:
      replicas: 1

networks:

  employee-network:
    external: true

# Why Are We Using external: true?

Because you already created:

docker network create \
--driver overlay \
employee-network

We don't want Docker Stack to create another overlay network.

Instead it uses the existing one.

# Step 2 – Remove Existing Swarm Service

Right now we already have

employee-service running.

Remove it.

docker service rm employee-service

Wait about 20 seconds.

Verify: docker service ls

It should be empty.

![image alt](https://github.com/ajaykumargk2k9/-Task-6-Docker-Swarm-Deployment-Cluster-Scaling-Scenario-/blob/main/Images/docker%20service%20ls%20stack.PNG?raw=true)

# Step 3 – Deploy the Stack

Run: docker stack deploy -c docker-stack.yml employee-management

# Step 4 – Verify Stack

Run: docker stack ls

# Step 5 – View Services

docker stack services employee-management

![image alt](https://github.com/ajaykumargk2k9/-Task-6-Docker-Swarm-Deployment-Cluster-Scaling-Scenario-/blob/main/Images/docker%20deploy%20verify%20view%20services%20stack.PNG?raw=true)

# Step 6 – View Tasks

docker stack ps employee-management

We'll see every container in the stack.

![image alt](https://github.com/ajaykumargk2k9/-Task-6-Docker-Swarm-Deployment-Cluster-Scaling-Scenario-/blob/main/Images/docker%20view%20tasks%20stack.PNG?raw=true)

---

# Docker Secrets (Enterprise Security)

# Why Docker Secrets?

Our application may have values like:

DB_HOST=mysql
DB_USER=root
DB_PASSWORD=admin123
JWT_SECRET=mysecretkey
API_KEY=123456789

# Where are these stored?

Usually in:

.env
docker-compose.yml
Dockerfile
GitHub

This is not secure because anyone with repository access can read them.

---

# Without Secrets:

GitHub Repository

        │
        
        ▼
        
docker-compose.yml

DB_PASSWORD=admin123 

Anyone can see the password.

---

# With Docker Secrets:

GitHub

      │
      
      ▼   
      
docker-stack.yml

DB_PASSWORD=/run/secrets/db_password 

           │
           
           ▼
           
Docker Swarm Secret Store

Only the running container can access the secret.

---

# Why Can't Use Environment Variables?

Because environment variables:

appear in docker inspect

can appear in logs

may be exposed through debugging tools

Secrets are mounted as temporary files under:

/run/secrets/

Only the container can read them.

---


