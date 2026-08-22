
# Jenkins Installation Common Errors and Solutions on Ubuntu

This document covers common errors encountered while installing Jenkins on Ubuntu and their solutions.

---

# Error 1: Jenkins Repository NO_PUBKEY Error

## Error Message

```
The following signatures couldn't be verified:
NO_PUBKEY 7198F4B714ABFC68

The repository 'https://pkg.jenkins.io/debian-stable binary/ Release' is not signed.
```

---

## Cause

The Jenkins repository signing key is:

- Missing
- Incorrectly installed
- Expired

APT cannot verify that the Jenkins packages are from a trusted source.

---

## Solution

### Step 1: Remove old Jenkins key

```bash
sudo rm -f /usr/share/keyrings/jenkins-keyring.asc
```

---

### Step 2: Download the latest Jenkins GPG key

```bash
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
https://pkg.jenkins.io/debian-stable/jenkins.io.key
```

---

### Step 3: Verify the key

```bash
gpg --show-keys /usr/share/keyrings/jenkins-keyring.asc
```

Expected:

```
pub rsa4096 Jenkins Project
uid Jenkins Project <jenkinsci-board@googlegroups.com>
```

---

### Step 4: Update repository

```bash
sudo apt update
```

---

# Error 2: Jenkins GPG Key Expired

## Error Message

```
pub rsa4096 2023-03-27 [SC]
expired: 2026-03-26

Jenkins Project <jenkinsci-board@googlegroups.com>
```

---

## Cause

The installed Jenkins signing key has expired.

APT blocks repository access because it cannot verify package signatures.

---

## Solution

Remove the expired key:

```bash
sudo rm -f /usr/share/keyrings/jenkins-keyring.asc
```

Download the updated key:

```bash
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
https://pkg.jenkins.io/debian-stable/jenkins.io.key
```

Verify:

```bash
gpg --show-keys /usr/share/keyrings/jenkins-keyring.asc
```

Update package list:

```bash
sudo apt update
```

---

# Error 3: Jenkins Service Not Starting

## Error Message

```
Job for jenkins.service failed because the control process exited with error code.
```

or

```
jenkins.service failed
```

---

## Cause

Common reasons:

- Java missing
- Incorrect Java version
- Port conflict
- Permission issue
- Configuration error

---

## Solution

### Check Jenkins service status

```bash
sudo systemctl status jenkins
```

---

### Check detailed logs

```bash
sudo journalctl -u jenkins -xe
```

---

### Verify Java version

```bash
java -version
```

Jenkins requires supported Java versions.

Example:

```
openjdk version "17.x.x"
```

---

Restart Jenkins:

```bash
sudo systemctl restart jenkins
```

---

# Error 4: Port 8080 Already in Use

## Error Message

```
Address already in use: bind
Port 8080 is already occupied
```

---

## Cause

Another application is already running on port 8080.

---

## Solution

Check which process is using port 8080:

```bash
sudo ss -tulnp | grep 8080
```

Example:

```
LISTEN 0 128 *:8080 users:(("java",pid=1234))
```

Stop the conflicting process:

```bash
sudo kill -9 <PID>
```

Restart Jenkins:

```bash
sudo systemctl restart jenkins
```

---

# Error 5: Cannot Access Jenkins Web UI

## Error Message

Browser shows:

```
This site cannot be reached
Connection refused
```

---

## Cause

Possible reasons:

- Jenkins service stopped
- Firewall blocking port 8080
- Security group blocking traffic
- Wrong server IP

---

## Solution

### Check Jenkins status

```bash
sudo systemctl status jenkins
```

---

### Check Jenkins port

```bash
sudo ss -tulnp | grep 8080
```

---

### Allow firewall port

```bash
sudo ufw allow 8080
```

---

### AWS EC2 Security Group

Allow inbound rule:

```
Type: Custom TCP
Port: 8080
Source: Your IP or Required Network
```

---

# Error 6: Unable to Get Initial Admin Password

## Error Message

```
cat: /var/lib/jenkins/secrets/initialAdminPassword:
No such file or directory
```

---

## Cause

Jenkins installation is incomplete or Jenkins has not started successfully.

---

## Solution

Check Jenkins status:

```bash
sudo systemctl status jenkins
```

Restart Jenkins:

```bash
sudo systemctl restart jenkins
```

Check Jenkins directory:

```bash
ls -la /var/lib/jenkins/secrets/
```

Get password:

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

---

# Error 7: Java Not Found

## Error Message

```
java: command not found
```

---

## Cause

Java is not installed.

---

## Solution

Install Java:

```bash
sudo apt install openjdk-17-jdk -y
```

Verify:

```bash
java -version
```

---

# Error 8: Jenkins Package Not Found

## Error Message

```
E: Unable to locate package jenkins
```

---

## Cause

Jenkins repository was not added correctly.

---

## Solution

Check repository file:

```bash
cat /etc/apt/sources.list.d/jenkins.list
```

Expected:

```
deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/
```

Update:

```bash
sudo apt update
```

Install Jenkins:

```bash
sudo apt install jenkins -y
```

---

# Error 9: Jenkins Cannot Execute Docker Commands

## Error Message

```
permission denied while trying to connect to Docker daemon socket
```

---

## Cause

Jenkins user does not have Docker permissions.

---

## Solution

Add Jenkins user to Docker group:

```bash
sudo usermod -aG docker jenkins
```

Restart Jenkins:

```bash
sudo systemctl restart jenkins
```

Verify:

```bash
sudo -u jenkins docker ps
```

---

# Error 10: Jenkins Upgrade or Plugin Failure

## Symptoms

- Jenkins UI not loading
- Plugin installation failed
- Build failures after upgrade

---

## Solution

Check Jenkins logs:

```bash
sudo journalctl -u jenkins -f
```

Restart Jenkins:

```bash
sudo systemctl restart jenkins
```

Backup Jenkins home:

```bash
sudo cp -r /var/lib/jenkins /backup/jenkins
```

---

# Important Troubleshooting Commands

## Jenkins Status

```bash
sudo systemctl status jenkins
```

## Jenkins Logs

```bash
sudo journalctl -u jenkins -f
```

## Check Port

```bash
sudo ss -tulnp | grep 8080
```

## Restart Jenkins

```bash
sudo systemctl restart jenkins
```

## Check Java

```bash
java -version
```

## Check Installed Jenkins Version

```bash
jenkins --version
```

---

# Summary Table

| Error | Root Cause | Solution |
|---|---|---|
| NO_PUBKEY | Missing/expired GPG key | Install latest Jenkins key |
| Jenkins service failed | Java/config issue | Check logs and Java version |
| Port 8080 busy | Another service using port | Stop process or change port |
| UI not accessible | Firewall/security group | Open port 8080 |
| Password file missing | Jenkins not initialized | Restart Jenkins |
| Package not found | Repository issue | Add Jenkins repo correctly |
| Docker permission denied | Jenkins lacks Docker access | Add Jenkins user to docker group |

---

This troubleshooting guide covers the most common Jenkins installation issues faced during DevOps setup on Ubuntu servers.
