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
2. Install Jekins
```
curl -fsSL https://pkg.jenkins.io/debian/jenkins.io-2023.key | sudo tee /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc]  https://pkg.jenkins.io/debian binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins
sudo systemctl start jenkins
```
3. Install Docker
```
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update

sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
sudo usermod -aG docker jenkins
Restart jenkins
```
4. Install Trivy
```
sudo apt-get install wget apt-transport-https gnupg lsb-release
wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | sudo apt-key add -
echo deb https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main | sudo tee -a /etc/apt/sources.list.d/trivy.list
sudo apt-get update
sudo apt-get install trivy
```
5. Login Jenkins UI and install pluggins
   
IP-ADDRESS:8080
```
sonarqube-scanner
Config file provider
maven integeration
pipeline maven integration maven
nexus artifact uploader
docker
owasp
docke pipeline
eclipse temurin installer
pipeline.stage view
Kub client API
Kub Credentials
Kubernetes
Kub CLI
Kub Credentials provider
Generic webhook trigger
```

**Setp SonarQube and Nexus**
1. Install Docker
```
apt install docker.io
sudo usermod -aG docker $USER
newgrp docker
```
2. Create Sonarqube server using Docker image
```
docker run -d --name sonar -p 9000:9000 --restart=always sonarqube:latest
```
3. Create Nexus Server using Docker image
```
docker run -d --name nexus -p 8081:8081 -p 5000:5000 --restart=always  sonatype/nexus3
```
4. Add Nexus URL in pom.xml
   
Login to Nexus --> browse --> copy maven releases and maven snapshots ---> paste in URL added in pom.xml

![image](https://github.com/user-attachments/assets/b67c6a9b-7bbe-4109-ae4a-2c9492ba9bbb)

5.  Edit pom.xml

 ![image](https://github.com/user-attachments/assets/f60c1554-2e75-41d9-ac1a-34d99acec1a0)

6. Create gloabl maven configuration in jenkins managed files
   
Goto managed files  --> add new config --> select Global Maven settings.xml --> give ID as Global-maven --> add below config under server --> username and password of nexus

```
-->
    <server>
      <id>maven-releases</id>
      <username>admin</username>
      <password>kirancgwd</password>
    </server>
    
    <server>
      <id>maven-snapshots</id>
      <username>admin</username>
      <password>kirancgwd</password>
    </server>
    -->
```

**Add Docker sonarqube Credentials in Jenkins**

1. Create token in Sonarqube and add in jenkins
2. Take Docker registry username and password & add it in Jenkins
3. Add Sonar server in Jenkins System configuration by selecting Sonarqube credentials.

**Create pipeline using Jenkins Files**



