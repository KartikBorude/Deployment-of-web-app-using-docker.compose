# Deployment-of-web-app-using-docker.compose
## Launch EC2 Instance (ubuntu)
-- Go to root directory


```shell
sudo -i
```

-- update the instance

```shell
apt update
```
## Prerequisities of the project for frontend ,backend and database

For backend

--Java Development Kit (JDK 17 or higher) installed.

--Maven installed.

--Spring Boot application source code or JAR file.

For frontend

--Install Node.js and npm

For Database

--Install MariaDB

--clone the repository from github
```shell
git clone https://github.com/Rohit-1920/EasyCRUD.git
```

## Database-setup

-- install mysql-client

```shell
apt install mysql-client -y
```
-- login to mysql and connect the database

```shell
mysql -h (endpoint of the database which we have created) -u admin -p
mysql -h database-1.cxaywyasm64g.ap-southeast-2.rds.amazonaws.com -u admin -p
```
enter the root password

create new database and user

```shell
CREATE DATABASE student_db;
```
```shell
GRANT ALL PRIVILEGES ON springbackend.* TO 'username'@'localhost' IDENTIFIED BY 'your_password';
```
replace the username and your_password which you have created
```shell
use student_db;
```
```shell
CREATE TABLE `students` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT,
  `name` varchar(255) DEFAULT NULL,
  `email` varchar(255) DEFAULT NULL,
  `course` varchar(255) DEFAULT NULL,
  `student_class` varchar(255) DEFAULT NULL,
  `percentage` double DEFAULT NULL,
  `branch` varchar(255) DEFAULT NULL,
  `mobile_number` varchar(255) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=80 DEFAULT CHARSET=latin1 COLLATE=latin1_swedish_ci;
```
```shell
show databases;
```

Exit mariaDB
```shell
exit
```

``Other requirements for deployment 

```shell
apt install docker.io -y
apt install docker-compose -y
```

## Backend

change the working directory to backend using the command

```shell
cd Easycrud/backend/
```
copy the file(application.properties) to present directory

```shell
cp src/main/resources/application.properties .
```
Edit the file application.properties

```shell
nano application.properties
```
create dockerfile for backend

```shell
nano dockerfile
```
Data for dockerfile

```shell
FROM maven:3.8.3-openjdk-17
COPY . /opt 
WORKDIR /opt
RUN rm -rf src/main/resources/application.properties
RUN cp -rf application.properties src/main/resources
RUN mvn clean package
WORKDIR /opt/target
EXPOSE 8080
CMD ["java" , "-jar" , "student-registration-backend-0.0.1-SNAPSHOT.jar"]
```
--Check the created dockerfile

```shell
ls
```


## Frontend

--change the working directory to frontend

```shell
cd Easycrud/fronted/
```
-- Edit the .env file

```shell
nano .env
```
change the public IP

VITE_API_URL = "http://public IP of instance:8080/api" and save the .env file

--create dockerfile for frontend

```shell
nano dockerfile
```
Data for dockerfile

```shell
FROM node:25-alpine3.21
COPY . /opt
WORKDIR /opt
RUN apk update -y
RUN apk add apache2
RUN npm install
RUN npm run build
RUN cp -rf dist/* /var/www/localhost/htdocs/
EXPOSE 80
CMD ["httpd" , "-D" , "FOREGROUND"]
```
Back to Easycrud

```shell
cd ..
```
Create the dockerfile of docker-compose

--Content for dockerfile
```shell
version: "3.8"
services: 
  backend:
    build:
      context: ./backend
      dockerfile: dockerfile
    ports:
      - "8080:8080"
 

  frontend:
    build: 
      context: ./frontend 
      dockerfile: dockerfile
    ports:
      - "80:80" 
    depends_on:
       - backend
```
## Docker-compose install , Build ,Up

install
```shell
apt install docker-compose -y
```
Build
```shell
docker-compose build --no-cache
```
up
```shell
docker-compose up -d
```
## search the public ip on chrome

