
# A-Connect

![Current Version](https://img.shields.io/badge/version-v0.0.1-blue)  ![GitHub contributors](https://img.shields.io/badge/contributors-5-blue) ![Owners](https://img.shields.io/badge/owner-Racloop-brightgreen) ![Java](https://img.shields.io/badge/java-17-brightgreen) ![Maintained](https://img.shields.io/badge/Maintained-yes-brightgreen) ![type](https://img.shields.io/badge/type-connector-blue)


A-Connect is Ayushman Connect , a headless plugin to make the life easier for any HMIS, LMIS, Health Applications , Health Locker provider or any other entity who want to be ABDM Compliance to share or receive the Health Records. It has SCAN-GATEWAY to avail scan share at Hospital.

## Table of Contents
- [Getting Started](#getting-started)
	- [Tools Required](#tools-required)
	- [Installation](#installation)
- [Deployment](#deployment)
	- [Using docker compose](#using-docker-compose)
	- [Using Ubuntu local](#ubuntu-installation)
		- [Install Java](#java-installation)
		- [Install Maven](#maven-installation) 
		- [Postgres Installation](#postgres-installation)
		-  [MongoDB Installation](#mongo-installation)
		- [Keycloak Installation](#keycloak-installation)
		- [Kafka Installation](#kafka-installation)
		- [Zookeeper Installation](#zookeeper-installation)
		- [Redis Installation](#redis-installation)
		 
- [Contributing](#contributing)
- [Versioning](#versioning)
- [Authors](#authors)
- [License](#license)

## Getting Started

The project might have multiple branches: `master`, `development`, etc. which can be explained here

* `master` contains aggregate code of all branches
* `development` contains code under development

Other details that need to be given while starting out with the project can be provided in this section. A project structure like below can also be included for the big projects:

```
	A-connect
	├── README.md
	├── pom.xml
	├── .gitignore
	├── docker-comspose.yaml
	├── public
	├── ABDM-M1
	├── ABDM-M2
	├── api-gateway
	├── audit-managment
	├── auth-service
	├── hapi_fhir-wrapper
	├── otp-service-backend
	├── scan-gateway-backend
	├── tracelyfe-backend
```

### Tools Required

All tools required go here. You would require the following tools to develop and run the project:

* Intellij Idea CE or UE
* Java version 17 or above(open-jdk recommended)
* Git 
* Docker desktop
* maven

### Installation & Development

A very comon Installtion for the development of the code

* Clone the project from the github respository (git should be installed in the system)
 ```bash
 ## Open the cmd or the terminal and clone the repository
 git clone https://github.com/racloop/A-connect.git
 ```
 
Open the Intellij IDEA and open this A-connect folder inside this
* Let the intellij complete the indexing 
* To change the jdk -version to open-17 jdk 
* Navigate to File-> Project Structure -> Platform Settings -> SDKs -> '+' Button-> Dowload JDK -> Select open-17-jdk
* Once the jdk is installed and build is complete you can run each service using the run -> arrow that would be appearing in the Intellij.

## Deployment

### Using docker compose
This makes up for the installation using the docker compose file inside the `A-connect` folder
commands:
```bash
cd A-connect/
docker-compose up
```
This would run all the services required for running A-connect module.
Now run each module/microservice in the Intellij IDEA CE or UE

### Using ubuntu local
#### Install Java
```bash
sudo apt update && sudo apt upgrade
sudo apt install openjdk-17-jre
sudo  apt  install openjdk-17-jdk
## Check installation 
java --version
```
#### Install Maven
```bash
sudo apt update
sudo apt install maven
## Check the installation 
mvn --version
```

#### Postgres Installation
```shell
sudo apt update
sudo apt install postgresql postgresql-contrib
sudo service postgresql status
```
Now if you find the status of the service running successfully, it means the postgres is working successfully.
Setting up Admin username and password for this postgres database
```sql
sudo -u postgres psql
ALTER USER postgres PASSWORD 'myPassword';
```

*Optional*: If you wan to access this database from any IP change *listen_address* field in the file directory given below to `listen_addresses="*"`
```sql
/etc/postgresql/{POSTGRES_VERSION}/main/postgresql.conf
```
Let's create a database for the keycloak inside the postgres
```bash
sudo -u postgres psql # this will login through the postgres and promt you to enter the password which was passed in the `myPassword`.
CREATE DATABASE keycloak;
\q #to exit out of the shell
```

#### MongoDB Installation
First, add the GPG key
```bash
wget -qO - https://www.mongodb.org/static/pgp/server-6.0.asc | sudo apt-key add -
```
Next, we need to create a list file for MongoDB
```bash
echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/6.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-6.0.list
```
Then, update the APT package list.
```bash
sudo apt-get update
```
Now it’s time to install MongoDB
```bash
sudo apt-get install -y mongodb-org
sudo systemctl start mongod
sudo systemctl status mongod
```
Now the status will show as mongo db running indicating successful installation of the mongodb.  
*Optional*: If you want to connect to this database from any other IP you need to configure this file
```bash
nano /etc/mongod.conf
```
and change the IP bind from `127.0.0.1` to `0.0.0.0` 
#### Keycloak Installation
Get the latest keycloak zip from [Keycloak Downloads page](https://www.keycloak.org/downloads)
```bash
# Download the zip file using wget
wget https://github.com/keycloak/keycloak/releases/download/21.0.1/keycloak-21.0.1.zip
# Extract the zip file
unzip keycloak-21.0.1.zip
# Get inside the Keycloak folder to change the configuration
cd keycloak-21.0.1/conf/
```
Update the `keycloak.conf` in this directory as per the requirements
```bash
# Basic settings for running in production. Change accordingly before deploying the server.

# Database

# The database vendor.
db=postgres

# The username of the database user.
db-username=postgres

# The password of the database user as passed while creating the postgres database admin.
db-password=password

# The full database JDBC URL. If not provided, a default URL is set based on the selected database vendor. Where keycloak is the name of the database we created in th e posgres.
db-url=jdbc:postgresql://localhost/keycloak

#port
http-port=8090
```
To setup the keycloak Admin username and password use the export command
```bash
export KEYCLOAK_ADMIN=KEYCLOAK_ADMIN_NAME
export KEYCLOAK_ADMIN_PASSWORD=KEYCLOAK_ADMIN_PASSWORD
```
These will be used to login to the UI of the Keycloak
#### Kafka Installation
```bash
wget https://archive.apache.org/dist/kafka/2.6.1/kafka_2.12-2.6.1.tgz
tar xzf kafka_2.12-2.6.1.tgz
sudo mv -f kafka_2.12-2.6.1 /opt
sudo ln -s kafka_2.12-2.6.1 /opt/kafka
vim ~/.profile
export KAFKA_HOME=/opt/kafka 
export PATH=$PATH:${KAFKA_HOME}/bin
source ~/.profile
```
The kafka installation is successful we can start the kafka using the command
```shell
# Start Zookeeper
zookeeper-server-start.sh -daemon /opt/kafka/config/zookeeper.properties
# Start Kafka
kafka-server-start.sh -daemon /opt/kafka/config/server.properties
# We can check if zookeeper and Kafka is running using netstat command
netstat -tulpn | grep 2181
netstat -tulpn | grep 9092
```
#### Redis Installation
```bash
sudo apt update
sudo apt install redis
sudo systemctl status redis
```
You can see the redis is running in the status command output

_Optional_: If you want to change the configuration for the redis you can do it by changing inside this file
```shell
/etc/redis/redis.conf
```
## Contributing

Mention what you expect from the people who want to contribute

We'd love to have your helping hand on `Project Title`! See [CONTRIBUTING.md] for more information on what we're looking for and how to get started.

## Versioning

If your project has multiple versions, include information about it here. 

For the available versions, see the [tags on this repository][tags]

## Authors
- [Pankaj Narang](https://in.linkedin.com/in/transformationarchitect)
- [Gaurav Chandana](https://github.com/gauravracloop)
- [Puneet Sharma](https://github.com/pksharma14)
- [Ashish Uniyal](https://github.com/bertoxious)

## License

`A Connect` is open HIMS system providing linkage of Health records as per [NHA](https://nha.gov.in/) norms.  Software is licensed under the parent company Racloop Technologies Pvt. Limited [NHA HMIS Partner](https://abdm.gov.in/partners/health-tech).
[Certified](https://abdm.gov.in:8081/uploads/Racloop_techmologies_43049e85fb.pdf)
