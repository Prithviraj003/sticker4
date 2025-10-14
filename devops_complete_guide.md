# DevOps Sets 4, 5 & 6 - Complete Guide with Prerequisites

## Table of Contents
- [Initial System Setup](#initial-system-setup)
- [Common Prerequisites Installation](#common-prerequisites-installation)
- [Set 4 Tasks](#set-4-tasks)
- [Set 5 Tasks](#set-5-tasks)
- [Set 6 Tasks](#set-6-tasks)

---

## Initial System Setup

### System Requirements
- **Operating System**: Ubuntu 20.04 LTS or later (recommended)
- **RAM**: Minimum 2GB (4GB recommended)
- **Disk Space**: At least 20GB free
- **Internet Connection**: Required for downloading packages
- **User Privileges**: sudo access required

### Update System Packages
```bash
# Update package list
sudo apt update

# Upgrade installed packages
sudo apt upgrade -y

# Install basic utilities
sudo apt install -y curl wget vim nano net-tools
```

---

## Common Prerequisites Installation

### 1. Installing Ansible

**Step 1: Update and Install Dependencies**
```bash
# Update package index
sudo apt update

# Install software-properties-common (needed to add PPA)
sudo apt install -y software-properties-common
```

**Step 2: Add Ansible PPA Repository**
```bash
# Add Ansible repository
sudo add-apt-repository --yes --update ppa:ansible/ansible

# If prompted, press ENTER to continue
```

**Step 3: Install Ansible**
```bash
# Install Ansible
sudo apt install -y ansible

# Verify installation
ansible --version

# Expected output:
# ansible [core 2.xx.x]
#   config file = /etc/ansible/ansible.cfg
#   ...
```

**Step 4: Configure Ansible**
```bash
# Create ansible configuration directory if not exists
sudo mkdir -p /etc/ansible

# Create inventory file
sudo nano /etc/ansible/hosts

# Add this basic configuration (replace with your IPs):
[local]
localhost ansible_connection=local

# Save and exit (Ctrl+X, Y, Enter)
```

**Step 5: Test Ansible**
```bash
# Test local connection
ansible localhost -m ping

# Expected output:
# localhost | SUCCESS => {
#     "changed": false,
#     "ping": "pong"
# }
```

---

### 2. Installing Docker

**Step 1: Remove Old Docker Versions (if any)**
```bash
# Remove old versions
sudo apt remove -y docker docker-engine docker.io containerd runc
```

**Step 2: Install Prerequisites**
```bash
# Install required packages
sudo apt install -y \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
```

**Step 3: Add Docker's Official GPG Key**
```bash
# Create directory for keyrings
sudo mkdir -p /etc/apt/keyrings

# Download and add Docker's GPG key
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

**Step 4: Add Docker Repository**
```bash
# Set up the repository
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

**Step 5: Install Docker Engine**
```bash
# Update package index
sudo apt update

# Install Docker Engine, CLI, and containerd
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# Verify installation
docker --version

# Expected output: Docker version 24.x.x, build xxxxx
```

**Step 6: Configure Docker (Post-Installation)**
```bash
# Add your user to docker group (avoid using sudo)
sudo usermod -aG docker $USER

# Apply group changes (or logout and login)
newgrp docker

# Enable Docker to start on boot
sudo systemctl enable docker

# Start Docker service
sudo systemctl start docker

# Verify Docker is running
sudo systemctl status docker

# Test Docker without sudo
docker run hello-world

# Expected output: "Hello from Docker!" message
```

**Step 7: Verify Docker Installation**
```bash
# Check Docker info
docker info

# List Docker images
docker images

# List running containers
docker ps
```

---

### 3. Installing Git

**Step 1: Install Git**
```bash
# Install Git
sudo apt install -y git

# Verify installation
git --version

# Expected output: git version 2.xx.x
```

**Step 2: Configure Git**
```bash
# Set your name (replace with your actual name)
git config --global user.name "Your Full Name"

# Set your email (use the same email as GitHub)
git config --global user.email "your.email@example.com"

# Set default branch name to 'main'
git config --global init.defaultBranch main

# Set default text editor
git config --global core.editor nano

# Verify configuration
git config --list

# View specific settings
git config --global user.name
git config --global user.email
```

**Step 3: Generate SSH Key (for GitHub)**
```bash
# Generate SSH key pair
ssh-keygen -t ed25519 -C "your.email@example.com"

# Press ENTER to accept default location (~/.ssh/id_ed25519)
# Enter passphrase (optional, press ENTER for no passphrase)

# Start SSH agent
eval "$(ssh-agent -s)"

# Add SSH key to agent
ssh-add ~/.ssh/id_ed25519

# Display public key (copy this entire output)
cat ~/.ssh/id_ed25519.pub

# Output will look like:
# ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAA... your.email@example.com
```

**Step 4: Add SSH Key to GitHub**
```bash
# Instructions (do this in browser):
# 1. Go to: https://github.com/settings/keys
# 2. Click "New SSH key"
# 3. Title: "My Ubuntu Server"
# 4. Key type: "Authentication Key"
# 5. Paste the public key you copied from previous step
# 6. Click "Add SSH key"
```

**Step 5: Test SSH Connection to GitHub**
```bash
# Test GitHub connection
ssh -T git@github.com

# First time will ask: "Are you sure you want to continue connecting?"
# Type: yes

# Expected output:
# Hi your-username! You've successfully authenticated, but GitHub does not provide shell access.
```

**Step 6: Create GitHub Personal Access Token**
```bash
# Instructions (do this in browser):
# 1. Go to: https://github.com/settings/tokens
# 2. Click "Generate new token" → "Generate new token (classic)"
# 3. Note: "Terminal Git Access"
# 4. Expiration: Choose duration (30 days, 90 days, etc.)
# 5. Select scopes:
#    ☑ repo (Full control of private repositories)
#    ☑ workflow (if using GitHub Actions)
# 6. Click "Generate token"
# 7. COPY THE TOKEN IMMEDIATELY (format: ghp_xxxxxxxxxxxx...)
# 8. Save it securely - you'll use this as password for HTTPS operations
```

---

### 4. Installing Jenkins

**Step 1: Install Java (Jenkins Requirement)**
```bash
# Install OpenJDK 11 or 17
sudo apt install -y openjdk-11-jdk

# Verify Java installation
java -version

# Expected output:
# openjdk version "11.0.x" 2023-xx-xx
```

**Step 2: Add Jenkins Repository**
```bash
# Add Jenkins GPG key
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null

# Add Jenkins repository
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null

# Update package list
sudo apt update
```

**Step 3: Install Jenkins**
```bash
# Install Jenkins
sudo apt install -y jenkins

# Start Jenkins service
sudo systemctl start jenkins

# Enable Jenkins to start on boot
sudo systemctl enable jenkins

# Check Jenkins status
sudo systemctl status jenkins

# Expected output: Active: active (running)
```

**Step 4: Configure Firewall (if UFW is enabled)**
```bash
# Check if UFW is active
sudo ufw status

# If active, allow Jenkins port
sudo ufw allow 8080
sudo ufw reload
```

**Step 5: Access Jenkins Web Interface**
```bash
# Get initial admin password
sudo cat /var/lib/jenkins/secrets/initialAdminPassword

# Copy the password output (long alphanumeric string)

# Open browser and navigate to:
# http://your-server-ip:8080
# or
# http://localhost:8080

# Steps in browser:
# 1. Paste the initial admin password
# 2. Click "Continue"
# 3. Select "Install suggested plugins" (wait for installation)
# 4. Create First Admin User:
#    - Username: admin
#    - Password: your-secure-password
#    - Full name: Your Name
#    - Email: your.email@example.com
# 5. Click "Save and Continue"
# 6. Jenkins URL: Keep default (http://your-server-ip:8080)
# 7. Click "Save and Finish"
# 8. Click "Start using Jenkins"
```

**Step 6: Install Maven in Jenkins**
```bash
# In Jenkins Web UI:
# 1. Click "Manage Jenkins" (left sidebar)
# 2. Click "Global Tool Configuration"
# 3. Scroll to "Maven" section
# 4. Click "Add Maven"
# 5. Configuration:
#    - Name: Maven-3.9
#    - Check "Install automatically"
#    - Install from Apache: Choose latest version (3.9.x)
# 6. Click "Save"
```

**Step 7: Verify Jenkins Installation**
```bash
# Check Jenkins service
sudo systemctl status jenkins

# View Jenkins logs
sudo journalctl -u jenkins -f

# Check Jenkins port
sudo netstat -tulpn | grep 8080

# Test Jenkins API (should return HTML)
curl http://localhost:8080
```

---

### 5. Installing Maven (for CLI usage)

**Step 1: Install Maven**
```bash
# Install Maven
sudo apt install -y maven

# Verify installation
mvn --version

# Expected output:
# Apache Maven 3.6.x or higher
# Maven home: /usr/share/maven
# Java version: 11.0.x
```

**Step 2: Configure Maven (Optional)**
```bash
# Create .m2 directory
mkdir -p ~/.m2

# Configure Maven settings (optional)
nano ~/.m2/settings.xml

# Add basic settings:
<settings>
  <localRepository>${user.home}/.m2/repository</localRepository>
</settings>

# Save and exit
```

**Step 3: Test Maven**
```bash
# Create test Maven project
mvn archetype:generate -DgroupId=com.example -DartifactId=test-app \
  -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

# Navigate to project
cd test-app

# Build the project
mvn clean install

# Expected output: BUILD SUCCESS
```

---

### 6. Setting Up SSH for Ansible

**Step 1: Generate SSH Key (if not already done)**
```bash
# Generate SSH key
ssh-keygen -t rsa -b 4096 -C "ansible-control"

# Press ENTER to accept default location (~/.ssh/id_rsa)
# Press ENTER twice for no passphrase (or set one)
```

**Step 2: Copy SSH Key to Target Servers**
```bash
# Replace with your target server IP/hostname
ssh-copy-id username@target-server-ip

# Example:
ssh-copy-id ubuntu@192.168.1.100

# Enter password when prompted
# This copies your public key to target server's ~/.ssh/authorized_keys
```

**Step 3: Test SSH Connection**
```bash
# SSH to target without password
ssh username@target-server-ip

# Should log in without password
# Exit with: exit
```

**Step 4: Configure Ansible Inventory**
```bash
# Edit inventory
sudo nano /etc/ansible/hosts

# Add target servers:
[webservers]
192.168.1.100 ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/id_rsa

[loadbalancers]
192.168.1.101 ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/id_rsa

# Save and exit
```

**Step 5: Test Ansible Connection**
```bash
# Test all hosts
ansible all -m ping

# Test specific group
ansible webservers -m ping

# Expected output: SUCCESS with "ping": "pong"
```

---

### 7. Creating Docker Hub Account

**Step 1: Sign Up**
```bash
# Instructions (do in browser):
# 1. Go to: https://hub.docker.com
# 2. Click "Sign Up"
# 3. Fill in:
#    - Docker ID (username): choose carefully, can't be changed
#    - Email: your email address
#    - Password: strong password
# 4. Complete CAPTCHA
# 5. Click "Sign Up"
```

**Step 2: Verify Email**
```bash
# 1. Check your email inbox
# 2. Click verification link in email from Docker
# 3. Confirm email verification
```

**Step 3: Login via CLI**
```bash
# Login to Docker Hub from terminal
docker login

# Enter Docker Hub username
# Enter Docker Hub password

# Expected output: Login Succeeded
```

**Step 4: Create Access Token (More Secure)**
```bash
# Instructions (do in browser):
# 1. Go to: https://hub.docker.com/settings/security
# 2. Click "New Access Token"
# 3. Description: "CLI Access"
# 4. Access permissions: "Read, Write, Delete"
# 5. Click "Generate"
# 6. COPY THE TOKEN (you won't see it again)
# 7. Use this token as password when doing 'docker login'
```

**Step 5: Test Docker Hub**
```bash
# Tag a test image
docker pull hello-world
docker tag hello-world:latest your-dockerhub-username/test:latest

# Push to Docker Hub
docker push your-dockerhub-username/test:latest

# Verify in browser:
# Go to: https://hub.docker.com/u/your-dockerhub-username
# You should see 'test' repository
```

---

### 8. Creating GitHub Account

**Step 1: Sign Up**
```bash
# Instructions (do in browser):
# 1. Go to: https://github.com
# 2. Click "Sign up"
# 3. Enter email address
# 4. Create password
# 5. Choose username (this will be your GitHub URL)
# 6. Choose email preferences
# 7. Complete CAPTCHA
# 8. Click "Create account"
# 9. Verify email (check inbox)
```

**Step 2: Configure GitHub Profile**
```bash
# 1. Go to: https://github.com/settings/profile
# 2. Add:
#    - Name (optional)
#    - Bio (optional)
#    - Profile picture (optional)
# 3. Click "Update profile"
```

**Step 3: Set Up Two-Factor Authentication (Recommended)**
```bash
# 1. Go to: https://github.com/settings/security
# 2. Click "Enable two-factor authentication"
# 3. Choose: "Set up using an app" (recommended)
# 4. Use app like Google Authenticator or Authy
# 5. Scan QR code with app
# 6. Enter 6-digit code from app
# 7. Save recovery codes securely
```

---

## Verification Checklist

Run these commands to verify all prerequisites are installed:

```bash
# Check all installations
echo "=== Ansible ==="
ansible --version | head -1

echo -e "\n=== Docker ==="
docker --version

echo -e "\n=== Git ==="
git --version

echo -e "\n=== Java ==="
java -version 2>&1 | head -1

echo -e "\n=== Maven ==="
mvn --version | head -1

echo -e "\n=== Jenkins ==="
sudo systemctl is-active jenkins

echo -e "\n=== Docker Hub Login ==="
cat ~/.docker/config.json | grep -q "auth" && echo "Logged in" || echo "Not logged in"

echo -e "\n=== GitHub SSH ==="
ssh -T git@github.com 2>&1 | grep "successfully authenticated" && echo "SSH configured" || echo "SSH not configured"

echo -e "\n=== User Groups ==="
groups $USER | grep docker && echo "User in docker group" || echo "User NOT in docker group"
```

---

## Common Troubleshooting

### Ansible Issues

**Problem: Command not found**
```bash
# Solution: Reinstall Ansible
sudo apt update
sudo apt install -y ansible
```

**Problem: Permission denied (SSH)**
```bash
# Solution: Copy SSH key to target
ssh-copy-id username@target-ip
# Ensure correct permissions
chmod 700 ~/.ssh
chmod 600 ~/.ssh/id_rsa
```

### Docker Issues

**Problem: Permission denied**
```bash
# Solution: Add user to docker group
sudo usermod -aG docker $USER
newgrp docker
# Or logout and login again
```

**Problem: Docker daemon not running**
```bash
# Solution: Start Docker service
sudo systemctl start docker
sudo systemctl enable docker
```

### Git/GitHub Issues

**Problem: Authentication failed**
```bash
# Solution 1: Use Personal Access Token instead of password
# Generate token at: https://github.com/settings/tokens
# Use token as password

# Solution 2: Use SSH instead of HTTPS
# Change remote URL:
git remote set-url origin git@github.com:username/repo.git
```

**Problem: SSH key not working**
```bash
# Solution: Add key to agent
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
# Test connection
ssh -T git@github.com
```

### Jenkins Issues

**Problem: Cannot access Jenkins on port 8080**
```bash
# Solution 1: Check if Jenkins is running
sudo systemctl status jenkins

# Solution 2: Check firewall
sudo ufw allow 8080
sudo ufw reload

# Solution 3: Find Jenkins port
sudo netstat -tulpn | grep jenkins
```

**Problem: Forgot initial admin password**
```bash
# Solution: Retrieve password
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

---

# Set 4 Tasks

## Set 4 - Q1: Ansible Playbook to install and start Nginx

**Expected Output:** Nginx installed, service running

### Prerequisites Verified:
- ✅ Ansible installed
- ✅ SSH access configured
- ✅ Target server accessible

### Step-by-Step Instructions:

#### 1. Set Up Ansible Inventory
```bash
# Create inventory file
sudo nano /etc/ansible/hosts

# Add your target server (replace with actual IP)
[webservers]
192.168.1.100 ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/id_rsa
```

#### 2. Test Connectivity
```bash
# Ping the target host
ansible webservers -m ping
```

#### 3. Create the Playbook
```bash
# Create a directory for your playbooks
mkdir -p ~/ansible-playbooks
cd ~/ansible-playbooks

# Create the playbook file
nano nginx-install.yml
```

#### 4. Write the Playbook Script
Copy and paste this into `nginx-install.yml`:

```yaml
---
- name: Install and start Nginx web server
  hosts: webservers
  become: yes
  tasks:
    - name: Update apt repository cache
      apt:
        update_cache: yes
    
    - name: Install Nginx
      apt:
        name: nginx
        state: present
    
    - name: Start and enable Nginx service
      service:
        name: nginx
        state: started
        enabled: yes
```

#### 5. Execute the Playbook
```bash
# Run the playbook
ansible-playbook nginx-install.yml

# Or with verbose output
ansible-playbook nginx-install.yml -v
```

#### 6. Verify the Result
```bash
# Check if Nginx is running on target server
ansible webservers -m shell -a "systemctl status nginx"

# Check from browser
# Open: http://192.168.1.100
# You should see "Welcome to nginx!" page

# Or use curl from control node
curl http://192.168.1.100
```

---

## Set 4 - Q2: Run the Docker container (image built in Jenkins pipeline) and display logs

**Expected Output:** Container running and producing expected output.

### Prerequisites Verified:
- ✅ Docker installed and running
- ✅ User in docker group
- ✅ Docker image available

### Step-by-Step Instructions:

#### 1. Log into Your Server
```bash
ssh user@your-server
```

#### 2. Verify Docker Installation
```bash
# Check Docker version
docker --version

# Check Docker service status
sudo systemctl status docker
```

#### 3. List Available Images
```bash
# List all Docker images
docker images

# Output will look like:
# REPOSITORY          TAG       IMAGE ID       CREATED         SIZE
# myapp              latest    abc123def456   2 hours ago     200MB
```

#### 4. Run the Container
```bash
# Run container in detached mode
docker run -d --name myapp myapp:latest

# Run with port mapping (if it's a web app)
docker run -d --name myapp -p 8080:80 myapp:latest
```

#### 5. Verify Container is Running
```bash
# List running containers
docker ps

# Should show:
# CONTAINER ID   IMAGE          COMMAND                  STATUS         PORTS
# xyz789abc123   myapp:latest   "/docker-entrypoint.…"   Up 10 seconds  8080->80/tcp
```

#### 6. Display Container Logs
```bash
# Show all logs
docker logs myapp

# Follow logs in real-time
docker logs -f myapp

# Show last 50 lines
docker logs --tail 50 myapp
```

#### 7. Additional Verification
```bash
# Check container details
docker inspect myapp

# Access container shell (if needed)
docker exec -it myapp /bin/bash

# Stop container (when done)
docker stop myapp

# Remove container
docker rm myapp
```

---

## Set 4 - Q3: Manual Maven build in Jenkins (no pipeline)

**Expected Output:** Maven project builds successfully and shows "BUILD SUCCESS" in the Jenkins console.

### Prerequisites Verified:
- ✅ Jenkins installed and running
- ✅ Maven configured in Jenkins
- ✅ Git repository accessible

### Step-by-Step Instructions:

#### 1. Configure Maven in Jenkins (One-time setup)
```bash
# In Jenkins UI:
# 1. Go to "Manage Jenkins" → "Global Tool Configuration"
# 2. Scroll to "Maven" section
# 3. Click "Add Maven"
# 4. Name: "Maven-3.9"
# 5. Check "Install automatically" OR specify MAVEN_HOME path
# 6. Click "Save"
```

#### 2. Create New Maven Job

**Option A: Using Maven Project (Recommended)**

1. **Create New Job**
   - Go to Jenkins Dashboard
   - Click "New Item"
   - Enter job name: `MyMavenBuild`
   - Select "Maven project"
   - Click "OK"

2. **Configure Source Code Management**
   - Scroll to "Source Code Management" section
   - Select "Git"
   - Enter Repository URL: `https://github.com/your-username/your-maven-project.git`
   - Add credentials if repository is private (click "Add" → "Jenkins")
   - Specify branch: `*/main` or `*/master`

3. **Build Configuration**
   - Scroll to "Build" section
   - In "Root POM" field: `pom.xml` (default)
   - In "Goals and options" field, enter:
     ```
     clean install
     ```

4. **Optional: Archive Artifacts**
   - Scroll to "Post-build Actions"
   - Click "Add post-build action"
   - Select "Archive the artifacts"
   - Files to archive: `target/*.jar` or `target/*.war`

5. **Save and Build**
   - Click "Save"
   - Click "Build Now" from left sidebar

**Option B: Using Freestyle Project**

1. **Create New Job**
   - Go to Jenkins Dashboard
   - Click "New Item"
   - Enter job name: `MyMavenFreestyleBuild`
   - Select "Freestyle project"
   - Click "OK"

2. **Configure Source Code Management**
   - Select "Git" under Source Code Management
   - Enter Repository URL
   - Configure credentials and branch

3. **Add Build Step**
   - Scroll to "Build" section
   - Click "Add build step"
   - Select "Invoke top-level Maven targets"
   - Maven Version: Select "Maven-3.9" (configured earlier)
   - Goals: Enter `clean install`

4. **Save and Build**
   - Click "Save"
   - Click "Build Now"

#### 3. Monitor the Build

```bash
# In Jenkins UI:
# 1. Click on your job name
# 2. Click on the build number (e.g., "#1") under "Build History"
# 3. Click "Console Output"
```

#### 4. Verify Success

Look for this output in Console:
```
[INFO] Scanning for projects...
[INFO] 
[INFO] ------------------------------------------------------------------------
[INFO] Building your-project-name 1.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO] 
[INFO] --- maven-clean-plugin:3.1.0:clean (default-clean) @ your-project ---
[INFO] --- maven-compiler-plugin:3.8.1:compile (default-compile) @ your-project ---
[INFO] Compiling 5 source files to /path/to/target/classes
[INFO] 
[INFO] --- maven-surefire-plugin:2.22.2:test (default-test) @ your-project ---
[INFO] Tests run: 3, Failures: 0, Errors: 0, Skipped: 0
[INFO] 
[INFO] --- maven-jar-plugin:3.2.0:jar (default-jar) @ your-project ---
[INFO] Building jar: /path/to/target/your-project-1.0-SNAPSHOT.jar
[INFO] 
[INFO] --- maven-install-plugin:2.5.2:install (default-install) @ your-project ---
[INFO] Installing /path/to/target/your-project-1.0-SNAPSHOT.jar
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 8.234 s
[INFO] Finished at: 2025-10-10T10:30:00Z
[INFO] ------------------------------------------------------------------------
Finished: SUCCESS
```

#### 5. Troubleshooting

If build fails, check:
```bash
# Maven not found
# Solution: Configure Maven in Global Tool Configuration

# Git credentials error
# Solution: Add credentials in Jenkins (Credentials → Global → Add Credentials)

# pom.xml not found
# Solution: Verify repository structure, ensure pom.xml is in root

# Compilation errors
# Solution: Check Console Output for specific Java/dependency errors
```

---

# Set 5 Tasks

## Set 5 - Q1: Ansible Playbook to install and start Apache2

**Expected Output:** Apache2 installed, service running, and accessible via browser.

### Prerequisites Verified:
- ✅ Ansible installed
- ✅ SSH access configured
- ✅ Target server accessible

### Step-by-Step Instructions:

#### 1. Set Up Ansible Inventory
```bash
# Edit or create inventory file
sudo nano /etc/ansible/hosts

# Add your target server
[webservers]
192.168.1.101 ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/id_rsa
```

#### 2. Test Connectivity
```bash
# Test connection
ansible webservers -m ping

# Expected output:
# 192.168.1.101 | SUCCESS => {
#     "changed": false,
#     "ping": "pong"
# }
```

#### 3. Create the Playbook
```bash
# Navigate to playbooks directory
cd ~/ansible-playbooks

# Create the playbook file
nano apache-install.yml
```

#### 4. Write the Playbook Script
Copy this into `apache-install.yml`:

```yaml
---
- name: Install and start Apache2 web server
  hosts: webservers
  become: yes
  tasks:
    - name: Update apt repository
      apt:
        update_cache: yes
    
    - name: Install Apache2
      apt:
        name: apache2
        state: present
    
    - name: Start and enable Apache2
      service:
        name: apache2
        state: started
        enabled: yes
```

#### 5. Execute the Playbook
```bash
# Dry run (check mode) - see what would change
ansible-playbook apache-install.yml --check

# Execute the playbook
ansible-playbook apache-install.yml

# With verbose output
ansible-playbook apache-install.yml -vv
```

#### 6. Verify the Result

**Method 1: Using Ansible**
```bash
# Check Apache service status
ansible webservers -m shell -a "systemctl status apache2"

# Check if Apache is listening on port 80
ansible webservers -m shell -a "netstat -tuln | grep :80"
```

**Method 2: Using Browser**
```bash
# Open browser and navigate to:
http://192.168.1.101

# You should see "Apache2 Ubuntu Default Page"
```

**Method 3: Using curl**
```bash
# From control node
curl http://192.168.1.101

# Should return HTML with "Apache2 Ubuntu Default Page"
```

**Method 4: SSH to target server**
```bash
# SSH to target
ssh ubuntu@192.168.1.101

# Check status
sudo systemctl status apache2

# Should show:
# ● apache2.service - The Apache HTTP Server
#    Loaded: loaded
#    Active: active (running)
```

---

## Set 5 - Q2: Dockerfile, build the image, and push to Docker Hub from CLI

**Expected Output:** Image successfully pushed to Docker Hub.

### Prerequisites Verified:
- ✅ Docker installed
- ✅ Docker Hub account created
- ✅ Logged into Docker Hub

### Step-by-Step Instructions:

#### 1. Create Project Directory
```bash
# Create a directory for this project
mkdir ~/apache-docker
cd ~/apache-docker
```

#### 2. Create the Dockerfile
```bash
# Create Dockerfile
nano Dockerfile
```

Copy this into the Dockerfile:

```dockerfile
FROM ubuntu:latest

# Install Apache and clean cache
RUN apt-get update \
    && apt-get install -y apache2 \
    && rm -rf /var/lib/apt/lists/*

# Expose port 80
EXPOSE 80

# Run Apache in foreground
CMD ["apache2ctl", "-D", "FOREGROUND"]
```

Save and exit (Ctrl+X, Y, Enter)

#### 3. Build the Docker Image
```bash
# Build image (replace 'yourusername' with your Docker Hub username)
docker build -t yourusername/apache-demo:latest .

# Example: docker build -t johnsmith/apache-demo:latest .

# You'll see output like:
# Step 1/4 : FROM ubuntu:latest
# Step 2/4 : RUN apt-get update...
# Successfully built abc123def456
# Successfully tagged yourusername/apache-demo:latest
```

#### 4. Test the Image Locally (Optional but Recommended)
```bash
# Run the container locally to test
docker run -d -p 8080:80 --name test-apache yourusername/apache-demo:latest

# Test with curl
curl http://localhost:8080

# Should return Apache default page HTML

# Stop and remove test container
docker stop test-apache
docker rm test-apache
```

#### 5. Login to Docker Hub
```bash
# Login to Docker Hub
docker login

# Enter your Docker Hub username
# Enter your Docker Hub password (or access token)

# Successful login shows:
# Login Succeeded
```

#### 6. Push Image to Docker Hub
```bash
# Push the image
docker push yourusername/apache-demo:latest

# You'll see:
# The push refers to repository [docker.io/yourusername/apache-demo]
# abc123: Pushed
# def456: Pushed
# latest: digest: sha256:xxxxx size: 1234
```

#### 7. Verify on Docker Hub

**Method 1: Web Interface**
```bash
# Visit: https://hub.docker.com
# Login to your account
# Go to "Repositories"
# You should see "apache-demo" listed
# Click on it to see tags and details
```

**Method 2: Pull from another machine**
```bash
# On a different machine (or after deleting local image)
docker pull yourusername/apache-demo:latest

# Run it
docker run -d -p 80:80 yourusername/apache-demo:latest
```

#### 8. Cleanup (Optional)
```bash
# Remove local image
docker rmi yourusername/apache-demo:latest

# Logout from Docker Hub
docker logout
```

---

## Set 5 - Q3: Create Git repository with multiple versions of same file

**Expected Output:** Multiple commits visible in GitHub history.

### Prerequisites Verified:
- ✅ Git installed and configured
- ✅ GitHub account created
- ✅ SSH key or Personal Access Token configured

### Step-by-Step Instructions:

#### 1. Create Repository on GitHub

**Via Web Interface:**
```bash
# 1. Go to https://github.com
# 2. Click the "+" icon (top right)
# 3. Select "New repository"
# 4. Repository name: myrepo
# 5. Description: (optional)
# 6. Choose: Public or Private
# 7. DO NOT initialize with README
# 8. Click "Create repository"
# 9. Copy the repository URL: https://github.com/your-username/myrepo.git
```

#### 2. Initialize Local Repository
```bash
# Create and navigate to directory
mkdir myrepo
cd myrepo

# Initialize Git repository
git init

# Output: Initialized empty Git repository in /path/to/myrepo/.git/
```

#### 3. Create First Version
```bash
# Create file with version 1
echo "Version 1" > file.txt

# Check status
git status

# Output shows: Untracked files: file.txt

# Stage the file
git add file.txt

# Commit with message
git commit -m "First version"

# Output:
# [main (root-commit) abc1234] First version
# 1 file changed, 1 insertion(+)
# create mode 100644 file.txt
```

#### 4. Create Second Version
```bash
# Modify the file
echo "Version 2" > file.txt

# View the change
cat file.txt
# Output: Version 2

# Check what changed
git diff file.txt

# Stage and commit
git add file.txt
git commit -m "Second version"

# Output:
# [main def5678] Second version
# 1 file changed, 1 insertion(+), 1 deletion(-)
```

#### 5. Create Third Version (Optional - for more commits)
```bash
# Add more content
echo "Version 3 - Added new features" > file.txt

git add file.txt
git commit -m "Third version"
```

#### 6. View Local Commit History
```bash
# View commit history
git log

# Output shows:
# commit def5678... (HEAD -> main)
# Author: Your Name <your.email@example.com>
# Date: ...
#     Second version
#
# commit abc1234...
# Author: Your Name <your.email@example.com>
# Date: ...
#     First version

# Compact view
git log --oneline

# Output:
# def5678 Second version
# abc1234 First version
```

#### 7. Connect to GitHub Remote
```bash
# Add remote repository (replace with your actual URL)
git remote add origin https://github.com/your-username/myrepo.git

# Verify remote was added
git remote -v

# Output:
# origin  https://github.com/your-username/myrepo.git (fetch)
# origin  https://github.com/your-username/myrepo.git (push)
```

#### 8. Rename Branch to 'main' (if needed)
```bash
# Check current branch
git branch

# If it shows 'master', rename to 'main'
git branch -M main
```

#### 9. Push to GitHub
```bash
# Push to GitHub
git push -u origin main

# You'll be prompted for GitHub credentials:
# Username: your-username
# Password: (use Personal Access Token, not password)

# Output:
# Enumerating objects: 6, done.
# Counting objects: 100% (6/6), done.
# Writing objects: 100% (6/6), 520 bytes | 520.00 KiB/s, done.
# Total 6 (delta 0), reused 0 (delta 0)
# To https://github.com/your-username/myrepo.git
#  * [new branch]      main -> main
# Branch 'main' set up to track remote branch 'main' from 'origin'.
```

#### 10. Verify on GitHub

**Method 1: Via Web Interface**
```bash
# 1. Go to https://github.com/your-username/myrepo
# 2. You should see:
#    - file.txt in the file list
#    - "Second version" as the latest commit message
# 3. Click on "2 commits" (or number of commits you made)
# 4. You'll see the full commit history with all versions
# 5. Click on "file.txt" to view current content
# 6. Click "History" button to see all changes to this file
```

**Method 2: Clone and Verify**
```bash
# On a different location or machine
cd ~
git clone https://github.com/your-username/myrepo.git
cd myrepo

# View commit history
git log

# View file content
cat file.txt
```

---

# Set 6 Tasks

## Set 6 - Q1: Ansible Playbook to install and start HAProxy

**Expected Output:** HAProxy installed and running.

### Prerequisites Verified:
- ✅ Ansible installed
- ✅ SSH access configured
- ✅ Target server accessible

### Step-by-Step Instructions:

#### 1. Set Up Ansible Inventory
```bash
# Edit inventory file
sudo nano /etc/ansible/hosts

# Add your target server
[loadbalancers]
192.168.1.102 ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/id_rsa
```

#### 2. Test Connectivity
```bash
# Test connection
ansible loadbalancers -m ping

# Expected: SUCCESS with "ping": "pong"
```

#### 3. Create the Playbook
```bash
# Navigate to playbooks directory
cd ~/ansible-playbooks

# Create the playbook file
nano haproxy-install.yml
```

#### 4. Write the Playbook Script
Copy this into `haproxy-install.yml`:

```yaml
---
- name: Install and start HAProxy
  hosts: loadbalancers
  become: yes
  tasks:
    - name: Update apt repository
      apt:
        update_cache: yes
    
    - name: Install haproxy
      apt:
        name: haproxy
        state: present
    
    - name: Enable and start haproxy service
      service:
        name: haproxy
        state: started
        enabled: yes
```

#### 5. Execute the Playbook
```bash
# Check syntax
ansible-playbook haproxy-install.yml --syntax-check

# Dry run
ansible-playbook haproxy-install.yml --check

# Execute
ansible-playbook haproxy-install.yml

# Expected output:
# PLAY [Install and start HAProxy]
# TASK [Gathering Facts]
# TASK [Update apt repository]
# TASK [Install haproxy]
# TASK [Enable and start haproxy service]
# PLAY RECAP
# 192.168.1.102 : ok=4 changed=3 ...
```

#### 6. Verify HAProxy Installation

**Method 1: Using Ansible**
```bash
# Check HAProxy status
ansible loadbalancers -m shell -a "systemctl status haproxy"

# Check HAProxy version
ansible loadbalancers -m shell -a "haproxy -v"

# Check if HAProxy is listening
ansible loadbalancers -m shell -a "netstat -tuln | grep haproxy"
```

**Method 2: SSH to Server**
```bash
# SSH to target server
ssh ubuntu@192.168.1.102

# Check service status
sudo systemctl status haproxy

# Output should show:
# ● haproxy.service - HAProxy Load Balancer
#    Loaded: loaded
#    Active: active (running)

# Check HAProxy version
haproxy -v

# View configuration
cat /etc/haproxy/haproxy.cfg

# Check logs
sudo tail -f /var/log/haproxy.log
```

**Method 3: Test HAProxy Stats Page (if enabled)**
```bash
# HAProxy stats page is usually at:
# http://192.168.1.102/haproxy?stats

# Or check what ports HAProxy is listening on:
sudo netstat -tuln | grep haproxy
```

#### 7. Optional: Configure HAProxy (Basic Setup)

```bash
# SSH to the server
ssh ubuntu@192.168.1.102

# Backup original config
sudo cp /etc/haproxy/haproxy.cfg /etc/haproxy/haproxy.cfg.backup

# Edit configuration
sudo nano /etc/haproxy/haproxy.cfg

# Add basic frontend/backend configuration at the end:
# frontend http_front
#    bind *:80
#    default_backend http_back
#
# backend http_back
#    balance roundrobin
#    server web1 192.168.1.10:80 check
#    server web2 192.168.1.11:80 check

# Test configuration
sudo haproxy -c -f /etc/haproxy/haproxy.cfg

# Restart HAProxy
sudo systemctl restart haproxy

# Verify it's running
sudo systemctl status haproxy
```

---

## Set 6 - Q2: Dockerfile to containerize simple Python app that prints a message

**Expected Output:** When the container runs, it prints: `Hello from Docker and Python!`

### Prerequisites Verified:
- ✅ Docker installed
- ✅ User in docker group
- ✅ Python available in Docker images

### Step-by-Step Instructions:

#### 1. Create Project Directory
```bash
# Create and navigate to project directory
mkdir ~/python-docker-app
cd ~/python-docker-app
```

#### 2. Create Python Application File
```bash
# Create the Python script
nano app.py
```

Copy this into `app.py`:

```python
print("Hello from Docker and Python!")
```

Save and exit (Ctrl+X, Y, Enter)

#### 3. Test Python Script Locally (Optional)
```bash
# Test if Python is installed
python3 --version

# Run the script locally
python3 app.py

# Output: Hello from Docker and Python!
```

#### 4. Create Dockerfile
```bash
# Create Dockerfile in the same directory
nano Dockerfile
```

Copy this into `Dockerfile`:

```dockerfile
FROM python:3.9-slim

COPY app.py /app.py

CMD ["python", "/app.py"]
```

Save and exit (Ctrl+X, Y, Enter)

#### 5. Verify Your Files
```bash
# List files in current directory
ls -la

# Should show:
# app.py
# Dockerfile

# View file contents
cat app.py
cat Dockerfile
```

#### 6. Build the Docker Image
```bash
# Build the image
docker build -t python-hello .

# Breakdown:
# -t python-hello  : Tag/name the image as "python-hello"
# .                : Build context (current directory)

# Expected output:
# Step 1/3 : FROM python:3.9-slim
# Step 2/3 : COPY app.py /app.py
# Step 3/3 : CMD ["python", "/app.py"]
# Successfully built abc123def456
# Successfully tagged python-hello:latest
```

#### 7. Verify Image was Created
```bash
# List Docker images
docker images

# Should show:
# REPOSITORY      TAG       IMAGE ID       CREATED          SIZE
# python-hello    latest    abc123def456   10 seconds ago   125MB
# python          3.9-slim  xyz789abc123   2 weeks ago      125MB
```

#### 8. Run the Container
```bash
# Run the container
docker run --rm python-hello

# Output: Hello from Docker and Python!

# Breakdown:
# --rm           : Automatically remove container after it exits
# python-hello   : Name of the image to run
```

#### 9. Run Container with Different Options

**Keep container running:**
```bash
# Run without --rm to keep container
docker run --name my-python-app python-hello

# List all containers (including stopped)
docker ps -a

# Should show:
# CONTAINER ID   IMAGE          STATUS                     NAMES
# abc123def456   python-hello   Exited (0) 5 seconds ago   my-python-app
```

**View container logs:**
```bash
# View logs of stopped container
docker logs my-python-app

# Output: Hello from Docker and Python!
```

**Run interactively to debug:**
```bash
# Override CMD and run bash instead
docker run -it --rm python-hello /bin/bash

# Now you're inside the container
# You can manually run:
python /app.py

# Exit with:
exit
```

#### 10. Modify and Rebuild (Practice)

```bash
# Edit the Python file
nano app.py

# Change to:
print("Hello from Docker and Python!")
print("This is version 2!")

# Rebuild image
docker build -t python-hello:v2 .

# Run new version
docker run --rm python-hello:v2

# Output:
# Hello from Docker and Python!
# This is version 2!
```

#### 11. Cleanup
```bash
# Remove stopped containers
docker rm my-python-app

# Remove images
docker rmi python-hello:latest
docker rmi python-hello:v2

# Or remove all unused images
docker image prune -a
```

#### 12. Troubleshooting

**Permission denied error:**
```bash
# If you get permission denied, add user to docker group:
sudo usermod -aG docker $USER

# Logout and login again, or run:
newgrp docker
```

**Image not found:**
```bash
# Ensure you're in the directory with Dockerfile
pwd
ls -la

# Make sure Dockerfile exists
```

**Python script not executing:**
```bash
# Check if app.py exists in image:
docker run --rm python-hello ls -la /

# Should show app.py in root directory
```

---

## Set 6 - Q3: Create repo, add index.html, and push to GitHub

**Expected Output:** Repository appears on GitHub with committed index.html and commit message 'Initial commit'.

### Prerequisites Verified:
- ✅ Git installed and configured
- ✅ GitHub account created
- ✅ Personal Access Token or SSH key configured

### Step-by-Step Instructions:

#### 1. Create GitHub Repository

**Via GitHub Web Interface:**
```bash
# 1. Go to https://github.com
# 2. Login to your account
# 3. Click the "+" icon in top-right corner
# 4. Select "New repository"
# 5. Repository name: mywebsite
# 6. Description: My first website (optional)
# 7. Choose: Public or Private
# 8. DO NOT check "Initialize this repository with a README"
# 9. DO NOT add .gitignore or license (we'll do it manually)
# 10. Click "Create repository"
# 11. Copy the repository URL shown: https://github.com/your-username/mywebsite.git
```

#### 2. Create Local Project Directory
```bash
# Create directory for your website
mkdir ~/mywebsite
cd ~/mywebsite

# Verify you're in the right place
pwd
# Output: /home/username/mywebsite
```

#### 3. Initialize Git Repository
```bash
# Initialize Git repo
git init

# Output: Initialized empty Git repository in /home/username/mywebsite/.git/

# Verify .git folder was created
ls -la

# Should show: .git/
```

#### 4. Create index.html File
```bash
# Create index.html with content
echo "<h1>Hello GitHub</h1>" > index.html

# Verify the file was created
ls -la

# Should show: index.html

# View the content
cat index.html

# Output: <h1>Hello GitHub</h1>
```

**Alternative: Create with more content**
```bash
# Create a more complete HTML file
nano index.html

# Paste this content:
<!DOCTYPE html>
<html>
<head>
    <title>My Website</title>
</head>
<body>
    <h1>Hello GitHub</h1>
    <p>This is my first GitHub pages site!</p>
</body>
</html>

# Save and exit (Ctrl+X, Y, Enter)
```

#### 5. Check Git Status
```bash
# Check current status
git status

# Output:
# On branch master
#
# No commits yet
#
# Untracked files:
#   (use "git add <file>..." to include in what will be committed)
#         index.html
#
# nothing added to commit but untracked files present
```

#### 6. Stage the File
```bash
# Add index.html to staging area
git add index.html

# Check status again
git status

# Output:
# On branch master
#
# No commits yet
#
# Changes to be committed:
#   (use "git rm --cached <file>..." to unstage)
#         new file:   index.html
```

#### 7. Commit the Changes
```bash
# Commit with message "Initial commit"
git commit -m "Initial commit"

# Output:
# [master (root-commit) abc1234] Initial commit
#  1 file changed, 1 insertion(+)
#  create mode 100644 index.html

# Verify commit was created
git log

# Output shows:
# commit abc1234567890...
# Author: Your Name <your.email@example.com>
# Date: Fri Oct 10 12:00:00 2025
#
#     Initial commit
```

#### 8. Rename Branch to 'main'
```bash
# GitHub uses 'main' as default branch now
git branch -M main

# Verify branch name
git branch

# Output: * main
```

#### 9. Connect to GitHub Remote Repository
```bash
# Add remote (replace with YOUR actual repository URL)
git remote add origin https://github.com/your-username/mywebsite.git

# Example:
# git remote add origin https://github.com/johnsmith/mywebsite.git

# Verify remote was added
git remote -v

# Output:
# origin  https://github.com/your-username/mywebsite.git (fetch)
# origin  https://github.com/your-username/mywebsite.git (push)
```

#### 10. Push to GitHub
```bash
# Push to GitHub
git push -u origin main

# You'll be prompted:
# Username for 'https://github.com': your-username
# Password for 'https://your-username@github.com': [paste your token here]

# Output:
# Enumerating objects: 3, done.
# Counting objects: 100% (3/3), done.
# Writing objects: 100% (3/3), 245 bytes | 245.00 KiB/s, done.
# Total 3 (delta 0), reused 0 (delta 0)
# To https://github.com/your-username/mywebsite.git
#  * [new branch]      main -> main
# Branch 'main' set up to track remote branch 'main' from 'origin'.
```

#### 11. Verify on GitHub

**Method 1: Web Interface**
```bash
# 1. Go to: https://github.com/your-username/mywebsite
# 2. You should see:
#    - index.html file listed
#    - "Initial commit" as the commit message
#    - "1 commit" link at the top
# 3. Click on "index.html" to view the file content
# 4. Should display: <h1>Hello GitHub</h1>
# 5. Click on "1 commit" to see commit history
# 6. Should show your commit with message "Initial commit"
```

**Method 2: Clone and Verify**
```bash
# From a different directory
cd ~
git clone https://github.com/your-username/mywebsite.git test-clone
cd test-clone

# List files
ls -la
# Should show: index.html

# View content
cat index.html
# Output: <h1>Hello GitHub</h1>

# View commit history
git log --oneline
# Output: abc1234 Initial commit

# Cleanup
cd ~
rm -rf test-clone
```

#### 12. Optional: Enable GitHub Pages

Make your HTML file accessible as a website:
```bash
# Via GitHub Web Interface:
# 1. Go to your repository: https://github.com/your-username/mywebsite
# 2. Click "Settings" tab
# 3. Scroll down to "Pages" in left sidebar
# 4. Under "Source", select "main" branch
# 5. Click "Save"
# 6. Wait 1-2 minutes
# 7. Your site will be live at: https://your-username.github.io/mywebsite
# 8. Open that URL in browser to see your page
```

#### 13. Make Additional Changes (Practice)

```bash
# Go back to your project
cd ~/mywebsite

# Modify index.html
echo "<h1>Hello GitHub</h1><p>Updated content!</p>" > index.html

# Check what changed
git diff

# Stage, commit, and push
git add index.html
git commit -m "Updated content"
git push origin main

# Verify on GitHub - you should see 2 commits now
```

#### 14. Troubleshooting

**Authentication Failed:**
```bash
# Problem: Wrong username or token
# Solution: Use Personal Access Token as password, not account password
# Make sure token has 'repo' scope enabled
```

**Remote already exists:**
```bash
# If you get "remote origin already exists"
git remote remove origin
git remote add origin https://github.com/your-username/mywebsite.git
```

**Push rejected:**
```bash
# If remote has changes you don't have locally
git pull origin main --rebase
git push origin main
```

**Wrong branch name:**
```bash
# If you accidentally used 'master' instead of 'main'
git branch -M main
git push -u origin main
```

---

## Summary of All Tasks

### Set 4:
1. ✅ Ansible playbook to install Nginx with full setup and verification
2. ✅ Docker container run with logs and detailed monitoring steps
3. ✅ Maven build in Jenkins with complete job configuration

### Set 5:
1. ✅ Ansible playbook to install Apache2 with multiple verification methods
2. ✅ Dockerfile for Apache with build, test, and Docker Hub push
3. ✅ Git repository with multiple versions and complete GitHub workflow

### Set 6:
1. ✅ Ansible playbook to install HAProxy with configuration guidance
2. ✅ Python app Dockerization with troubleshooting tips
3. ✅ Website repository creation with GitHub Pages setup

---

## Quick Reference Commands

### Ansible:
```bash
ansible all -m ping                          # Test connectivity
ansible-playbook playbook.yml --check        # Dry run
ansible-playbook playbook.yml -v             # Verbose execution
```

### Docker:
```bash
docker images                                # List images
docker ps                                    # Running containers
docker ps -a                                 # All containers
docker logs <container>                      # View logs
docker build -t name:tag .                   # Build image
docker run -d --name name image              # Run detached
```

### Git:
```bash
git status                                   # Check status
git add <file>                               # Stage file
git commit -m "message"                      # Commit changes
git push origin main                         # Push to remote
git log --oneline                            # View history
```

### Jenkins:
- Dashboard → New Item → Choose project type
- Configure SCM (Git repository URL)
- Add build steps (Maven goals)
- Save and click "Build Now"
- Check Console Output for results

---

## Additional Resources

- **Ansible Documentation**: https://docs.ansible.com
- **Docker Documentation**: https://docs.docker.com
- **Git Documentation**: https://git-scm.com/doc
- **GitHub Guides**: https://guides.github.com
- **Jenkins Documentation**: https://www.jenkins.io/doc
- **Maven Documentation**: https://maven.apache.org/guides

---

## End of Document