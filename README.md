## Ansible Integration in Jenkins

This project was a part of the TWN DevOps Bootcamp.

Project demonstrates a complete CI/CD pipeline integrating **Jenkins** and **Ansible** to automatically configure **AWS EC2 instances**

## Technologies used:
![img.png](img.png) 

## Project Description:
- Create and configure a dedicated server for Jenkins
- Create and configure a dedicated server for Ansible Control Node
- Write Ansible Playbook, which configures 2 EC2 Instances
- Add ssh key file credentials in Jenkins for Ansible Control Node server and Ansible Managed Node
  servers
- Configure Jenkins to execute the Ansible Playbook on remote Ansible Control Node server as
  part of the CI/CD pipeline
- Jenkinsfile configuration will do the following:
 a) Connect to the remote Ansible Control Node server
 b) Copy Ansible playbook and configuration files to the remote Ansible Control Node server
 c) Copy the ssh keys for the Ansible Managed Node servers to the Ansible Control Node server
 d) Install Ansible, Python3 and Boto3 on the Ansible Control Node server
 e) With everything installed and copied to the remote Ansible Control Node server, execute the
  playbook remotely on that Control Node that will configure the 2 EC2 Managed Nodes


A Java Spring Boot application with a fully automated CI/CD pipeline that uses **Jenkins** to orchestrate **Ansible**, which provisions and configures **AWS EC2** instances dynamically.

## Overview

```
Jenkins Pipeline
  ├── Stage 1: Copy Ansible files → Ansible Control Node (via SSH/SCP)
  └── Stage 2: Execute Ansible Playbook → EC2 instances (Docker + Docker Compose)
```

The pipeline connects to a dedicated Ansible control node, uploads the playbook and credentials, then triggers the playbook remotely. Ansible uses the **dynamic AWS EC2 inventory plugin** to discover target instances automatically — no static host lists required.

## Tech Stack

| Layer | Technology |
|---|---|
| Application | Java 8, Spring Boot 2.3, Maven |
| CI/CD | Jenkins (Declarative Pipeline) |
| Configuration Management | Ansible |
| Cloud | AWS EC2 (ap-southeast-2) |
| Container Runtime | Docker, Docker Compose |

## Repository Structure

```
.
├── Jenkinsfile                   # Pipeline definition
├── pom.xml                       # Maven build descriptor
├── ansible-server.sh             # Bootstrap script for the Ansible control node
├── ansible/
│   ├── ansible.cfg               # Ansible configuration (dynamic inventory, SSH key)
│   ├── inventory_aws_ec2.yaml    # AWS EC2 dynamic inventory plugin config
│   └── my-playbook.yaml          # Playbook: install Docker & Docker Compose on EC2s
└── target/
    └── classes/static/index.html # Static frontend served by Spring Boot
```

## Pipeline Stages

### Stage 1 — Copy files to Ansible server

Copies the entire `ansible/` directory and the EC2 SSH private key to the Ansible control node using `scp`.

Credentials used:
- `ansible-server-key` — SSH key for the Ansible control node
- `ec2-server-key` — SSH key for the EC2 target instances (copied as `ssh-key.pem`)

### Stage 2 — Execute Ansible playbook

Connects to the Ansible control node over SSH and:
1. Runs `ansible-server.sh` — installs Ansible and `python3-boto3` (required by the EC2 inventory plugin)
2. Runs `ansible-playbook my-playbook.yaml` — configures all discovered EC2 instances

## Ansible Playbook

The playbook (`my-playbook.yaml`) runs two plays against all dynamically discovered EC2 hosts:

1. **Install Docker** — uses `yum` to install and start the Docker daemon via `systemd`
2. **Install Docker Compose** — creates the CLI plugin directory, detects the host architecture, and downloads the correct `docker-compose` binary from GitHub Releases

## Dynamic Inventory

The inventory plugin (`inventory_aws_ec2.yaml`) discovers EC2 instances in `ap-southeast-2` and groups them by:
- EC2 tags (`tag_*`)
- Instance type (`instance_type_*`)

This allows the playbook to target specific instance groups without hardcoding IP addresses.

## Prerequisites

- Jenkins with the following plugins: **SSH Agent**, **SSH Pipeline Steps**
- Jenkins credentials:
  - `ansible-server-key` (SSH private key for the Ansible control node)
  - `ec2-server-key` (SSH private key for EC2 instances)
- Ansible control node reachable at the IP defined in `ANSIBLE_SERVER`
- AWS credentials configured on the Ansible control node (for the EC2 inventory plugin)
- EC2 instances tagged appropriately for Ansible to discover them

## Application

The Spring Boot application is a minimal web app that serves a static HTML page. It is built with Maven:

```bash
mvn package
```

The resulting JAR is a self-contained executable bundled by the `spring-boot-maven-plugin`.
