
## Susbcribe:
](https://www.youtube.com/@DevopsWithRanjeet?sub_confirmation=1)

# Deploy Netflix Clone on Cloud using Jenkins - DevSecOps Project!

Deploy T2.Large Instance for the Jenkins,SonarCube And Trivy
Add the security group such as port 80, Then Port 443 and 22
Add storage as 25 Gb
Update the package using command sudo apt update >> This will update the package 
Clone the project using command git clone https://github.com/devranj/Netflix-Clone-DevSecops.git

sudo apt-get update
sudo apt-get install docker.io -y
sudo usermod -aG docker ubuntu  # Replace with your system's username, e.g., 'ubuntu'
newgrp docker
sudo chmod 777 /var/run/docker.sock 
git clone https://github.com/devranj/DevSecOps-Project.git
docker build -t netflix .
docker run -d -p 8081:80 

Install firewall 
sudo apt install firewalld
systemctl status firewalld
systemctl stop firewalld

#to delete
docker stop <containerid>
docker rmi -f netflix

Steps To Get An API key
Step 4: Get the API Key:

Open a web browser and navigate to TMDB (The Movie Database) website.
Click on "Login" and create an account.
Once logged in, go to your profile and select "Settings."
Click on "API" from the left-side panel.
Create a new API key by clicking "Create" and accepting the terms and conditions.
Provide the required basic details and click "Submit."
You will receive your TMDB API key.

https://www.themoviedb.org/u/ranjeetk
Username:ranjeetk
password:arst@dm1n
API Key: 8f2f00852d0c7e75eeeae3b96e801da5

docker build --build-arg TMDB_V3_API_KEY=8f2f00852d0c7e75eeeae3b96e801da5 -t netflix .

Run the command 
docker run -d -p 8081:80
If you are getting an error that port is not listening follow this below steps 
sudo service docker stop
sudo rm /var/lib/docker/network/files/local-kv.db
sudo service docker start

Install trivy In Box >> Acess 9000 Port Defaulty Username Is admin and password Is admin

sudo apt-get install wget apt-transport-https gnupg lsb-release
wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | sudo apt-key add -
echo deb https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main | sudo tee -a /etc/apt/sources.list.d/trivy.list
sudo apt-get update
sudo apt-get install trivy        
trivy image netflix
trivy fs .

Integrate SonarQube and Configure
docker run -d --name sonar -p 9000:9000 sonarqube:lts-community
docker ps >> to check the sttaus of our copntainer

Install Jenkins And Configure
sudo apt update
sudo apt install fontconfig openjdk-17-jre
java -version
openjdk version "17.0.8" 2023-07-18
OpenJDK Runtime Environment (build 17.0.8+7-Debian-1deb12u1)
OpenJDK 64-Bit Server VM (build 17.0.8+7-Debian-1deb12u1, mixed mode, sharing)

#jenkins
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
/etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins
sudo systemctl start jenkins
sudo systemctl enable jenkins

Access Jenkins in a web browser using the public IP of your EC2 instance.
publicIp:8080
Get the password : cat /var/lib/jenkins/secrets/initialAdminPassword

Install Necessary Plugins in Jenkins:

Goto Manage Jenkins →Plugins → Available Plugins →

Install below plugins

1 Eclipse Temurin Installer (Install without restart)

2 SonarQube Scanner (Install without restart)

3 NodeJs Plugin (Install Without restart)

4 Email Extension Plugin

Configure Java and Nodejs in Global Tool Configuration
Goto Manage Jenkins → Tools → Install JDK(17) and NodeJs(16)→ Click on Apply and Save

JDK Installation:
Name:jdk17
Install automnatically 
Install from adoptium.net
version >> jdk-17.0.8.1+1

Name >> Node16
Version:Nodejs 16.2.0

SonarQube
Create the token y following steps 
Go to sonar cube >> click on administration >> click on security >> >> click on administrator >> Under that find user >> click on update token >> give name as Jenkins
get the token copy 
Token >> squ_361a02cfc04a48cfc93f373918105ade4bda6af9

Now 
Goto Jenkins Dashboard → Manage Jenkins → Credentials → global >> Click on secret text >> Add Secret Text. It should look like this
ID >> sonar-token
description >> sonar-token 

After adding sonar token
Click on Apply and Save

The Configure System option is used in Jenkins to configure different server
Global Tool Configuration is used to configure different tools that we install using Plugins
We will install a sonar scanner in the tools.

go back to manage jenkins 
Click on system
Serach for sonarcube
Click on Add 
Enter name as sonar-server
Enter URL http://13.232.28.128:9000
Select the token which just now created 

We need add one more tool >> sonar-scanner 
Click on manage jenkins 
Click on tools
Search for Sonar qube installation 
name it as "sonar-scanner"
version should be default 

We are all set to create a pipeline 
Go to dashBoard 
Click on new item 
give name as "NetFlix" and select pipeline 
Click on Pipeline
Paste the code >> Take from repo
pipeline {
    agent any
    tools {
        jdk 'jdk17'
        nodejs 'node16'
    }
    environment {
        SCANNER_HOME = tool 'sonar-scanner'
    }
    stages {
        stage('clean workspace') {
            steps {
                cleanWs()
            }
        }
        stage('Checkout from Git') {
            steps {
                git branch: 'main', url: 'https://github.com/devranj/Netflix-Clone-DevSecops.git'
            }
        }
        stage("Sonarqube Analysis") {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh '''$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Netflix \
                    -Dsonar.projectKey=Netflix'''
                }
            }
        }
        stage("quality gate") {
            steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'Sonar-token'
                }
            }
        }
        stage('Install Dependencies') {
            steps {
                sh "npm install"
            }
        }
    }
}



Later on Go to sonarcube 
Click On Projects
Enter Project name As : Netflix 
Enter Project Key As : Netflix 
Select Main branch as "main"
Click on setup 
Click on locally 
Click on generate 
Click on continue 
Click on other
Click on linux 

Install Dependency-Check and Docker Tools in Jenkins

Go to "Dashboard" in your Jenkins web interface.
Navigate to "Manage Jenkins" → "Manage Plugins."
Click on the "Available" tab and search for "OWASP Dependency-Check."
Check the checkbox for "OWASP Dependency-Check" and click on the "Install without restart" button.

Go to "Dashboard" in your Jenkins web interface.
Navigate to "Manage Jenkins" → "Manage Plugins."
Click on the "Available" tab and search for "Docker."
Check the following Docker-related plugins:
Docker
Docker Commons
Docker Pipeline
Docker API
docker-build-step
Click on the "Install without restart" button to install these plugins.




Configure Dependency-Check Tool:

After installing the Dependency-Check plugin, you need to configure the tool.
Go to "Dashboard" → "Manage Jenkins" → "Global Tool Configuration."
Find the section for "Dependency-Check."
Add the tool's name, e.g., "DP-Check."
Install from github
Version can be the latest 
Save your settings.

Configure Docker Tools and Docker Plugins Following the same
After installing the Dependency-Check plugin, you need to configure the tool.
Go to "Dashboard" → "Manage Jenkins" → "Global Tool Configuration."
Find the section for "Docker."
Add the tool's name, e.g., "docker"
Install from docker
Version can be the latest 
Save your settings.



Add DockerHub Credentials:

To securely handle DockerHub credentials in your Jenkins pipeline, follow these steps:
Go to "Dashboard" → "Manage Jenkins" → "Manage Credentials."
Click on "System" and then "Global credentials (unrestricted)."
Click on "Add Credentials" on the left side.
Choose "Secret text" as the kind of credentials.
Enter your DockerHub credentials (Username and Password) and give the credentials an ID (e.g., "docker").
Click "OK" to save your DockerHub credentials.
Enter id : docker 
Description: docker 

Docker Credentials
Email:sainiranjeet936@gmail.com
Username:ranjeet1512
Password:arst@dm1n

After this now we have to stop and delete the image related to netflix to reimort from Docker 
docker ps 
docker stop 
docker rm 
Recheck by seeing docker ps command 

Now go to your existing pipeline and made the changes with respect to API 
Enter into configure and update the APi key 

pipeline{
    agent any
    tools{
        jdk 'jdk17'
        nodejs 'node16'
    }
    environment {
        SCANNER_HOME=tool 'sonar-scanner'
    }
    stages {
        stage('clean workspace'){
            steps{
                cleanWs()
            }
        }
        stage('Checkout from Git'){
            steps{
                git branch: 'main', url: 'https://github.com/devranj/Netflix-Clone-DevSecops.git'
            }
        }
        stage("Sonarqube Analysis "){
            steps{
                withSonarQubeEnv('sonar-server') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Netflix \
                    -Dsonar.projectKey=Netflix '''
                }
            }
        }
        stage("quality gate"){
           steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'Sonar-token' 
                }
            } 
        }
        stage('Install Dependencies') {
            steps {
                sh "npm install"
            }
        }
        stage('OWASP FS SCAN') {
            steps {
                dependencyCheck additionalArguments: '--scan ./ --disableYarnAudit --disableNodeAudit', odcInstallation: 'DP-Check'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        stage('TRIVY FS SCAN') {
            steps {
                sh "trivy fs . > trivyfs.txt"
            }
        }
        stage("Docker Build & Push"){
            steps{
                script{
                   withDockerRegistry(credentialsId: 'docker', toolName: 'docker'){   
                       sh "docker build --build-arg TMDB_V3_API_KEY=8f2f00852d0c7e75eeeae3b96e801da5 -t netflix ."
                       sh "docker tag netflix ranjeet1512/netflix:latest "
                       sh "docker push ranjeet1512/netflix:latest "
                    }
                }
            }
        }
        stage("TRIVY"){
            steps{
                sh "trivy image nasi101/netflix:latest > trivyimage.txt" 
            }
        }
        stage('Deploy to container'){
            steps{
                sh 'docker run -d netflix -p 8081:80 ranjeet1512/netflix:latest'
            }
        }
    }
}


Now We need to set Up A Monitoring Server, in that case set up a dedicated servere 
Install T2.Medium server >> ubuntu

Install Prometheus and Grafana:
Set up Prometheus and Grafana to monitor your application.
Installing Prometheus:
First, create a dedicated Linux user for Prometheus and download Prometheus:
sudo useradd --system --no-create-home --shell /bin/false prometheus
wget https://github.com/prometheus/prometheus/releases/download/v2.47.1/prometheus-2.47.1.linux-amd64.tar.gz
tar -xvf prometheus-2.47.1.linux-amd64.tar.gz
cd prometheus-2.47.1.linux-amd64/
sudo mkdir -p /data /etc/prometheus
sudo mv prometheus promtool /usr/local/bin/
sudo mv consoles/ console_libraries/ /etc/prometheus/
sudo mv prometheus.yml /etc/prometheus/prometheus.yml
sudo chown -R prometheus:prometheus /etc/prometheus/ /data/
sudo nano /etc/systemd/system/prometheus.service

Add the following content 
[Unit]
Description=Prometheus
Wants=network-online.target
After=network-online.target

StartLimitIntervalSec=500
StartLimitBurst=5

[Service]
User=prometheus
Group=prometheus
Type=simple
Restart=on-failure
RestartSec=5s
ExecStart=/usr/local/bin/prometheus \
  --config.file=/etc/prometheus/prometheus.yml \
  --storage.tsdb.path=/data \
  --web.console.templates=/etc/prometheus/consoles \
  --web.console.libraries=/etc/prometheus/console_libraries \
  --web.listen-address=0.0.0.0:9090 \
  --web.enable-lifecycle

[Install]
WantedBy=multi-user.target

sudo systemctl enable prometheus
sudo systemctl start prometheus
sudo systemctl status prometheus

Add Port 9090 on security group 

Extract Node Exporter files, move the binary, and clean up:
sudo useradd --system --no-create-home --shell /bin/false node_exporter
wget https://github.com/prometheus/node_exporter/releases/download/v1.6.1/node_exporter-1.6.1.linux-amd64.tar.gz
tar -xvf node_exporter-1.6.1.linux-amd64.tar.gz
sudo mv node_exporter-1.6.1.linux-amd64/node_exporter /usr/local/bin/
rm -rf node_exporter*
sudo nano /etc/systemd/system/node_exporter.service
[Unit]
Description=Node Exporter
Wants=network-online.target
After=network-online.target

StartLimitIntervalSec=500
StartLimitBurst=5

[Service]
User=node_exporter
Group=node_exporter
Type=simple
Restart=on-failure
RestartSec=5s
ExecStart=/usr/local/bin/node_exporter --collector.logind

[Install]
WantedBy=multi-user.target

After this now go to 
cd /etc/prometheus
Edit the prometheus.yml file 

global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'node_exporter'
    static_configs:
      - targets: ['localhost:9100']

  - job_name: 'jenkins'
    metrics_path: '/prometheus'
    static_configs:
      - targets: ['<your-jenkins-ip>:<your-jenkins-port>']
	  

Check the validity of the configuration file:
promtool check config /etc/prometheus/prometheus.yml

Reload the Prometheus configuration without restarting:
curl -X POST http://localhost:9090/-/reload
You can access Prometheus targets at:

http://<your-prometheus-ip>:9090/targets

Install Grafana on Ubuntu 22.04 and Set it up to Work with Prometheus

sudo apt-get update
sudo apt-get install -y apt-transport-https software-properties-common
wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -
sudo apt-get update
sudo apt-get -y install grafana
sudo systemctl enable grafana-server
sudo systemctl start grafana-server
sudo systemctl status grafana-server
http://43.205.241.227:3000/login
You'll be prompted to log in to Grafana. The default username is "admin," and the default password is also "admin."


Step 9: Add Prometheus Data Source:
To visualize metrics, you need to add a data source. Follow these steps:
Click on the gear icon (⚙️) in the left sidebar to open the "Configuration" menu.
Select "Data Sources."
Click on the "Add data source" button.
Choose "Prometheus" as the data source type.
In the "HTTP" section:
Set the "URL" to http://localhost:9090 (assuming Prometheus is running on the same server).
Click the "Save & Test" button to ensure the data source is working.
Step 10: Import a Dashboard:
To make it easier to view metrics, you can import a pre-configured dashboard. Follow these steps:
Click on the "+" (plus) icon in the left sidebar to open the "Create" menu.
Select "Dashboard."
Click on the "Import" dashboard option.
Enter the dashboard code you want to import (e.g., code 1860).
Click the "Load" button.
Select the data source you added (Prometheus) from the dropdown.
Click on the "Import" button.
You should now have a Grafana dashboard set up to visualize metrics from Prometheus.
Grafana is a powerful tool for creating visualizations and dashboards, and you can further customize it to suit your specific monitoring needs.
That's it! You've successfully installed and set up Grafana to work with Prometheus for monitoring and visualization.

Integrate Jenkins with Prometheus to monitor the CI/CD pipeline.

Now go to Jenkins >> Manage Jenkins >> Availible plugin >> Prometheus 
Click On Manage Jenkins
Click On System 


Add your email and password in Jenkins credentails to make it work 
Email Integration With Jenkins and Plugin Setup
Install Email Extension Plugin in Jenkins
Go to your Gmail and click on your profile
Then click on Manage Your Google Account --> click on the security tab on the left side panel you will get this page(provide mail password).
2-step verification should be enabled.
Search for the app in the search bar you will get app passwords
Click on other and provide your name and click on Generate and copy the password
In the new update, you will get a password
Once the plugin is installed in Jenkins, click on manage Jenkins --> configure system there under the E-mail Notification section configure the details
Click on Apply and save.
Click on Manage Jenkins--> credentials and add your mail username and generated password
This is to just verify the mail configuration
Now under the Extended E-mail Notification section
Click on Apply and save
Reconfigure pipeline
post {
     always {
        emailext attachLog: true,
            subject: "'${currentBuild.result}'",
            body: "Project: ${env.JOB_NAME}<br/>" +
                "Build Number: ${env.BUILD_NUMBER}<br/>" +
                "URL: ${env.BUILD_URL}<br/>",
            to: 'devopswithranjeet@gmail.com',  #change Your mail
            attachmentsPattern: 'trivyfs.txt,trivyimage.txt'
        }
    }

Kuberenetes Setup
Take-Two Ubuntu 20.04 instances one for k8s master and the other one for worker.
Install Kubectl on Jenkins machine also.
Kubectl is to be installed on Jenkins also
sudo apt update
sudo apt install curl
curl -LO https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
kubectl version --client
sudo hostnamectl set-hostname K8s-Master
sudo hostnamectl set-hostname K8s-Worker
sudo apt-get update 
sudo apt-get install -y docker.io
sudo usermod –aG docker Ubuntu
newgrp docker
sudo chmod 777 /var/run/docker.sock
sudo curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
sudo tee /etc/apt/sources.list.d/kubernetes.list <<EOF
deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo snap install kube-apiserver
sudo kubeadm join <master-node-ip>:<master-node-port> --token <token> --discovery-token-ca-cert-hash <hash>
Copy the config file to Jenkins master or the local file manager and save it
copy it and save it in documents or another folder save it as secret-file.txt
create a secret-file.txt in your file explorer save the config in it and use this at the kubernetes credential section.
Install Kubernetes Plugin, Once it's installed successfully
goto manage Jenkins --> manage credentials --> Click on Jenkins global --> add credentials
