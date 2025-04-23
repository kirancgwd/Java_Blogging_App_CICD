**Java Blogging APP**

**Project Setup**

Github --> Jenkins --> Compile & Testing --> Trivy Fs Scan --> Quality Gate Check --> SonarQube --> Docker Image create --> Trivy Image Scan --> Docker Container Create --> Deploy to EKS Cluster --> Monitor

**Infra Setup**

1. VM or EC2  -- AWSCLI, Git, Terraform
2. CICD Server -- Jenkins AND Jenkins Slave -- java, Trivy, Docker, Kubectl
3. Code Analisys and Artifact Storage -- SonarQube, Nexus

**AWS Instance Creation**

We need 2 ubuntu servers with 25GB Storage

1. Jenkins(t2.large)
2. SonarQube and Nexus (t2.large )

**Setup Jenkins**
1. Install Java
```   
apt-get update
sudo apt install openjdk-17-jdk
```
3. Install Jekins
