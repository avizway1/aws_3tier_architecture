

```markdown
# Setup Instructions
```

![Three-Tier Architecture](https://avinash.s3.amazonaws.com/awsdoc.png)


**make sure you use your S3 bucket to store code and copy to webservers**

## Clone the Git Repository
Download the code from the Git repository:

```bash
git clone https://github.com/avizway1/aws_3tier_architecture.git
```

## App Server Setup: Launch an ec2 instance in APP subnet of Custom VPC

### Install MySQL

On the app server, install MySQL:

```bash
sudo yum install mysql -y
```

### Configure MySQL Database

Connect to the database and perform basic configuration: Replace below info with your DB information

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

### Update Application Configuration to with DB information

Update the `**application-code/app-tier/DbConfig.js**` file with your database credentials.

### Install and Configure Node.js and PM2

Install Node.js and PM2:

```bash
curl -o- https://raw.githubusercontent.com/avizway1/aws_3tier_architecture/main/install.sh | bash
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

Create an internal load balancer and update the Nginx configuration with the internal load balancer IP. 

```text
internal-app-alb-574972862.ap-south-1.elb.amazonaws.com
```

## Web Tier Setup: Launch EC2 Instance in Web Subnets we have created in Custom VPC

### Web Tier Installation. 

Install Node.js and Nginx on the web tier:

```bash
curl -o- https://raw.githubusercontent.com/avizway1/aws_3tier_architecture/main/install.sh | bash
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

## Generate an ACM certificate and Add A record with CNAME record then you can access application with domain name securely with "https" protocol.
