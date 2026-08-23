# Jenkins Installation on Ubuntu Server

## Overview

This document explains the complete installation and basic configuration of Jenkins on an Ubuntu server.

Jenkins is an open-source automation server used for implementing CI/CD pipelines, automating builds, testing, and deployments.

---

# Prerequisites

Before installing Jenkins, ensure:

- Ubuntu server is running
- User has sudo privileges
- Internet connectivity is available
- Required ports are allowed

---

# 1. Update Ubuntu Packages

Update the system package list and upgrade existing packages.

```bash
sudo apt update
sudo apt upgrade -y
```

---

# 2. Install Java

Jenkins requires Java to run. Install OpenJDK 17.

```bash
sudo apt install openjdk-17-jdk -y
```

Verify Java installation:

```bash
java -version
```

Expected output:

```
openjdk version "17.x.x"
```

---

# 3. Install Required Packages

Install required utilities for adding the Jenkins repository.

```bash
sudo apt install wget gnupg -y
```

---

# 4. Add Jenkins Repository Signing Key

Download the Jenkins GPG signing key.

```bash
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
https://pkg.jenkins.io/debian-stable/jenkins.io.key
```

Verify the key:

```bash
gpg --show-keys /usr/share/keyrings/jenkins-keyring.asc
```

Expected output should show Jenkins Project key information.

---

# 5. Add Jenkins Repository

Create the Jenkins repository configuration file.

```bash
sudo tee /etc/apt/sources.list.d/jenkins.list <<EOF
deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/
EOF
```

---

# 6. Update Package Repository

Refresh the package list.

```bash
sudo apt update
```

---

# 7. Install Jenkins

Install Jenkins package.

```bash
sudo apt install jenkins -y
```

---

# 8. Manage Jenkins Service

Start Jenkins service:

```bash
sudo systemctl start jenkins
```

Enable Jenkins to start automatically after reboot:

```bash
sudo systemctl enable jenkins
```

Check Jenkins status:

```bash
sudo systemctl status jenkins
```

Expected status:

```
Active: active (running)
```

---

# 9. Verify Jenkins Port

Jenkins runs on port **8080** by default.

Check whether Jenkins is listening:

```bash
sudo ss -tulnp | grep 8080
```

Expected output:

```
LISTEN 0 50 *:8080
```

---

# 10. Configure Firewall

If UFW firewall is enabled, allow Jenkins port.

Allow port 8080:

```bash
sudo ufw allow 8080
```

Check firewall status:

```bash
sudo ufw status
```

---

# 11. Access Jenkins Web Interface

Open a browser and access Jenkins:

```
http://<server-ip>:8080
```

Example:

```
http://192.168.1.100:8080
```

---

# 12. Get Initial Administrator Password

Retrieve the Jenkins initial admin password.

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Copy the password and enter it on the Jenkins setup page.

---

# 13. Complete Jenkins Setup

From the Jenkins web interface:

1. Enter initial administrator password
2. Select **Install suggested plugins**
3. Create an administrator user
4. Configure Jenkins URL
5. Complete installation

---

# Jenkins Service Management Commands

## Check Jenkins Status

```bash
sudo systemctl status jenkins
```

## Start Jenkins

```bash
sudo systemctl start jenkins
```

## Stop Jenkins

```bash
sudo systemctl stop jenkins
```

## Restart Jenkins

```bash
sudo systemctl restart jenkins
```

## Enable Jenkins at Boot

```bash
sudo systemctl enable jenkins
```

## Disable Jenkins at Boot

```bash
sudo systemctl disable jenkins
```

---

# Jenkins Logs

View Jenkins logs:

```bash
sudo journalctl -u jenkins -f
```

or

```bash
sudo tail -f /var/log/jenkins/jenkins.log
```

---

# Important Jenkins Directories

| Purpose | Location |
|---|---|
| Jenkins Home Directory | `/var/lib/jenkins` |
| Jenkins Configuration | `/var/lib/jenkins/config.xml` |
| Jenkins Plugins | `/var/lib/jenkins/plugins` |
| Jenkins Logs | `/var/log/jenkins/jenkins.log` |
| Jenkins Service File | `/lib/systemd/system/jenkins.service` |

---

# Install Docker for Jenkins CI/CD (Optional)

Docker is commonly used with Jenkins pipelines for building container images.

## Install Docker

```bash
sudo apt install docker.io -y
```

## Start Docker Service

```bash
sudo systemctl start docker
```

Enable Docker at boot:

```bash
sudo systemctl enable docker
```

---

## Add Jenkins User to Docker Group

Allow Jenkins to execute Docker commands.

```bash
sudo usermod -aG docker jenkins
```

Restart Jenkins:

```bash
sudo systemctl restart jenkins
```

---

## Verify Docker Access from Jenkins User

```bash
sudo -u jenkins docker ps
```

---

# Jenkins Installation Troubleshooting

## Check Jenkins Service

```bash
sudo systemctl status jenkins
```

## Check Jenkins Logs

```bash
sudo journalctl -u jenkins -f
```

## Check Port 8080

```bash
sudo ss -tulnp | grep 8080
```

## Restart Jenkins

```bash
sudo systemctl restart jenkins
```

---

# Conclusion

Jenkins is now installed and configured on the Ubuntu server. The server is ready for creating CI/CD pipelines, integrating source control systems, running automated builds, and deploying applications.
