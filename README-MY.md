For understanding botocore waiters (python api for checking status through AWS api), check:    
- https://boto3.amazonaws.com/v1/documentation/api/latest/guide/clients.html You can check here on what waiters are and the function of wait(). Though for more you need to look for the explanation of the create_waiter_with_client() function.  

General:   
- to see the imports in the .py files:
    - for the external python libraries like boto3, hoover over the red library and do "install package ..."
    - for the internal python libraries like constants.py in the common->python folder, rightclick folder and "Mark Folder as" source.

"checkstatus" folder:    
To understand what is going on in the get_db...status_function.py scripts check the following:  
- Using Amazon RDS Event Notification: https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_Events.html  
- For the possible Waiter names (like db_snapshot_available) and the polling actions they take on the client check https://docs.aws.amazon.com/sdk-for-ruby/v3/api/Aws/RDS/Waiters.html
- Functions of the DBSnapshotAvailable waiter https://docs.aws.amazon.com/sdk-for-ruby/v3/api/Aws/RDS/Waiters/DBSnapshotAvailable.html     
- On using AWS Lambda with RDS (through SNS): https://docs.aws.amazon.com/lambda/latest/dg/services-rds.html  
- On lambda event and context arguments (like in the lambda_get_dbinstance_status function), check https://docs.aws.amazon.com/lambda/latest/dg/python-handler.html 
- AWS Lambda function handler in python: https://docs.aws.amazon.com/lambda/latest/dg/python-handler.html  
- Lambda context object in Python: https://docs.aws.amazon.com/lambda/latest/dg/python-context.html  

On template.yaml and what SAM represents: https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-specification.html   

AWS resources info  

My RDS MySQL Free tier db:
https://eu-central-1.console.aws.amazon.com/rds/home?region=eu-central-1#database:id=database-1;is-cluster=false admin + so bukva    

**Networking**    
Availability zone:   
VPC:  name: Trapheus-VPC id: vpc-0710d425a3d07c27a 
VPC link: https://eu-central-1.console.aws.amazon.com/vpc/home?region=eu-central-1#vpcs:sort=VpcId    
Subnets: Trapheus-Subnet-1-PR-az-euc1a, Trapheus-Subnet-2-PR-az-euc1b, Trapheus-Subnet-3-PR-az-euc1c, Trapheus-Subnet-4-PU-az-euc1a, Trapheus-Subnet-5-PU-az-euc1b, Trapheus-Subnet-6-PU-az-euc1c  
Subnets private separated by comma: subnet-0364fd18d49bfa473,subnet-0a3a20efe2820f95b,subnet-0df5695cf0f8bfad2  
Subnets link: https://eu-central-1.console.aws.amazon.com/vpc/home?region=eu-central-1#subnets:sort=SubnetId  
"Auto-assign public IPv4 address = Yes" are the public subnets.       
Security  https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html  
VPC security groups:   

My frankfurt default regioun email address https://eu-central-1.console.aws.amazon.com/ses/home?region=eu-central-1#verified-senders-email:  

My S3 bucket: trapheus-s3

Pre-Requsites (additional to the README.md ones)  
You need S3 setup: 

**Parameters section (RecipientEmail)  (additional to the README.md ones):**  
1. 
2. For more on sizing check https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Subnets.html#vpc-sizing-ipv4 According to https://docs.aws.amazon.com/quickstart/latest/vpc/architecture.html the private subnets should be double the size of the public subnet and the rest should be spare+private subnet with dedicated custom network ACL. Since we are not using the later two, create the private and public ones only:  
VPC: 10.0.0.0/16  
Since 3 AZs in Frankfurt (https://aws.amazon.com/about-aws/global-infrastructure/regions_az), the subnets you need to create are:    
private in az1: Trapheus-Subnet-1-PR-az-euc1a, 10.0.0.0/19
private in az2: Trapheus-Subnet-2-PR-az-euc1b 10.0.32.0/19
private in az3: Trapheus-Subnet-3-PR-az-euc1c 10.0.64.0/19
public in az1: Trapheus-Subnet-4-PU-az-euc1a 10.0.128.0/20
public in az2: Trapheus-Subnet-5-PU-az-euc1b 10.0.144.0/20
public in az3: Trapheus-Subnet-6-PU-az-euc1c 10.0.160.0/20
NEW (trial to fix the connection problem):
private in az1: Trapheus-Subnet-1-PR-az-euw1a, 10.0.0.0/19
private in az2: Trapheus-Subnet-2-PR-az-euw1b 10.0.32.0/19
private in az3: Trapheus-Subnet-3-PR-az-euw1c 10.0.64.0/19
Private subnet ids (taken after creation) separated by comma: subnet-057fd28a4e3a41a05,subnet-026d2aad784271a82,subnet-034f33f3a6ffbacdf

3. 
4. Email can't be received in other then the following regions: https://docs.aws.amazon.com/ses/latest/DeveloperGuide/regions.html  
Receieve email problematic region: https://forums.aws.amazon.com/thread.jspa?messageID=925600&tstart=0  
?You need to define a rule set to define a receiving email address: https://eu-west-1.console.aws.amazon.com/ses/home?region=eu-west-1#rule-set:default-rule-set ?
If you have another region from where you need to verify the email address, check https://docs.aws.amazon.com/ses/latest/DeveloperGuide/verify-email-addresses-procedure.html   
Amazon SES email receiving concepts: https://docs.aws.amazon.com/ses/latest/DeveloperGuide/receiving-email-concepts.html  


**My section changes (additional to the README.md ones):**  
**Instructions**  
**Setup**  
2. sam package --template-file template.yaml --output-template-file deploy.yaml --s3-bucket trapheuss3
3. sam deploy --template-file deploy.yaml --stack-name Trapheus-stack --region eu-west-1 --capabilities CAPABILITY_NAMED_IAM --parameter-overrides vpcId=vpc-0e7aa330236a2b3ac Subnets=subnet-057fd28a4e3a41a05,subnet-026d2aad784271a82,subnet-034f33f3a6ffbacdf SenderEmail=deksa13jakim@yahoo.com RecipientEmail=deksa13jakim@yahoo.com  
OLD (before trial to fix the connection problem): sam deploy --template-file deploy.yaml --stack-name Trapheus-stack --region eu-central-1 --capabilities CAPABILITY_NAMED_IAM --parameter-overrides vpcId=vpc-0710d425a3d07c27a Subnets=subnet-0364fd18d49bfa473,subnet-0a3a20efe2820f95b,subnet-0df5695cf0f8bfad2 SenderEmail=deksa13jakim@yahoo.com RecipientEmail=deksa13jakim@yahoo.com  
Subnets are public in the default VPC but perhaps it is not going to complain. For more here: https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Subnets.html

    
**Execution**  
For creating a snapshot of my non-clustered database:    
{
    "identifier": "database-1",
    "task": "create_snapshot",
    "isCluster": false
}
NEW:
{
    "identifier": "database-2",
    "task": "create_snapshot",
    "isCluster": false
}

For restoring my non-clustered database:    
{
    "identifier": "database-1",
    "task": "db_restore",
    "isCluster": false
}

**Tear down**  
1. .. 
2. ..
3. SES (both in Frankfurt and Ireland), S3, RDS 


**What to clear (not referring to any section from the README.md:**  

**Exceptions**  
**The "Connect timeout on endpoint URL: https://rds.eu-central-1.amazonaws.com/" exception:**  
[ERROR] SnapshotCreationException: Identifier:database-1 Connect timeout on endpoint URL: "https://rds.eu-central-1.amazonaws.com/"
Traceback (most recent call last):
  File "/var/task/snapshot_function.py", line 22, in lambda_create_dbinstance_snapshot
    util.eval_snapshot_exception(error, instance_id, rds)
  File "/opt/python/utility.py", line 71, in eval_snapshot_exception
    raise custom_exceptions.SnapshotCreationException(error_message)
**Findings**  
https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect/  
https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/rds.html#RDS.Client.create_db_snapshot
https://www.google.com/search?q=Connect+timeout+on+endpoint+URL%3A+rds+amazonaws.com+create_db_snapshot  
About security groups:  
https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Overview.RDSSecurityGroups.html  
https://aws.amazon.com/premiumsupport/knowledge-center/lambda-rds-connection-timeouts/    
**Tried**
- Creating an All trafic with own sec. group as source inbound rule. Remove it if it doesn't work.
    - Didn't work probably because for the RDS the default sec group of the Trapheus VPC was active.  
    - I am trying to make the RDS work on the my-sg sec group. Deleted the database and recreated it. This means the RDS needs to be created after the stack is created. Perhaps change the sec group without deleting the DB if possible.  
- Creating a All trafic with Lambda sec. group as source inbound rule. 
	- https://stackoverflow.com/questions/37030704/allow-aws-lambda-to-access-rds-database#:~:text=You%20can%20configure%20Lambda%20to,you%20need%20it%20to%20access.  
- Permissions – AWSLambdaVPCAccessExecutionRole, the prerequisite for lambda accessing rds exists for the lambda function. https://docs.aws.amazon.com/lambda/latest/dg/services-rds-tutorial.html
- 
    
**TODO (after each work day, shutdown RDS)**
- (DONE) run python tests from intellij
- (POSTPONED until I have more time, as it will take longer to do this) run the step functions from intellij
    - setup the local stepfunction execution
        - For setting up the local state machine and step function check https://docs.aws.amazon.com/step-functions/latest/dg/sfn-local-lambda.html  
        - For setting up the RDS database locally, check 
            - a custom way (here https://stackoverflow.com/questions/54798633/how-to-connect-rds-instance-when-running-sam-locally they say that an RDS is able to be created in a docker container locally)
                - for the communication between the local state machine/step function/lambdas and the RDS instance check the same url above.
            - if custom way fails, check out localstack
            - if localstack fails, check stackery https://docs.stackery.io/docs/workflow/local-invoke-rds/    
- (DONE - he wrote them) write a test for the use case of the contributor to the issue
- write a new use case additional to the use case of the contributor to the issue
	- Maybe the use case "You can copy a snapshot from an AWS Region where S3 export isn't supported to one where it is supported, then export the copy. The S3 bucket must be in the same AWS Region as the copy."? (check https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ExportSnapshot.html)  
    - I need to make the app work on AWS first.    
		- Check above exceptions for the progress.
			- HERE Try placing everything in the Ireland region as there you have all kinds of capabilities that are not available in Frankfurt region.
				- If that doesn't work, check how to make Lambda access RDS by creating a simple one https://docs.aws.amazon.com/lambda/latest/dg/services-rds-tutorial.html and then apply missing parts to mine.
- improve the documentation  
