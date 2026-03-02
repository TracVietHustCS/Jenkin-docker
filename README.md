# Install Jenkins using Docker Compose

This repository contains a Docker Compose configuration for a quick installation of Jenkins. This setup is not intended for production systems.

Credits: This approach is mostly based on the [official instructions](https://www.jenkins.io/doc/book/installing/docker/) but takes advantage of Docker Compose (by using a `docker-compose.yml` file) to reduce the number of steps needed to get Jenkins up and running.

# Docker Installation

## Step 0

Install Docker locally (probably using Docker Desktop is the easiest approach).

## Step 1

Clone this repository or download its contents. 

## Step 2

Open a terminal window in the same directory where the `Dockerfile` from this repository is located. Build the Jenkins Docker image:

```
docker build -t my-jenkins .
```

## Step 3

Start Jenkins:

```
docker compose up -d
```

## Step 4

Open Jenkins by going to: [http://localhost:8080/](http://localhost:8080/) and finish the installation process.

## Step 5

If you wish to stop Jenkins and get back to it later, run:

```
docker compose down
```

If you wish to start Jenkins again later, just run the same command from Step 3.


# Removing Jenkins

Once you are done playing with Jenkins, maybe it is time to clean things up.

Run the following command to terminate Jenkins and to remove all volumes and images used:

```
docker compose down --volumes --rmi all 
```

digram 

```
+-----------------------------------------------------------------------------------+
|  HOST MACHINE (Máy chủ vật lý / Laptop của bạn)                                   |
|                                                                                   |
|   [Port 8080] <---(Truy cập Web Jenkins)          [Port 2376] <---(Docker TLS)    |
|   [Port 50000]<---(Kết nối Jenkins Agent)              |                          |
|         |                                              |                          |
| +-------|----------------------------------------------|------------------------+ |
| |       v  DOCKER NETWORK: 'jenkins' (Bridge)          v                        | |
| |  +--------------------------------+     +--------------------------------+    | |
| |  | CONTAINER: my-jenkins          |     | CONTAINER: jenkins-docker      |    | |
| |  | (Jenkins Web & Master)         |     | (Docker-in-Docker Daemon)      |    | |
| |  |--------------------------------|     |--------------------------------|    | |
| |  | Mở port: 8080, 50000           |     | Mở port: 2376                  |    | |
| |  |                                |     |                                |    | |
| |  | ENV:                           |     | ENV:                           |    | |
| |  | - DOCKER_HOST=...              |     | - DOCKER_TLS_CERTDIR=/certs    |    | |
| |  +--|--------------------------|--+     +--|--------------------------|--+    | |
| |     |                          |           |                          |       | |
| +-----|--------------------------|-----------|--------------------------|-------+ |
|       |                          |           |                          |         |
| ==== VOLUMES (Lưu trữ dữ liệu) ==|===========|==========================|======== |
|       |                          v           v                          |         |
|       |     [Volume: jenkins-docker-certs]                              |         |
|       +---> (Đọc certs - ro)                 (Tạo & ghi certs) <--------+         |
|                                                                                   |
|             [Volume: jenkins-data]                                                |
|             (Lưu workspace, cấu hình job...)                                      |
+-----------------------------------------------------------------------------------+
```