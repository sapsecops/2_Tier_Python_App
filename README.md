# Database Setup
Create "t2.micro" EC2 Instance and open port "3306" for DB 

## Install MYSQL DB
```
sudo yum update -y
sudo wget https://dev.mysql.com/get/mysql80-community-release-el9-1.noarch.rpm
sudo dnf install mysql80-community-release-el9-1.noarch.rpm -y
sudo rpm --import https://repo.mysql.com/RPM-GPG-KEY-mysql-2023
sudo dnf install mysql-community-client -y
sudo dnf install mysql-community-server -y
sudo systemctl start mysqld
sudo systemctl enable mysqld
sudo systemctl status mysqld
```

## Setup MYSQL DB

#### Allow any Host connect to DB
```
sudo vi /etc/my.cnf
```
ADD these Under [mysqld]
```
bind-address = 0.0.0.0
```
Restart MYSQL DB
```
sudo systemctl restart mysqld
```
Get your temporary root Password
```
sudo grep 'temporary password' /var/log/mysqld.log
```
Setup your root Password
```
sudo mysql_secure_installation
```
Login to your MYSQL
```
mysql -u root -p
```
Test it is working or Not
```
SELECT VERSION();
```
### Create one Databse Admin User for our DB 
These user can login to DB to do Tasks and used 
```
CREATE USER '<user-name>'@'Host-IP' IDENTIFIED BY 'Password-HERE';
GRANT ALL PRIVILEGES ON <DB-Name>.* TO '<user-name>'@'Host-IP';
FLUSH PRIVILEGES;
```

```
CREATE USER 'dbadmin'@'%' IDENTIFIED BY 'Admin@123';
GRANT ALL PRIVILEGES ON *.* TO 'dbadmin'@'%' WITH GRANT OPTION;
FLUSH PRIVILEGES;
```


# Application server Setup

Create "t2.micro" EC2 Instance and Open port "8080" for Python Application server

## Install Python3
```
sudo yum update -y
sudo yum install git -y
sudo yum install python3 -y
sudo yum install python3-pip -y
```
## Get the Code
```
git clone https://github.com/sapsecops/2_Tier_Python_App.git
cd 2_Tier_Python_App
```
### switch to PROD Branch

```
sudo git checkout 01-Local-setup
```
## Setup your Application Database by executing "initdb.sql" script from Application-server

Step:1 ==> install "MYSQL-Client" for communicate with MYSQL Database
```
sudo yum update -y
sudo wget https://dev.mysql.com/get/mysql80-community-release-el9-1.noarch.rpm
sudo dnf install mysql80-community-release-el9-1.noarch.rpm -y
sudo rpm --import https://repo.mysql.com/RPM-GPG-KEY-mysql-2023
sudo dnf install mysql-community-client -y
```
Step:2 ==> Execute your "init.sql" script for your Application DB setup

```
mysql -h <DB-Prvate-IP> -udbadmin -pAdmin@123 < initdb.sql
```

## Export DB Credentials as Environment Variables for DB Connection

```
export MYSQL_HOST="<your-DB-Private-IP>"
export MYSQL_USER="appuser"
export MYSQL_PASSWORD="P@55Word"
export MYSQL_DATABASE="employeedb"
```

## Install Dependencies
```
pip install -r requirements.txt
```

## Start the App
```
python3 app.py
```
### Open browser abd check your App

```
http://<AWS-Public-IP>:8080
```

# Check data saved in DB or Not

Login to your MYSQL
```
mysql -u root -p
```
Show the List of DBs
```
SHOW DATABASES;
```
Switch to your "user" DB
```
USE employeedb;
```

See the Tables under "user" DB
```
SHOW TABLES;
```
To see Data stored under "user" DB or Not
```
SELECT * FROM user;
```



