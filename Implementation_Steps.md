

```markdown
# Setup Instructions

## Clone the Git Repository

Download the code from the Git repository:

```bash
git clone https://github.com/aws-samples/aws-three-tier-web-architecture-workshop.git
```

## App Server Setup

### Install MySQL

On the app server, install MySQL:

```bash
sudo yum install mysql -y
```

### Configure MySQL Database

Connect to the database and perform basic configuration:

```bash
mysql -h mydb.cfpgnjehw330.ap-south-1.rds.amazonaws.com -u admin -p
```

In the MySQL shell, execute the following commands:

```sql
CREATE DATABASE webappdb;
SHOW DATABASES;
USE webappdb;

CREATE TABLE IF NOT EXISTS transactions(
  id INT NOT NULL AUTO_INCREMENT, 
  amount DECIMAL(10,2), 
  description VARCHAR(100), 
  PRIMARY KEY(id)
);

SHOW TABLES;
INSERT INTO transactions (amount, description) VALUES ('400', 'groceries');
SELECT * FROM transactions;
```

### Update Application Configuration

Update the `application-code/app-tier/DbConfig.js` file with your database credentials.

### Install and Configure Node.js and PM2

Install Node.js and PM2:

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.38.0/install.sh | bash
source ~/.bashrc

nvm install 16
nvm use 16

npm install -g pm2
```

Download application code from S3 and start the application:

```bash
cd ~/
aws s3 cp s3://3tierproject-avinash/application-code/app-tier/ app-tier --recursive

cd ~/app-tier
npm install
pm2 start index.js

pm2 list
pm2 logs
pm2 startup
pm2 save
```

Verify that the application is running by executing:

```bash
curl http://localhost:4000/health
```

It should return: `This is the health check`.

## Internal Load Balancer

Create an internal load balancer and update the Nginx configuration with the internal load balancer IP:

```text
internal-app-alb-574972862.ap-south-1.elb.amazonaws.com
```

## Launch Web-Tier EC2 Instance

### Web Tier Installation

Install Node.js and Nginx on the web tier:

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.38.0/install.sh | bash
source ~/.bashrc
nvm install 16
nvm use 16

cd ~/
aws s3 cp s3://3tierproject-avinash/application-code/web-tier/ web-tier --recursive

cd ~/web-tier
npm install
npm run build

sudo amazon-linux-extras install nginx1 -y
```

Update Nginx configuration:

```bash
cd /etc/nginx
ls

sudo rm nginx.conf
sudo aws s3 cp s3://3tierproject-avinash/application-code/nginx.conf .

sudo service nginx restart

chmod -R 755 /home/ec2-user

sudo chkconfig nginx on
```
```

This Markdown file provides a comprehensive description of each command and step involved in setting up and configuring the three-tier web architecture.
