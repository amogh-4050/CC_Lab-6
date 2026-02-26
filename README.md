# CC_Lab-6 -- Jenkins + Docker + NGINX Load Balancing

## 📌 Overview

This lab demonstrates how to:

-   Build backend Docker images using Jenkins
-   Deploy multiple backend containers
-   Configure NGINX as a load balancer
-   Implement different load balancing strategies
-   Automate everything using Jenkins Pipeline

------------------------------------------------------------------------

## 🏗 Architecture

Jenkins → Builds Docker image → Deploys backend containers →\
NGINX → Load balances traffic across backends

------------------------------------------------------------------------

## 🧰 Prerequisites

-   Docker installed and running
-   Git installed
-   Public GitHub repository
-   Jenkins running inside Docker

------------------------------------------------------------------------

## 🚀 Step 1: Run Jenkins

### Build custom Jenkins image

``` bash
docker build -t jenkins-docker -f Dockerfile.jenkins .
```

### Run Jenkins container

``` bash
docker run -d -p 8080:8080 -p 50000:50000 -v jenkins_home:/var/jenkins_home -v /var/run/docker.sock:/var/run/docker.sock --user root --name jenkins jenkins-docker
```

Access Jenkins at:

http://localhost:8080

Get initial password:

``` bash
docker exec jenkins cat /var/jenkins_home/secrets/initialAdminPassword
```

------------------------------------------------------------------------

## ⚙️ Jenkins Freestyle Job (Task 2)

Execute Shell:

``` bash
docker build -t backend-app backend
```

------------------------------------------------------------------------

## 🔄 Parameterized Job (Task 3)

``` bash
docker rm -f backend1 backend2 || true

if [ "$BACKEND_COUNT" = "1" ]; then
    docker run -d --name backend1 backend-app
else
    docker run -d --name backend1 backend-app
    docker run -d --name backend2 backend-app
fi
```

------------------------------------------------------------------------

## 🧪 Jenkins Pipeline (Task 4)

Pipeline configured as:

-   Pipeline script from SCM
-   GitHub repo
-   Branch: \*/main
-   Script path: Jenkinsfile

------------------------------------------------------------------------

## 🌐 NGINX Load Balancer

### Round Robin (Default)

``` nginx
upstream backend_servers {
    server backend1:8080;
    server backend2:8080;
}
```

------------------------------------------------------------------------

### Least Connections

``` nginx
upstream backend_servers {
    least_conn;
    server backend1:8080;
    server backend2:8080;
}
```

------------------------------------------------------------------------

### IP Hash

``` nginx
upstream backend_servers {
    ip_hash;
    server backend1:8080;
    server backend2:8080;
}
```

------------------------------------------------------------------------

## 🌍 Access Application

If using port 8081:

http://localhost:8081

Refresh multiple times to observe load balancing behavior.

------------------------------------------------------------------------

## 🛑 Stopping Everything

Stop all containers:

``` bash
docker stop $(docker ps -q)
```

Remove lab containers:

``` bash
docker rm -f backend1 backend2 nginx-lb jenkins
```

------------------------------------------------------------------------

## 🧠 Key Learnings

-   Jenkins can automate Docker builds
-   Pipelines provide better control than Freestyle jobs
-   Docker socket mounting enables Jenkins to control host Docker
-   NGINX can distribute traffic using:
    -   Round Robin
    -   Least Connections
    -   IP Hash

------------------------------------------------------------------------

## 👨‍💻 Author

Amogh Singh
