# DB Setup
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
## Setup your Application Database by executing "initdb.js" script from Application-server

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
Step:2 ==> Execute your "init.sql" script for your Application DB setup

```
mongosh "mongodb://<DB-Private-IP>:27017/db" < initdb.js
``` 
### Pass our DB Credentials as Environment Variables 

```
export MONGO_USER=appuser
export MONGO_PASS=pa55Word
export MONGO_HOST=AWS-DB-Private-IP
export MONGO_DB=user-account
```
