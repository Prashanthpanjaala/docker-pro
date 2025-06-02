In this project, we leverage Jenkins to create a robust CI/CD pipeline that integrates tools like Docker, Trivy, SonarQube, and OWASP Dependency Check to deliver secure and high-quality software.

Tools used:

1.GitHub

2.Jenkins

3.Docker

4.OWASP

5.Trivy

6.Sonarqube

7.NodeJS


GitHub

GitHub is a platform for hosting and sharing code using Git. It helps developers collaborate by tracking changes, managing versions, and reviewing code. You can create repositories (repos) to store and organize projects. It also offers features like issue tracking and CI/CD workflows.

Jenkins

Jenkins is an automation tool for building and deploying software. It uses pipelines to automate tasks like testing, building, and deploying code. It supports plugins to integrate with various tools. Jenkins makes Continuous Integration and Continuous Delivery (CI/CD) easy.

Docker

Docker is a tool to create, share, and run lightweight virtualized environments called containers. Containers package code and dependencies together for consistent behavior across systems. It simplifies app deployment and avoids “it works on my machine” issues. Docker images are reusable blueprints for containers.

OWASP

OWASP (Open Web Application Security Project) focuses on improving web application security. It provides resources like the OWASP Top 10, a list of the most critical security risks in web apps. Developers use it as a guide to build secure applications. OWASP also creates free tools for security testing.

Trivy

Trivy is a security tool that scans containers, filesystems, and code for vulnerabilities. It detects issues like outdated dependencies and misconfigurations. Easy to use, it integrates with CI/CD pipelines to ensure secure builds. Trivy helps identify security risks early in the development process.

SonarQube

SonarQube analyzes code to find bugs, vulnerabilities, and code smells (poor practices). It gives a detailed report on code quality and suggests improvements. It supports multiple languages and integrates with CI/CD tools. SonarQube helps teams maintain clean, reliable, and secure code.

Node.js

Node.js is a runtime that lets you run JavaScript outside the browser. It’s great for building fast and scalable server-side applications. Node.js uses non-blocking, event-driven programming for high performance. It’s commonly used for web servers, APIs, and real-time apps.

Key Highlights:

Code Quality Assurance:

SonarQube is used to analyze source code, ensuring it adheres to coding standards and detecting bugs, code smells, and vulnerabilities.
Vulnerability Scanning:

Trivy scans container images for known vulnerabilities, helping to secure the application at the image level.

OWASP Dependency Check analyzes dependencies for vulnerabilities, ensuring no insecure libraries or frameworks are introduced.

Dockerized Environments:

All stages of the pipeline run in Dockerized containers, ensuring consistency and simplifying environment management.

CI/CD Workflow:

The pipeline automates build, test, and deploy processes, making it efficient and reliable.

Security and quality checks are seamlessly integrated into the pipeline, ensuring every release is both secure and robust.

Lets implement it step-by-step,

STEP-1: Launch EC2 Instance with below Configurations

AMI : Amazon Linux Kernel 5.10

Instance Type: T2.Large

EBS Volume : 20+ GB

Security Groups : All Traffic

STEP-2: Install Jenkins
#! /bin/bash
sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo 
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key
yum install java-17-amazon-corretto -y 
yum install jenkins -y
systemctl start jenkins.service 
systemctl enable jenkins.service 
systemctl status jenkins.service

STEP-3: Install Docker & GIT
yum install docker git -y
systemctl start docker
systemctl status docker
chmod 777 ///var/run/docker.sock

STEP-4: Install Sonarqube
docker run -d --name sonar -p 9000:9000 sonarqube:lts-community

Now our sonarqube is up and running

Now our sonarqube is up and running on Port :9000
![image](https://github.com/user-attachments/assets/c4cea82d-7f65-4546-9668-345a03c8209e)

username - admin
password - admin

Now our sonarqube is up and running
![image](https://github.com/user-attachments/assets/41fe59a6-80f3-4583-8ead-e47db4854b3f)


Update New password, This is Sonar Dashboard.
![image](https://github.com/user-attachments/assets/711fded6-7828-4989-99cb-9675d7788efe)



STEP-5: Install Trivy
wget https://github.com/aquasecurity/trivy/releases/download/v0.18.3/trivy_0.18.3_Linux-64bit.tar.gz
tar zxvf trivy_0.18.3_Linux-64bit.tar.gz
sudo mv trivy /usr/local/bin/
echo 'export PATH=$PATH:/usr/local/bin/' >> ~/.bashrc
source .bashrc
trivy --version

STEP-6: Install the following dependencies on Jenkins

Sonarqube Scanner

Eclipse Temurin Installer

NodeJS

OWASP Dependency-Check

Docker Pipeline

STEP-7: Integrate all the tools to Jenkins

Go to Jenkins Dashboard → Manage Jenkins → Tools

JDK installations — v17
![image](https://github.com/user-attachments/assets/0d8f88c8-0ffa-4e22-ae64-7bcc3cebd970)



NodeJS installations — v 16
![image](https://github.com/user-attachments/assets/0199e591-b63d-4d9a-844f-b000b303af1d)


Dependency-Check installations
![image](https://github.com/user-attachments/assets/090f5e32-0b66-4e21-95d6-395c2be4f0de)


SonarQube Scanner installations
![image](https://github.com/user-attachments/assets/7e7936b4-c95f-4e8a-9ee9-40dcda6e1e38)

After that click on apply

STEP-8: Configure Sonar with Jenkins

Goto your Sonarqube Server. Click on Administration → Security → Users → Click on Tokens and Update Token → Give it a name → and click on Generate Token
![image](https://github.com/user-attachments/assets/e13e5325-77a8-4bb2-876d-08629b199fb5)


click on update Token
![image](https://github.com/user-attachments/assets/edef5fba-41c2-4a41-9cae-d7ca3dcc713a)


Create a token with a name and generate
![image](https://github.com/user-attachments/assets/81b59b21-1103-4180-8058-702bb15d3a69)
copy Token


Goto Jenkins Dashboard → Manage Jenkins → Credentials → System → Global credentials (unrestricted) → Add Secret Text
![image](https://github.com/user-attachments/assets/57c3037d-5223-428a-9bd8-850f66c5eda7)


Enter detail and click on create
![image](https://github.com/user-attachments/assets/c67173e2-32f9-4312-a89a-ac3314bd25fa)

Now, go to Dashboard → Manage Jenkins → System and Add like the below image.
![image](https://github.com/user-attachments/assets/0bfb02e4-0cfb-464d-804f-b911f22f750c)

Click on Apply and Save

STEP-9: Add Dockerhub Credentials

Goto Jenkins Dashboard → Manage Jenkins → Credentials → System → Global credentials (unrestricted) → Add Username and password

username : dockerhub-username

password: dockerhub-password

credentials id : docker-password

Click on Apply and Save

STEP-10: Create a Jenkins Job

Goto Jenkins Dashboard create a job as My-Deployment Name, select pipeline and click on ok.
pipeline {
    agent any
    tools {
        jdk 'jdk17'
        nodejs 'node16'
    }
    environment {
        SCANNER_HOME=tool 'sonar-server'
    }
    stages {
        stage ("clean workspace") {
            steps {
                cleanWs()
            }
        }
        stage ("Checkout from Git") {
            steps {
                git branch: 'main', url: 'https://github.com/devops0014/Zomato-Repo.git'
            }
        }
        stage("Sonarqube Analysis") {
            steps{
                withSonarQubeEnv('mysonar') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=zomato \
                    -Dsonar.projectKey=zomato '''
                }
            }
        }
        stage ("quality gate") {
            steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'Sonar-Token'
                }
            }
        }
        stage ("Install dependencies") {
            steps {
                sh 'npm install'
            }
        }
        stage ("OWASP FS SCAN") {
            steps {
                dependencyCheck additionalArguments: '--scan ./ --disableYarnAudit --disableNodeAudit', odcInstallation: 'DP-Check'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        stage ("TRIVY FS SCAN") {
            steps {
                sh "trivy fs . > trivyfs.txt"
            }
        }
        stage("Docker Build & Push"){
            steps{
                script{
                    withDockerRegistry(credentialsId: 'docker-password') {
                        sh 'docker build -t image1 .'
                        sh "docker tag image1 devopsprashanth/zomato:mydockerimage"
                        sh "docker push devopsprashanth/zomato:mydockerimage"
                    }
                }
            }
        }
        stage ("TRIVY") {
            steps {
                sh 'trivy image devopsprashanth/zomato:mydockerimage'
            }
        }
        stage ("Deploy to container") {
            steps {
                sh 'docker run -d --name zomato -p 3000:3000 devopsprashanth/zomato:mydockerimage'
            }
        }
    }
}


If you click on Build Now pipeline will start again
![image](https://github.com/user-attachments/assets/816dc5c0-b762-458c-bae0-d7a42c8cb749)



Dependency-Chek Trend
![image](https://github.com/user-attachments/assets/e4eeb67c-ffa1-4f23-bddd-8dbf81a862bd)



Sonarqube report
![image](https://github.com/user-attachments/assets/4fd32d4a-bb66-43c8-a620-68dc854c63c3)



Dependency-Check Results
![image](https://github.com/user-attachments/assets/c4d80f05-06c5-4981-a3c2-47aab7e2f395)



Docker hub registry image
![Screenshot (149)](https://github.com/user-attachments/assets/0ef23368-82c0-4782-800a-0825bd5ede68)



stage view
![image](https://github.com/user-attachments/assets/285b1a05-d1b2-4658-aae3-0f7a82f323f9)

Deployed website



http://<Jenkins-public-ip:3000\>
![image](https://github.com/user-attachments/assets/39f8072e-e114-4e1c-bfec-cb79c626782a)


