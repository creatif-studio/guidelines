# Jenkins

Jenkins is an open source automation server which enables developers to reliably build, test, and deploy their software.

You can setup with 3 methods:

- Native: We can deploy manually with native app
- Docker Compose: We can deploy with docker compose
- Kubernetes: We can deploy jenkins with rancher

## Native

Requirements:

- Ubuntu Server 20.04
- OpenJDK 8/11
- Jenkins

Install OpenJDK 8 or 11, choose one:

```bash
sudo apt install -y openjdk-8-jdk
sudo apt install -y openjdk-11-jdk
```

Add jenkins repository

```bash
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo tee /usr/share/keyrings/jenkins-keyring.asc &gt; /dev/null
```

```bash
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list &gt; /dev/null
```

Install jenkins

```bash
sudo apt update -y && sudo apt install jenkins -y
```

### Docker Compose

- Create a new virtual machine
- Go to this directory `terry/developeroperations/resources/jenkins`

```bash
docker-compose up -d
```

## Kubernetes

- Make sure login to rancher
- Choose the cluster
- On the left open `workloads -> deployments`
- Create deployment
- Choose namespace `default`
- Insert name `jenkins`
- Choose tab `pod -> storage`
  - Choose `add volume -> bind-mount` for jenkins data
    - volume name: `jenkins-data`
    - path on node: `/mnt/jenkins-data`
  - Choose `add volume -> bind-mount` for docker daemon
    - volume name: `docker-data`
    - path on node: `/var/run/docker.sock`
- Choose tab `container-0 -> general`
  - Insert container name `jenkins`
  - Insert image `jenkins/jenkins:2.383`
  - Add ports for jenkins
    - service type: `node port`
    - name: `jenkins-port`
    - private service port: `8080`
  - Add ports for agents
    - service type: `node port`
    - name: `jenkins-agent-port`
    - private service port: `50000`
- Choose tab `container-0 -> security context`
  - Choose `privileged: yes`
- Choose tab `container-0 -> storage`
  - Choose `select volume -> jenkins-data` for jenkins data
    - mount point: `/var/jenkins_home`
  - Choose `select volume -> docker-data` for docker daemon
    - mount point: `/var/run/docker.sock`
- Click button `create`
