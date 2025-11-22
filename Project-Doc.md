#  Hosting Multi-Tier Docker Deployment – Frontend | Backend | Database

##  Project Overview
This project demonstrates deployment of a complete **3-tier application** using Docker containers on a single AWS EC2 instance.

Architecture components:
- **Frontend** → Node/NPM 
- **Backend** → Java + Maven  
- **Database** → MySQL / MariaDB  
- **Hosting** → AWS EC2 (t2.medium)

---


##  Architecture Diagram
<img width="1000" height="563" alt="image" src="https://github.com/user-attachments/assets/3662094b-1aad-43ef-9661-1cb2ac77ee3e" />

---

##  Tech Stack

| Layer     | Technology |
|-----------|------------|
| Frontend  | Node/NPM |
| Backend   | Java, Maven |
| Database  | MySQL / MariaDB |
| Hosting   | AWS EC2 (t2.medium) |

---

## ✔️ Prerequisites
- AWS EC2 instance  
  - Type: **t2.medium**  
  - Storage: **30GB**  
- Docker Engine installed  
- Git installed  

---

###  A. Database Setup (MySQL / MariaDB)

<img width="1905" height="820" alt="Screenshot 2025-11-05 204035" src="https://github.com/user-attachments/assets/4692ce52-624f-4d93-8f48-b5077498d41d" />

#### 1️⃣ install  docker 
```bash
apt install docker.io
```
#### 2️⃣install mysql-client
```bash
apt install mysql-client -y
```
#### 3️⃣ Login to MySQL
```bash
mysql -h (endpoint ) -u admin -p
```
enter the root password :

#### 4️⃣ Create database
```bash
CREATE DATABASE student_db;

GRANT ALL PRIVILEGES ON springbackend.* TO 'username'@'localhost' IDENTIFIED BY 'your_password';

USE student_db;

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

exit;
```
#### 5️⃣ Backend connection details
```bash
DB_USER=<your_username>
DB_PASS=<your_password>
DB_PORT=3306
DB_NAME=student_db
DB_HOST=<database_container_ip>
```
---

### ⚙️ B. Backend Configuration (Java + Maven)

#### 1️⃣ Clone project repository
```sql
git clone <your_repo_url>
```
```bash
cd EasyCRUD/backend
```
#### 2️⃣ Update application.properties

**Copy and edit:**
```bash
cp src/main/resources/application.properties .
```
```bash
vim application.properties
```
**Update values:**
Replace localhost with DB container IP
Set correct DB username & password

<img width="867" height="375" alt="image" src="https://github.com/user-attachments/assets/78fc67da-e753-486b-893a-bea91209c8ec" />

#### 3️⃣ Backend Dockerfile

**Create backend/Dockerfile:**
```bash
nano dockerfile
```
**Content for backend dockerfile**

```bash
FROM maven:3.8.3-openjdk-17
COPY . /opt/
WORKDIR /opt
RUN rm -rf src/main/resources/application.properties
RUN cp application.properties src/main/resources/
RUN mvn clean package
WORKDIR /opt/target
EXPOSE 8080
CMD ["java","-jar","student-registration-backend-0.0.1-SNAPSHOT.jar"]
```
#### 4️⃣ Build backend image
```bash
docker build . -t backend-app 
```
#### 5️⃣ Run backend container
```bash
docker run -d -p 8080:8080 backend-app
```
---

###  C. Frontend Deployment (React.js + Apache)

#### 1️⃣ Update .env
```bash
cd EasyCRUD/frontend
```
```bash
vim .env
```
**Set:**
VITE_API_BASE_URL=http://<EC2_PUBLIC_IP>:8080

<img width="426" height="117" alt="image" src="https://github.com/user-attachments/assets/0503eb82-d974-46e5-8d84-c8bfe4a29576" />

#### 2️⃣ Frontend Dockerfile

**Create frontend/Dockerfile:**
```bash
nano dockerfile
```
**Content For frontend dockerfile**
```bash
FROM node:24-alpine
COPY . /opt
WORKDIR /opt
RUN npm install && npm run build
RUN apk update && apk add apache2
RUN cp -rf dist/* /var/www/localhost/htdocs/
EXPOSE 80
CMD ["httpd", "-D", "FOREGROUND"]
```

#### 3️⃣ Build frontend image
```bash
docker build . -t frontend-app 
```
#### 4️⃣ Run frontend container
```bash
docker run -d -p 80:80 frontend-app
```
<img width="1694" height="328" alt="Screenshot 2025-11-05 203026" src="https://github.com/user-attachments/assets/4c72d945-cfbd-498e-837e-5b69534a15ca" />

###  Final Step
```bash
Open your browser and visit:
http://<YOUR_EC2_PUBLIC_IP>
You should now see your website live! 🎉

```
<img width="1905" height="1011" alt="Screenshot 2025-11-05 202951" src="https://github.com/user-attachments/assets/0c5709a0-835a-4fed-9b0a-8fb22ee4198d" />

---

## To see data that saved in database

**🔍 1. See All Databases**
```bash
SHOW DATABASES;
```
**🔍 2. Select Your Database**
```bash
USE student_db;
```
**🔍 3. See All Tables**
```bash
SHOW TABLES;
```
**🔍 4. View All Data in a Table**
```bash
SELECT * FROM users;
```
<img width="1486" height="420" alt="Screenshot 2025-11-05 203849" src="https://github.com/user-attachments/assets/ff4cc41f-c2a6-482e-bd3d-65b325188665" />

