# Jenkins Setup

This guide explains how to install Jenkins and configure it with the required plugins for our pipelines.

---

## 1. Install Jenkins

### On Ubuntu/Debian

```bash
# Update system
sudo apt update && sudo apt upgrade -y

# Install Java (required by Jenkins)
sudo apt install -y fontconfig openjdk-17-jre

# Add Jenkins repo and key
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee   /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc]   https://pkg.jenkins.io/debian-stable binary/ | sudo tee   /etc/apt/sources.list.d/jenkins.list > /dev/null

# Install Jenkins
sudo apt update
sudo apt install -y jenkins

# Enable and start service
sudo systemctl enable jenkins
sudo systemctl start jenkins
```

### Verify Installation

Open Jenkins in your browser:  
👉 `http://<your-server-ip>:8080`

Unlock Jenkins using the initial admin password:

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

---

## 2. Required Plugins

Our pipelines depend on the following plugins:

| Plugin Name                  | Purpose                                                               |
| ---------------------------- | --------------------------------------------------------------------- |
| **AnsiColor**                | Enables colored console output for better readability.                |
| **Pipeline Utility Steps**   | Provides utility steps (JSON, CSV, file, ZIP handling) for pipelines. |
| **Pipeline Stage View**      | Adds visualization of stages and branches in the pipeline UI.         |
| **Nexus Artifacts Uploader** | To Upload Artifacts on to nexus.                                      |
| **Docker Pipeline**          | To Run agent as Docker.                                               |

---

## 3. Installing Plugins

### Option A: Jenkins UI

1. Go to **Manage Jenkins** → **Plugins** → **Available Plugins**.
2. Search and install:
   - `AnsiColor`
   - `Pipeline Utility Steps`
   - `Pipeline Stage View`
   - `Nexus Artifacts Uploader`
   - `Docker Pipeline`
3. Restart Jenkins if prompted.

### Option B: Jenkins CLI

```bash
# Download Jenkins CLI
wget http://<jenkins-server>:8080/jnlpJars/jenkins-cli.jar

# Install plugins
java -jar jenkins-cli.jar -s http://<jenkins-server>:8080 install-plugin ansicolor
java -jar jenkins-cli.jar -s http://<jenkins-server>:8080 install-plugin pipeline-utility-steps
java -jar jenkins-cli.jar -s http://<jenkins-server>:8080 install-plugin pipeline-stage-view
java -jar jenkins-cli.jar -s http://<jenkins-server>:8080 install-plugin nexus-artifacts-uploader

# Restart Jenkins
java -jar jenkins-cli.jar -s http://<jenkins-server>:8080 safe-restart
```

---

## 4. Verify Plugins

After Jenkins restarts:

- Go to **Manage Jenkins** → **Plugins** → **Installed Plugins**.
- Confirm the required plugins are listed and enabled.

---

## 5. Example Jenkinsfile

Example pipeline using the installed plugins:

```groovy
pipeline {
    agent any
    options {
        ansiColor('xterm')
    }
    stages {
        stage('Build') {
            steps {
                echo "Building project..."
            }
        }
        stage('Parse JSON') {
            steps {
                script {
                    def data = readJSON text: '{"version":"1.0.0"}'
                    echo "Version: ${data.version}"
                }
            }
        }
        stage('Deploy') {
            steps {
                echo "Deploying..."
            }
        }
    }
}
```

---

✅ With Jenkins installed and plugins configured, you can now build and visualize pipelines effectively.
