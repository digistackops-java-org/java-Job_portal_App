<img width="782" height="73" alt="image" src="https://github.com/user-attachments/assets/306f77dc-2486-4a9f-8d89-e181e401beed" /># DB Setup
## Launch EC2 "t2.micro" Instance and In Sg, Open port "27017" for MongoDB
### Create mondDB repo in YUM repository
```
sudo vim /etc/yum.repos.d/mongodb-org-8.0.repo
```
### Add MongoDB repo Details 
```
[mongodb-org-8.0]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/amazon/2023/mongodb-org/8.0/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://pgp.mongodb.com/server-8.0.asc
```
### Install mongoDB
```
sudo yum update -y
sudo yum install -y mongodb-org
```
### Start mongoDB
```
sudo systemctl daemon-reload
sudo systemctl enable mongod
sudo systemctl start mongod
sudo systemctl status mongod
```
## Setup MongoDB

#### Allow Remote Access
```
sudo vim /etc/mongod.conf
```
Replace 0.0.0.0 in bindIp
```
# network interfaces 
    net:   
       port: 27017   
       bindIp: 0.0.0.0 # to bind to all interfaces
```
##### Restart mongoDB
```
sudo systemctl restart mongod
```
# Backend Application Setup
## Launch EC2 "t2.micro" Instance and In Sg, Open port "8080" for JAVA Application 
#### Setup your Application Database by executing "initdb.js" script from Application-server

Step:1 ==> install "mongo-Client" for communicate with Mongo Database

```
sudo vim /etc/yum.repos.d/mongodb-org-8.0.repo
```
```
[mongodb-org-8.0]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/amazon/2023/mongodb-org/8.0/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://pgp.mongodb.com/server-8.0.asc
```
To install "Mongo-Shell" to communicate with Mongo database
```
sudo yum update -y
sudo yum install -y mongodb-mongosh
```
Step:2 ==> Execute your "initdb.js" script for your Application DB setup

```
mongosh "mongodb://candidate_user:pa55Word@<DB-Private-IP>:27017/candidate" < initdb.js
```
####  Install GIT
```
sudo yum install git -y
``` 

## Install JAVA
####  Installation of openJDK 17
```
sudo dnf update -y
sudo yum install java-17-amazon-corretto-devel -y
``` 

## Install Maven
```
sudo wget https://dlcdn.apache.org/maven/maven-3/3.9.11/binaries/apache-maven-3.9.11-bin.tar.gz
sudo tar xzf apache-maven-3.9.11-bin.tar.gz -C /opt
sudo ln -s apache-maven-3.9.11 /opt/maven
```
#### Create Profile for Maven  
```
sudo vi /etc/profile.d/maven.sh
```

```
export M2_HOME=/opt/maven
export PATH=${M2_HOME}/bin:${PATH}
```
#### Reload profile
```
sudo chmod +x /etc/profile.d/maven.sh
source /etc/profile.d/maven.sh
mvn -version
```

### create Application user for Executing Application
Generally in organization they create one Application user for the Project, HERE I am Creating user "candidate" 
user "candidate"  is a function (or) Daemon usr to run the Application, apart from that we don’t use this user to login to servers

```
sudo useradd candidate
```
### We keep application in one standard location. This is a usual practice that runs in the organization. Lets setup an app directory.
```
sudo mkdir /app
```

```
cd /app
sudo git clone https://github.com/digistackops-java-org/java-Job_portal_App.git
cd java-Job_portal_App
sudo chown -R candidate:candidate /app/java-Job_portal_App
```
Switch branch

```
git checkout 01-Local-setup-Prod-V1
sudo chown -R candidate:candidate /app/java-Job_portal_App
```
### Buikld the Package
```
cd job-service
mvn clean package
```
### Production Backend Setup
Start Backend Application, for HA we use Linux service for Backend
```
sudo vim /etc/systemd/system/backend.service
```
```
[Unit]
Description=Student Spring Boot App
After=network.target

[Service]
User=candidate
WorkingDirectory=/app/java-Job_portal_App/job-service/

ExecStart=/usr/bin/java -jar /app/java-Job_portal_App/job-service/target/studentapp-0.0.1-SNAPSHOT.jar
SuccessExitStatus=143
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
```
Enable the backens servive
```
sudo systemctl daemon-reload
sudo systemctl enable backend
sudo systemctl start backend
sudo systemctl status backend
```

