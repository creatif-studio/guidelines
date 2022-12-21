# Docker

Docker is a platform designed to help developers build, share, and run modern applications.

## Installation

Please run this command line by line:

Update the apt package index and install packages to allow apt to use a repository over HTTPS

```bash
sudo apt update -y && sudo apt install -y \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common
```

Add Docker’s official GPG key

```bash
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

Use the following command to set up the repository:

```bash
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

Update the apt package index

```bash
sudo apt update -y
```

Install Docker Engine, containerd, and Docker Compose

```bash
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

Allow users to run docker without sudo

```bash
sudo usermod -aG docker $(whoami)
```
