### Steps:

### CREATE RDS INSTANCE

Standard Create

create a rds instance of MySQl type

Templates - Free Tier

Credentials:
user - admin
password - password

storage capacity - 20 GB

Disable auto storage scaling

Connectivity - Dont Connect to EC2

Network type - IPv4

VPC - default VPC

public access - Yes

create a new security group (dont use an existing one)

Availiability zone - us-east-1a

Database authentication 
Password authentication

Monitoring

Diable - Enhanced monitoring

create the database

### Create a database on the RDS Instance 

using DB weaver, connect to RDS and create a new database using the following sql command

Hostname - endpoint name
database - << keep it empty >>
username - admin
password - password

create a database with name "test" using the DBeaver UI

### Create EC2 Instance 

create an ec2 instance with amazon linux, with a key pair and open atleast port 22 and port 80 in the security group

3.1 - once the EC2 is created, then go to Actions -> Networking -> Connect to RDS and connect to the RDS instance. This will establish the required connectivity between EC2 and RDS.

4. on the ec2 instance, run the following commands:

connect to the EC2 instance using the AWS Connect option via instance connect or via ssh 

a. sudo su 

b. wget https://gaurav-public-bucket.s3.amazonaws.com/project-data/springboot-backend.jar

c. sudo yum -y install java-1.8.0

d. create a file with the following details 

## create the application.properties with the following content:
vi application.properties

press i key

(paste the below lines in the application properties file)

server.port=80
spring.datasource.url=jdbc:mysql://database-1.cyvai2w24sic.us-east-1.rds.amazonaws.com:3306/test
spring.datasource.username=admin
spring.datasource.password=password
spring.jpa.properties.hibernate.dialect = org.hibernate.dialect.MySQL5InnoDBDialect
hibernate.dialect.storage_engine=innodb
spring.jpa.database-platform: org.hibernate.dialect.MySQL5InnoDBDialect
spring.jpa.hibernate.ddl-auto=update

--- copy content end --- 

press esc key
press :wq


## run the java application using the following commands

java -jar springboot-backend.jar 

the application by default will run on port 80

If there is no error while running the application, then

## stop the application by pressing Ctrl c

and run the below command (this will prevent the application to stop if the ssh session is terminated)

nohup java -jar springboot-backend.jar & 

### create a load balancer and register the ec2 instance with the load balancer. 
Allow mapping for all AZs. 
In SG, allow all ports
while registering the ec2 instance use port 80
the ec2 intance should come to healthy state


### create a new s3 bucket and make the following changes:

a. make the bucket public by the policy, change the bucket name

{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::<bucket-name>/*"
        }
    ]
}

b. Block public access (bucket settings) - set it to OFF

c. download the following file - https://gaurav-public-bucket.s3.amazonaws.com/project-data/angular-frontend.zip 

unzip the same and modify the main.js file and replace the load balancer to your load balancer 

File - main.js

search for /api/v1/enployees in the file and replace the "<<SPECIFY ALB DNS Name>>" with the new load balancer hostname

this.baseURL = "http://<<SPECIFY ALB DNS Name>>/api/v1/employees";

after making the changes, the above should like this:

this.baseURL = "http://project-alb.something.aws.com/api/v1/employees";

d. enable the Static site hosting option  - set the index to index.html file 


### Below steps not required. 

mysql -u admin -h <db endpoint name>  -p
password: password

SHOW DATABASES;

// the below settings are not required.
c. in bucket permission, edit Cross-origin resource sharing (CORS) and add the following statements

[
    {
        "AllowedHeaders": [],
        "AllowedMethods": [
            "GET",
            "HEAD",
            "POST",
            "DELETE"
        ],
        "AllowedOrigins": [
            "*"
        ],
        "ExposeHeaders": []
    }
