---
GeekDocCollapseSection: true
weight: 3
---

# AWS Infrastructure Doc
{{< toc >}}

![should be a pic here](infrastructure/infrastructureDiagram.png)

# Compute

---

## EC2

{{< expand "Names of Current EC2 instances" >}}
- Web Server
- App Server
- Bastion Host
{{< /expand >}}
### AMI 

**Author:** Arthur Hui
AMIs are snapshot of the instances at that very moment in time. They contain all of the files that exist in the instance at that very moment. All operating system updates, installed libraries, and stuff will be there when it is recreated and turned into an EC2 instance. 

{{< expand "AMIs" >}}
- App Server AMI: Has docker, MySQL, and python installed on it.
- Web Server AMI: Has MySQL, python, Django, and apache installed. There are  
    also html and python files to host the actual website.
{{< /expand >}}

### Lambda Function - 
**Author:** Edgar Ramirez

- The AWS Lambda service creates what's called Lambda functions which help in calling code after a certain event has occured in the application. The code can also call other AWS services, such as a load balancer, SQS or SNS, EC2, or S3 Bucket.
    - Our Lambda function gets triggers when AWS receives an object in the S3 bucket (an object storage service), in this case when admin creates a new event through the webpage.
        - When the event gets creates the information entered by the user gets saved into a JSON file and saved onto the S3 Bucket.

        ![](infrastructure/InfrastructureDiagram2.png)

        - The Lambda function, now triggered, will run code (Python) to call AWS SQS (Simple Queue Service, sends messages)
            - It will query a list of emails (from RDS, AWS Relational Database Service) of the students that will be participating in the assignment.
            - Will email the list of students their submission tokens for the assignment.

### Relational Database Service - 
**Author:** Arthur Hui

Amazon Relational Database Service is how we are storing information that the front end and back end need to access. The database use MySQL to handle queries, updates, deletions, and creation of data inside the it. 

- Here is the Entity Relation Diagram. Shows what fields that each entity has associated to it and how they associated with one another.

 

![](infrastructure/RDS-ERD.png)

- Steps to connect to the RDS from the instance
    1. Find the user name of the RDS you want to connect to
    2. Find the password for the RDS you want to connect to
    3. Get the endpoint of the RDS you want to connect to
    4. Use the command below to access the database. 
        - Replace the #### with the RDS endpoint and $$$$ with the user name of the RDS

    ```bash
    mysql -h ####### -P 3306 -u $$$$$$ -p
    ```

# Networking

---

## VPC

{{< expand "VPC configuration" >}}
- CIDR-10.0.0.0/16
- Virtual Private Cloud Will encapsulate the following resources
    - Public Subnet
    - Private Subnet
    - Internet Gateway
    - Nat Gateway
    - Public Route table: Associated with IG
    - Private Route table: Associated with Nat Gateway
{{< /expand >}}

## Subnet Configuration 
**Author:** Jose Cahue 

{{< expand "Public Subnet" >}}
- CIDR- 10.0.0.0/24
- Public to the public internet
- Will encasulate the following resources
    - Bastion Host- Manages access to Web server
    - Web Server- Responsible for hosting website
    - Nat Instance-Responsible for forwarding traffic to App server.
{{< /expand >}}

{{< expand "Private Subnet" >}}
- CIDR- 10.0.0.0/24
- Will encapsulate the following resources
    - App Server-Responsible for back end logic processing
    - Docker deamon-Service used to decouple diffrent process functionality
{{< /expand >}}

## Internet Gateway
**Author**: Jose Cahue

{{< expand "Role" >}}

-Responsible for public internet access into the VPC
{{< /expand >}}

## NAT Gateway
**Author:** Jose Cahue

{{< expand "Role" >}}

-Responsible for managing communication internal VPC data traffice to our private subnet resources
{{< /expand >}}

## Load Balancer 
**Author:** Edgar Ramirez

- The AWS Load Balancer spreads out application traffic to various items such as EC2 instances. (EC2 instances, containters, IP Addresses, Lambda functions, and etc.)
    - Single or Multiple Availability Zones
        - The application can choose to reroute the overflow of traffic to just one or multiple availability zones or a section of the globe that provides resources, computing or storage or both for the application.
        - This project has two load balancers
            - Both use multiple availability zones
                - US-East-1a (Virginia)
                - US-East-1b (Virginia)

# Security Group Communication 
---
**Author:** Arthur Hui

The security groups allows for network communication between the instances, RDS, and the internet. Each security group should be explicitly name to avoid confusion. Here are the security groups that are being used in our infrastructure.

{{< expand "Security Groups" >}}
- Bastion Host SG
    - Description: allow ssh into bastion host
    - Inbound rule: ssh on port 22 with source 0.0.0.0/0
    - Outbound rule: All traffic on all ports with 0.0.0.0/0
- Web Server SG
    - Description: allow ssh from bastion host into Web Server and people to view the sight
    - Inbound rules
        - Type ssh on port 22 with the source being the Bastion Host SG
        - Type HTTP on port 80 with source being 0.0.0.0/0 and ::/0
        - Type HTTPs on port 443 with source being 0.0.0.0/0 and ::/0
        - Type custom tcp on port  8000 with source being 0.0.0.0/0
    - Outbound rule: All traffic on all ports with 0.0.0.0/0
- App Server SG
    - Description: allow ssh from bastion host into App Server
    - Inbound Rules
        - Type ssh on port 22 with the source being the Bastion Host SG
        - Type MYSQL/Aurora on port 3306 with source being 0.0.0.0/0
    - Outbound rule: All traffic on all ports with 0.0.0.0/0
- RDS_SG
    - Description: allow RDS to be accessed by app and web server
    - Inbound rule: MYSQL/Aurora on port 3306 with source being 10.0.0.0/16
    - Outbound rule: All traffic on all ports with 0.0.0.0/0
{{< /expand >}}

# SQS 
**Author:** Zhili Wang

Amazon Simple queue service (SQS) is a fully managed message queue service for communicating between distributed systems and microservices at any scale. SQS makes it simple to decouple the application component so it can run and fail independently. This allows users to build an application that is fault tolerant and easy to scale.

---

{{< expand "Queues" >}}

We have two standard SQS Queues, both are trigger by the Lamda function base on its invocation condition.
{{< /expand >}}
{{< expand "Queue type" >}}

We used the standard Queue for simplicity because the order of the message is not important.
{{< /expand >}}
{{< expand "Queue Details" >}}

Our queue accept data size between 1 to 256 kb, and the message retention
period is 4 days. There is a 10-second delay on delivery and receive wait
time.
{{< /expand >}}
{{< expand "Queue Usage" >}}
{{< /expand >}}

# SES  
**Author:** Elijah Clark

---

Simple Email Service is used to send out emails via AWS. Allows for emails to be sent to verified emails from AWS console. And if SES setting for sandbox mode is set to off then one may send out emails to unverified emails..

{{< expand "Prerequisites" >}}
- DOMAIN

    A domain must be set up either with AWS route 53. Or created using a 3rd party Domain service.
    Using route 53 is well documented, thus follow documentation if going with AWS made domain.

- EMAIL

    An email can be verified inside of SES console under Identity management, and simply clicking 
    Verify a new email address one may enter an email that they want to send emails from or to.

- aws_access_id & aws_secret_access_key

    AWS has this well documented, must have admin generate and give these values.

- Toggle Sandbox mode off

    By going to SES console, under the sending statistics tab one may click edit your account details 
    to enable production access, fill in the rest of the needed information and submit for review. Wait 
    for verification to finish. Once done with the review one can finally send emails via code to unverified emails.
{{< /expand >}}

{{< expand "Components" >}}
- Region

    Using AWS region format, example: "us-east-1". Is used to set region currently being used 
    for by AWS service.

- Domain

    Domain that was from prerequisites is used as input during use of constructor or simply a call to
    the domain setter prior to use of method to send emails via domain. 

- Recipient

    Email that was from prerequisites is used as input during use of constructor or simply a call to
        the recipient setter prior to use of method to send emails via domain
        Once outside of sandbox mode
        SES can send emails out to unverified emails, thus sending emails to any address that is valid.

- Subject

    Simply a string  for setting subject of email

- Body_text

    Simply a string input for body of text for body of email.

- Body_html

    Must have correct formatting and be correct HTML otherwise it will not work. Recommend testing html
    separate before  passing it in as a string. All html must be passed as a string.
{{< /expand >}}

{{< expand "USE" >}}
Import [SES.py](http://ses.py) into another PY file.

Once imported make a call to constructor and use setters from [SES.py](http://ses.py) to set the values
for domain, region, recipient, html, and subject. Once all needed information is there to 
make up to totality of the email. simply call method send email with domain which will use provided 
information to send email out.
{{< /expand >}}
# S3
**Author:** Zhili Wang

---

Amazon Simple Storage Service (S3) is a cloud storage that offers scalability, data availability, security, and performance. It has a simple web service interface that can be used to store and retrieve any amount of data at any time from anywhere. The S3 bucket is also cost-effective and very reliable in terms of preventing data loss.

{{< expand "Property" >}}

The bucket region is us-east-1, and the event notification destination is our lambda function (Submitter_ Trigger).
{{< /expand >}}
{{< expand "Permission" >}}

We use Identity and Access Management (IAM) and bucket policy to control the permission of the S3 bucket
{{< /expand >}}
{{< expand "Usage" >}}

Our s3 bucket can send a notification to the lambda function and retrieve user input (JSON File) from it. We can also upload the item to the bucket using Amazon SDK
{{< /expand >}}

{{< expand "Management" >}}

We don't have any rules for bucket management at the moment. We thought about making a lifecycle rule so it can transition objects to archive or delete the infrequent access data when they are no longer need it, but it's not necessary with the current build of the project.
{{< /expand >}}
{{< expand "Storage" >}}

Back end will explain how the objects are being stored in the S3
{{< /expand >}}
# IAM 
**Author:** Arthur Hui

---

Identity and access management is useful for allowing multiple people access to the same AWS account. Each new IAM user must have certain polices attached to the user for accessing AWS resources. The owner can also put multiple users in a group and attach polices to said group to make IAM management easier.

{{< expand "IAM naming examples" >}}
{{< expand "First Names (using common names as an example)" >}}
- Cameron
- Kim
- Derek
- Bob
{{< /expand >}}
{{< expand "A specific user names" >}}
- Comp350ProjectLeader
- FrontEnd213
- BackEnd456
- Infrastructure805
{{< /expand >}}
{{< expand "Group naming" >}}
    - front-end-team
    - back-end-team
    - infrastructure-team
{{< /expand >}}
{{< /expand >}}
Roles are useful to allow certain resources to interact with each other like S3 to SQS and SQS to EC2 as an example. It also makes sure that these resources can only access those specific resources.

{{< expand "Existing Roles" >}}
- Submitter_lambda_role

    allows the lambda function to get a objects from S3 and put a message on the SQS

- ReadS3Bucket

    allows reading from the S3 bucket, SQS, and executing the SQS

- rds-monitoring-role
    - does what it says, monitors the relation database service
- backend_queueaccess_role
    - allows the back app server to receive, delete, and get attribute from the SQS
{{< /expand >}}
# Cloud Formation 
**Author:** Daniel Thai

---

A service that helps users model and set up AWS resources for them to focus on their applications that run in AWS. Users could create a template that'll describe all the AWS resources that they want to use and Cloud Formation will take care of provisioning and configuring the resources for the users. 

- Creating the template
    - Set up and log into your AWS account

        Sign in to the Console

    - Define Access Controls

        Set up the access with AWS Identity

    - Deploy your first collection of resources

        Create and deploy your first Cloud Formation template

- YAML file template reference

    Usage:  
        Submitter Networking Enviroment and Resources

    This template will:  
    * Create a VPC with:  
        * 2 Public Subnets

- YAML file template

```
    Resources:
      SubmitterVPC:
        Type: 'AWS::EC2::VPC'
        Properties:
          CidrBlock: 10.0.0.0/16
        Metadata:
```