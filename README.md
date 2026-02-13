# Java Application Deployment Lab on DigitalOcean

## Overview

This lab demonstrates how to securely deploy and operate a Java Spring Boot application on a cloud server using **DigitalOcean**, following real-world **Application Manager / DevOps** practices.

The focus is not just running an application, but:
- Hardening the server
- Enforcing secure access
- Building and packaging a Java application
- Deploying and validating the application in production-like conditions

---

## Architecture Summary

- **Client:** Developer workstation (Windows + Git Bash + IntelliJ IDEA)
- **Cloud Provider:** DigitalOcean
- **Compute:** Ubuntu 22.04 Droplet
- **Runtime:** OpenJDK 17
- **Build Tool:** Gradle
- **Application:** Spring Boot (embedded Tomcat, port 8080)
- **Access:** SSH key-based authentication
- **Security:** UFW firewall, non-root user, SSH hardening

---

## Project Structure

java-app/
├── src/main/java/com/example/
│   ├── App.java
│   └── HelloController.java
├── build/libs/
│   ├── java-app-1.0-SNAPSHOT.jar
│   └── java-app-1.0-SNAPSHOT-plain.jar
├── build.gradle
└── gradlew

---

## Phase 1 — Droplet Creation
- Created Ubuntu 22.04 Droplet on DigitalOcean
- Selected region and size suitable for Java workloads
- Added SSH key during creation

![Droplet Creation](screenshots/01-droplet-created.png)

## Phase 2 — Initial Root SSH Login
- Logged in using SSH as root for initial configuration

  - Root Login (Initial Access)
  - ssh root@209.38.238.74

![Secure SSH Access](screenshots/02-ssh-root-login.png)

## Phase 3 — Create Non-Root User
- Created a dedicated application user
- Added user to sudoers group

  - adduser ali
  - usermod -aG sudo ali

![Created Non-Root User](screenshots/03-create-user-ali.png)

## Phase 4 — Copy SSH Keys to Non-Root User
- Copied SSH keys from root to non-root user
  
  - rsync --archive --chown=ali:ali ~/.ssh /home/ali

📸 screenshots/04-copy-ssh-keys.png
![Copy SSH Keys to Non-Root User](screenshots/04-copy-ssh-keys.png)

## Phase 5 — SSH Hardening
- Edited SSH configuration to improve security
- Disabled root login
- Enforced key-based authentication

  - sudo nano /etc/ssh/sshd_config
  - sudo systemctl restart ssh

![SSH Hardening](screenshots/05-ssh-hardened.png)

## Phase 6 — Firewall Configuration (UFW)
- Allowed SSH traffic
- Enabled firewall protection

  - sudo ufw allow OpenSSH
  - sudo ufw enable
  - sudo ufw status  

![Firewall Configuration (UFW)](screenshots/06-ufw-enabled.png)

## Phase 7 — Reboot and Access Verification
- Rebooted server (droplet)
- Verified SSH login using non-root user

  - sudo reboot
  - ssh ali@209.38.238.74

![Reboot and Access Verification](screenshots/07-post-reboot-login.png)

---

## Phase 8 — Grant Sudo Access Validation
- Verified sudo access for non-root user

  - sudo whoami

![Reboot and Access Verification 1](screenshots/08-ali-added-to-sudo.png)
![Reboot and Access Verification 2](screenshots/09-sudo-working.png)

## Phase 9 — Build Java Application Locally
- Built Spring Boot application using Gradle Wrapper

  - ./gradlew clean build

![Build Java Application Locally](screenshots/10-gradle-build-success.png)

## Phase 10 — Install Java on Droplet
- Installed OpenJDK 17
- Verified Java runtime

  - sudo apt update
  - sudo apt install -y openjdk-17-jdk
  - java -version 

![Install Java on Droplet](screenshots/11-java-installed-on-droplet.png)

## Phase 11 — Create Application Directory
- Created a dedicated directory for application deployment

  - mkdir -p ~/apps/java-app

![Create Application Directory](screenshots/12-fix-wrong-directory.png)

## Phase 12 — Transfer Application Artifact
- Copied built JAR file to the Droplet

  - scp build/libs/java-app-1.0-SNAPSHOT.jar ali@<droplet-ip>:~/apps/java-app/

![Transfer Application Artifact](screenshots/13-jar-copied-to-droplet.png)

## Phase 13 — Verify Artifact on Server (Droplet)
- Confirmed JAR file presence and permissions

  - ls -la ~/apps/java-app

![Verify Artifact on Server](screenshots/14-jar-visible-on-server(droplet).png)

## Phase 14 — Start Application
- Started Spring Boot application using nohup
- Redirected logs to file

  - nohup java -jar java-app-1.0-SNAPSHOT.jar > app.log 2>&1 &

![Start Applicationy](screenshots/15-app-started.png)

## Phase 15 — Port and Process Validation
- Copied built JAR file to the Droplet

  - ss -lntp | grep 8080

![Port and Process Validation 1](screenshots/16-port-check.png)
![Port and Process Validation 2](screenshots/17-ufw-allow-8080.png)
![Port and Process Validation 3](screenshots/18-app-running-nohup.png)

## Phase 16 — Application Access & Validation
- Verified application endpoints via browser
  - /
  - /health
    
- Validated via browser:
  - http://<droplet-ip>:8080/
  - http://<droplet-ip>:8080/health

![[Application Access & Validation 1](screenshots/23-browser-home.png)
![[Application Access & Validation 2](screenshots/24-browser-health.png)

---

## Phase 17 — Application Shutdown
- Stopped the application

  - ps -ef | grep java
  - kill <pid>

![Application Shutdown](screenshots/25-app-stopped.png)

## Phase 18 — Droplet Cleanup
- Verified application endpoints via browser

  - /
  - /health

![Phase 16 — Droplet Cleanup](screenshots/26-droplet-powered-off.png)

---




