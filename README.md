# CI/CD with Jenkins - Explanation and Examples

## What is CI/CD?

- **CI (Continuous Integration)** is the practice of frequently integrating code into a shared repository. Every change is automatically tested, ensuring that new code does not break the existing system.
- **CD (Continuous Delivery/Deployment)** refers to automatically delivering software to production or staging environments. Continuous Delivery ensures that code is always in a deployable state, while Continuous Deployment extends this by automatically deploying every change that passes the tests to production.

Jenkins is one of the most popular tools for implementing CI/CD. It automates the process of building, testing, and deploying software, which improves the efficiency and reliability of your development pipeline.

### How Jenkins Helps in CI/CD?

Jenkins enables CI/CD by automating various tasks involved in the software development lifecycle. It can:
1. Automatically pull code from a version control system (like Git).
2. Build and compile code.
3. Run tests (unit tests, integration tests).
4. Deploy the application to various environments (development, staging, production).

### Jenkins Pipeline Overview

Jenkins uses **Pipelines** to define the series of steps that need to be executed in the CI/CD process. Pipelines can be defined in:
- **Declarative Pipeline**: A simplified syntax for defining a pipeline.
- **Scripted Pipeline**: A more flexible pipeline using Groovy scripting.

### Setting Up Jenkins for CI/CD

### **Installation of Jenkins**
---

1. **Install Jenkins**:
   - You can download and install Jenkins from the [official website](https://www.jenkins.io/download/).
   - On Linux, you can use `apt-get` (Ubuntu/Debian) or `yum` (CentOS/RHEL).
   - On macOS, you can use Homebrew: `brew install jenkins`.

2. **Start Jenkins**:
   Once installed, start Jenkins by running:

   ```bash
   java -jar jenkins.war
   
By default, Jenkins runs on http://localhost:8080.

3. **Unlock Jenkins**:
   After installation, you'll be prompted to enter an "unlock" key, which can be found in the log file.

5. **Install Suggested Plugins**: During the setup process, Jenkins will ask you to install plugins. Install the recommended plugins to get started with CI/CD.

6. **Create an Admin User**: Once the setup is complete, create an admin user to manage Jenkins.

### **Setting Up Version Control (Git)**
---

In Jenkins, we typically use Git as a version control system. Here’s how you can integrate it:

1. **Install Git Plugin**:
Go to Manage Jenkins → Manage Plugins → Available tab → search for Git Plugin and install it.
2. **Create a New Job in Jenkins**:
Go to Jenkins Dashboard → New Item → Select Freestyle Project.
Under the Source Code Management section, select Git and provide the Git repository URL and credentials.
## 3. Creating a Simple Jenkins Pipeline

You can define your CI/CD pipeline in Jenkins using a **Jenkinsfile**. 

A Jenkinsfile is a text file that contains the pipeline as code, which defines the stages of the pipeline.

### Example: Basic Declarative Jenkinsfile

```groovy
pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                // Checkout code from Git
                git 'https://github.com/your-repo/your-app.git'
            }
        }

        stage('Build') {
            steps {
                // Build application (Example for Java)
                sh './mvnw clean install'
            }
        }

        stage('Test') {
            steps {
                // Run unit tests
                sh './mvnw test'
            }
        }

        stage('Deploy') {
            steps {
                // Deploy to staging (Example for a web app)
                sh 'scp target/your-app.war user@staging-server:/path/to/deploy'
            }
        }
    }

    post {
        success {
            echo 'Pipeline executed successfully'
        }
        failure {
            echo 'Pipeline failed'
        }
    }
}
```


### Jenkins Freestyle Job Example

For simpler jobs, you can use **Freestyle Projects** in Jenkins, which don't require a `Jenkinsfile`. Here's how to set up a basic Freestyle job:

### Step 1: Create a New Job

1. Go to **New Item** in Jenkins.
2. Select **Freestyle Project**.
3. Enter a name for your project and click **OK**.

### Step 2: Configure Source Code Management

1. In the job configuration, scroll down to the **Source Code Management** section.
2. Select **Git**.
3. Enter the repository URL for your project.

### Step 3: Add Build Steps

1. Scroll to the **Build** section and click on **Add build step**.
2. Choose an appropriate build step:
   - For example, to build a Java project, select **Execute Shell** and enter the following command:
   
   ```bash
   ./mvnw clean install

This command will run Maven to clean the project and build the application.

### Step 4: Add Post-build Actions
Scroll to the Post-build Actions section.

Here, you can define actions such as:

- Email Notifications: Send notifications to specified recipients after the build.
- Archive Artifacts: Save build artifacts such as JAR or WAR files.
  Example: To archive the build artifacts, you can configure it like this:

Files to archive: target/*.jar or target/*.war
This will save all .jar or .war files produced in the target directory.

### Step 5: Run the Job
You can now trigger the job manually by clicking Build Now.

Alternatively, you can set it to run automatically using triggers such as a Git webhook on each push.

## Integrating Testing into the Pipeline
You can integrate automated tests, such as unit tests or integration tests, directly into your Jenkins pipeline. Here's an example of how to configure unit tests using Maven:

### Example with Maven:

```groovy
stage('Test') {
    steps {
        // Run tests using Maven
        sh './mvnw test'
    }
    post {
        always {
            junit '**/target/test-*.xml'  // Publish test results
        }
    }
}
```

## Deploying the Application
---
Deployment can be done in various environments, such as staging, production, etc. Jenkins provides multiple ways to configure deployment using different tools and methods, including:

- **SSH Deployment**: Using `scp` or `rsync` to deploy artifacts.
- **Docker**: You can use Docker commands in the pipeline to build Docker images and push them to a registry.
- **Kubernetes**: Jenkins can deploy applications to Kubernetes clusters using `kubectl`.

### Example: Deploying with Docker

Here’s an example of deploying your application using Docker in a Jenkins pipeline:

```groovy
stage('Deploy') {
    steps {
        script {
            // Build Docker image
            sh 'docker build -t my-app:latest .'
            
            // Deploy Docker container
            sh 'docker run -d -p 8080:8080 my-app:latest'
        }
    }
}
```

### Triggering Builds Automatically

You can trigger Jenkins builds automatically using:

- **Webhook**: For GitHub or GitLab integration, Jenkins can trigger a build when there’s a new commit or pull request.
- **Scheduled Builds**: You can schedule Jenkins jobs using cron syntax (e.g., every night at midnight).
- **Pipeline Triggers**: Trigger one pipeline from another pipeline using build steps.

  **Example**: Trigger a Build on Git Push
  In the GitHub repository, go to Settings → Webhooks and add the Jenkins webhook URL (http://<jenkins-server>/github-webhook/),
  which will trigger the Jenkins job whenever there’s a push.

## Continuous Deployment (CD) Example
To enable Continuous Deployment, you can extend the pipeline to deploy your application automatically after successful builds and tests:

```groovy
stage('Deploy to Production') {
    steps {
        script {
            // SSH to production server and deploy app
            sh 'scp target/app.war user@production-server:/opt/tomcat/webapps'
        }
    }
}
```

**Conclusion**
Jenkins helps automate the CI/CD pipeline, from code integration to deployment. With Jenkins, you can easily set up a full automation pipeline that:

- Builds and tests code.
- Deploys it to staging or production.
- Notifies teams on success or failure.

By using Jenkins, you can streamline development processes, ensure consistent quality, and deliver features and bug fixes faster with more reliability.
