Throughout the lab, various parts of code will be replaced with user specific information.
These codes will be denoted with some variation of REPLACE_ME_VARIABLE
DNSname=  http://mysfits-nlb-72c8cce5897e93c4.elb.us-east-1.amazonaws.com

VPC Link = a149ce
REPLACE_ME_BUCKET_NAME = myth-misfit-aws-proj

REPLACE_ME_YOUR_REGION = us-east-1

REPLACE_ME_ACCOUNT_ID = 192893987974

REPLACE_ME_WITH_DOCKER_IMAGE_TAG = 192893987974.dkr.ecr.us-east-1.amazonaws.com/mythicalmysfits/service:latest

CognitoUserPoolId REPLACE_ME = us-east-1_Senh3IDAp

"CognitoUserPoolClientId": "4qfefpd5rblv6g00afgbdmqie3"
REPLACE_ME_NLB_ARN =  arn:aws:elasticloadbalancing:us-east-1:192893987974:loadbalancer/net/mysfits-nlb/72c8cce5897e93c4

REPLACE_ME_WITH_API_ID = yki4m8tzlh

Var MyfitsApiEndpoint = yki4m8tzlh.execute-api.us-east-1.amazonaws.com/prod/

REPLACE_ME_SECURITY_GROUP_ID = sg-0ac4ef7123b00bd4c

REPLACE_ME_VPC_ID = vpc-0f6be6ccfa87ba5eb
REPLACE_ME_PUBLIC_SUBNET_ONE = subnet-076c128098bbb980f
REPLACE_ME_ECS_TASK_ROLE_ARN =  arn:aws:iam::192893987974:role/MythicalMysfitsCoreStack-ECSTaskRole-CJGE4K7PS1P7 
REPLACE_ME_PRIVATE_SUBNET_TWO = subnet-02b3fb885f15456be

REPLACE_ME_PUBLIC_SUBNET_TWO = subnet-06ead27cc0aef3d7a

REPLACE_ME_CODEBUILD_ROLE_ARN = arn:aws:iam::192893987974:role/MythicalMysfitsServiceCodeBuildServiceRole

REPLACE_ME_CODEPIPELINE_ROLE_ARN= arn:aws:iam::192893987974:role/MythicalMysfitsServiceCodePipelineServiceRole

REPLACE_ME_ECS_SERVICE_ROLE_ARN = arn:aws:iam::192893987974:role/MythicalMysfitsCoreStack-EcsServiceRole-YYNGD5Y3FMQK

REPLACE_ME_PRIVATE_SUBNET_ONE = subnet-00ec20a920500a183

TargetGroupArn": "arn:aws:elasticloadbalancing:us-east-1:192893987974:targetgroup/MythicalMysfits-TargetGroup/9b43df52f06b4c7f"

REPLACE_ME_QUESTIONS_API_ENDPOINT = https://638y66099b.execute-api.us-east-1.amazonaws.com/prod

REPLACE_ME_QUESTIONS_REST_API_ID = 638y66099b

MysfitsRecommendationsApi = https://eu1xcta4ah.execute-api.us-east-1.amazonaws.com/prod

REPLACE_ME_ARTIFACTS_BUCKET_NAME = mythartifactsbucket

Issues that arose
swagger.json 
invalid base64 
[Fixed by combing for syntax issues, then implementing fileb:// when directing to the file path]
==========================================================================================================================================================================

Website photos would not populate (Fixed by changing variables across certain files to point to the ELB rather than the api endpoint. MysfitApiEndpoint was misleading with what variable to implement)

Initial mistake
var mysfitsapiendpoint = yki4m8tzlh.execute-api.us-east-1.amazonaws.com/prod/mysfits

Proper Syntax 
Var mysfitsapiendpoint = mysfits-nlb-72c8cce5897e93c4.elb.us-east-1.amazonaws.com
In order for the website contents to properly populate, It was necessary to point to the ELB rather than the apigateway endpoint. 
==========================================================================================================================================================================

Skills learned
Architecting a website that combines serverless applications, dynamodb, apigateways, load balancing, and  cloud storage.
Services used
Apigateway, S3, DynamoDB, CloudFront, VPC, EC2, Cloud9, Kinesis, Lambda, X ray, Sagemaker
Issues that arose

Invalid base64 swagger.json

[Fixed by combing for syntax issues, then implementing fileb:// when directing to the file path] This didn’t work on my first attempt. 
Website photos would not populate (Fixed by changing variables across certain files to point to the ELB rather than the api endpoint. MysfitApiEndpoint was misleading with what variable to implement)

Module 1
Creating your Mythical Mysifts IDE

Launch a Cloud9 Instance 


Cloning the Mythical Mysfits Workshop Repository
Run these commands
git clone -b python https://github.com/aws-samples/aws-modern-application-workshop.git
cd aws-modern-application-workshop

Creating a Static Website in Amazon S3
[Keep in mind, Cloud front is the better option for website hosting but for the purposes of this sample project we will be using S3]

aws s3 mb s3://myth-misfit-aws-proj [Bucket Creation]
//This bucket will be referenced a lot through the CLi.
aws s3 website s3://myth-misfit-aws-proj --index-document index.html [ Static Website Configuration ]

Update the S3 Bucket Policy

All buckets created in Amazon S3 are fully private by default. In order to be used as a public website, we need to create an S3 Bucket Policy that indicates objects stored within this new bucket may be publicly accessed by anyone. Bucket policies are represented as JSON documents that define the S3 Actions (S3 API calls) that are allowed (or not not allowed) to be performed by different Principals (in our case the public, or anyone).

Update Bucket Policy located in Module 1 Folder 
file://~/environment/aws-modern-application-workshop/module-1/aws-cli/website-bucket-policy.json
[UPDATED CODE]
{
  "Id": "MyPolicy",
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicReadForGetBucketObjects",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::myth-misfit-aws-proj/*"
    }
  ]
}


Run this command in the CLI to update bucket policy

aws s3api put-bucket-policy --bucket myth-misfit-aws-proj --policy file://~/environment/aws-modern-application-workshop/module-1/aws-cli/website-bucket-policy.json


Publish the Website Content to S3
aws s3 website s3://myth-misfit-aws-proj --index-document index.html
aws s3 cp ~/environment/aws-modern-application-workshop/module-1/web/index.html s3://myth-misfit-aws-proj/index.html
http://REPLACE_ME_BUCKET_NAME.s3-website-REPLACE_ME_YOUR_REGION.amazonaws.com
http://myth-misfit-aws-proj.s3-website-us-east-1.amazonaws.com

MODULE 2
Creating core infrastructure using cloud front
aws cloudformation create-stack --stack-name MythicalMysfitsCoreStack --capabilities CAPABILITY_NAMED_IAM --template-body file://~/environment/aws-modern-application-workshop/module-2/cfn/core.yml  
This Stack will provide us with
"An Amazon VPC - a network environment that contains four subnets (two public and two private) in the 10.0.0.0/16 private IP space, as well as all the needed Route Table configurations. The subnets for this network are created in separate AWS Availability Zones (AZ) to enable high availability across multiple physical facilities in an AWS Region. Learn more about how AZs can help you achieve High Availability here.
Two NAT Gateways (one for each public subnet, also spanning multiple AZs) - allow the containers we will eventually deploy into our private subnets to communicate out to the Internet to download necessary packages, etc.
A DynamoDB VPC Endpoint - our micro service backend will eventually integrate with Amazon DynamoDB for persistence (as part of module 3).
A Security Group - Allows your Docker containers to receive traffic on port 8080 from the Internet through the Network Load Balancer.
IAM Roles - Identity and Access Management Roles are created. These will be used throughout the workshop to give AWS services or resources you create access to other AWS services like DynamoDB, S3, and more."

To check on the creation of the stack, use this command.
aws cloudformation describe-stacks --stack-name MythicalMysfitsCoreStack 

After the command shows "CREATE_COMPLETE"

Run 
aws cloudformation describe-stacks --stack-name MythicalMysfitsCoreStack > ~/environment/cloudformation-core-output.json

In order to commit the output from the previous command into a file in the IDE. 
This file will be referenced multiple times as many variations of REPLACE_ME are located in here

Module 2a: Deploying a Service with AWS Fargate
Creating a Flask Service Container
Building A Docker Image

cd ~/environment/aws-modern-application-workshop/module-2/app
docker build . -t 192893987974.dkr.ecr.us-east-1.amazonaws.com/mythicalmysfits/service:latest
You will see Docker download and install all of the necessary dependency packages that our application needs, and output the tag for the built image. Copy the image tag for later reference. Below the example tag shown is: 111111111111.dkr.ecr.us-east-1.amazonaws.com/mythicalmysfits/service:latest

Successfully built 8bxxxxxxxxab
Successfully tagged 111111111111.dkr.ecr.us-east-1.amazonaws.com/mythicalmysfits/service:latest
docker run -p 8080:8080 192893987974.dkr.ecr.us-east-1.amazonaws.com/mythicalmysfits/service:latest

Testing the Service Locally

run 111111111111.dkr.ecr.us-east-1.amazonaws.com/mythicalmysfits/service:latest/mysfits in the browser within cloud9 to test it

If successful you will see a response from the service that returns the JSON document stored at /aws-modern-application-workshop/module-2/app/service/mysfits-response.json 
\\\\Find the response and post it here, also edit the above
When done testing, using CTRL C to quit.
Pushing the Docker Image to Amazon ECR
With a successful test of our service locally, we're ready to create a container image repository in Amazon Elastic Container Registry (Amazon ECR) and push our image into it.
aws ecr create-repository --repository-name mythicalmysfits/service

The response to this command will contain additional metadata about the created repository. In order to push container images into our new repository, we will need to obtain authentication credentials for our Docker client to the repository. Run the following command, which will return a login command to retrieve credentials for our Docker client and then automatically execute it (include the full command including the $ below). 'Login Succeeded' will be reported if the command is successful.
$(aws ecr get-login --no-include-email)
Next, push the image you created to the ECR repository using the copied tag from above. Using this command, docker will push your image and all the images it depends on to Amazon ECR:
docker push REPLACE_ME_WITH_DOCKER_IMAGE_TAG
Run the following command to see your newly pushed docker image stored inside the ECR repository:
aws ecr describe-images --repository-name mythicalmysfits/service
Configuring the Service Prerequisites in Amazon ECS
Create an ECS Cluster
Now, we have an image available in ECR that we can deploy to a service hosted on Amazon ECS using AWS Fargate. The same service you tested locally via the terminal in Cloud9 as part of the last module will now be deployed in the cloud and publicly available behind a Network Load Balancer.
First, we will create a Cluster in the Amazon Elastic Container Service (ECS). This represents the cluster of “servers” that your service containers will be deployed to. Servers is in "quotations" because you will be using AWS Fargate. Fargate allows you to specify that your containers be deployed to a cluster without having to actually provision or manage any servers yourself.
To create a new cluster in ECS, run the following command:
aws ecs create-cluster --cluster-name MythicalMysfits-Cluster
Create an AWS CloudWatch Logs Group
Next, we will create a new log group in AWS CloudWatch Logs. AWS CloudWatch Logs is a service for log collection and analysis. The logs that your container generates will automatically be pushed to AWS CloudWatch logs as part of this specific group. This is especially important when using AWS Fargate since you will not have access to the server infrastructure where your containers are running.
To create the new log group in CloudWatch logs, run the following command:
aws logs create-log-group --log-group-name mythicalmysfits-logs
Register an ECS Task Definition
Now that we have a cluster created and a log group defined for where our container logs will be pushed to, we're ready to register an ECS task definition. A task in ECS is a set of container images that should be scheduled together. A task definition declares that set of containers and the resources and configuration those containers require. You will use the AWS CLI to create a new task definition for how your new container image should be scheduled to the ECS cluster we just created.
A JSON file has been provided that will serve as the input to the CLI command.
Open ~/environment/aws-modern-application-workshop/module-2/aws-cli/task-definition.json in the IDE.
Replace the indicated values with the appropriate ones from your created resources.
These values will be pulled from the CloudFormation response you copied earlier as well as the docker image tag that you pushed earlier to ECR, eg: REPLACE_ME_ACCOUNT_ID.dkr.ecr.us-east-1.amazonaws.com/mythicalmysfits/service:latest
Once you have replaced the values in task-defintion.json and saved it. Execute the following command to register a new task definition in ECS:
aws ecs register-task-definition --cli-input-json file://~/environment/aws-modern-application-workshop/module-2/aws-cli/task-definition.json
Enabling a Load Balanced Fargate Service
Create a Network Load Balancer
With a new task definition registered, we're ready to provision the infrastructure needed in our service stack. Rather than directly expose our service to the Internet, we will provision a Network Load Balancer (NLB) to sit in front of our service tier. This would enable our frontend website code to communicate with a single DNS name while our backend service would be free to elastically scale in-and-out, in multiple Availability Zones, based on demand or if failures occur and new containers need to be provisioned.
To provision a new NLB, execute the following CLI command in the Cloud9 terminal (retrieve the subnetIds from the CloudFormation output you saved):
aws elbv2 create-load-balancer --name mysfits-nlb --scheme internet-facing --type network --subnets subnet-076c128098bbb980f subnet-06ead27cc0aef3d7a  > ~/environment/nlb-output.json
When this command has successfully completed, a new file will be created in your IDE called nlb-output.json. You will be using the DNSName, VpcId, and LoadBalancerArn in later steps.
Create a Load Balancer Target Group
Next, use the CLI to create an NLB target group. A target group allows AWS resources to register themselves as targets for requests that the load balancer receives to forward. Our service containers will automatically register to this target so that they can receive traffic from the NLB when they are provisioned. This command includes one value that will need to be replaced, your vpc-id which can be found as a value within the earlier saved MythicalMysfitsCoreStack output returned by CloudFormation.

aws elbv2 create-target-group --name MythicalMysfits-TargetGroup --port 8080 --protocol TCP --target-type ip --vpc-id vpc-0f6be6ccfa87ba5eb --health-check-interval-seconds 10 --health-check-path / --health-check-protocol HTTP --healthy-threshold-count 3 --unhealthy-threshold-count 3 > ~/environment/target-group-output.json
When this command completes, its output will be saved to target-group-output.json in your IDE. You will reference the TargetGroupArn value in a subsequent step.
Create a Load Balancer Listener
Next, use the CLI to create a load balancer listener for the NLB. This informs that load balancer that for requests received on a specific port, they should be forwarded to targets that have registered to the above target group. Be sure to replace the two indicated values with the appropriate ARN from the TargetGroup and the NLB that you saved from the previous steps:
aws elbv2 create-listener --default-actions TargetGroupArn= arn:aws:elasticloadbalancing:us-east-1:192893987974:targetgroup/MythicalMysfits-TargetGroup/9b43df52f06b4c7f ,Type=forward --load-balancer-arn arn:aws:elasticloadbalancing:us-east-1:192893987974:loadbalancer/net/mysfits-nlb/72c8cce5897e93c4 --port 80 --protocol TCP
Creating a Service with Fargate
Creating a Service Linked Role for ECS
If you have already used ECS in the past you can skip over this step and move on to the next step. If you have never used ECS before, we need to create a service linked role in IAM that grants the ECS service itself permissions to make ECS API requests within your account. This is required because when you create a service in ECS, the service will call APIs within your account to perform actions like pulling docker images, creating new tasks, etc.
Without creating this role, the ECS service would not be granted permissions to perform the actions required. To create the role, execute the following command in the terminal:
aws iam create-service-linked-role --aws-service-name ecs.amazonaws.com

If the above returns an error about the role existing already, you can ignore it, as it would indicate the role has automatically been created in your account in the past. Create the Service
With the NLB created and configured, and the ECS service granted appropriate permissions, we're ready to create the actual ECS service where our containers will run and register themselves to the load balancer to receive traffic. We have included a JSON file for the CLI input that is located at: ~/environment/aws-modern-application-workshop/module-2/aws-cli/service-definition.json. This file includes all of the configuration details for the service to be created, including indicating that this service should be launched with AWS Fargate - which means that you do not have to provision any servers within the targeted cluster. The containers that are scheduled as part of the task used in this service will run on top of a cluster that is fully managed by AWS.
Open ~/environment/aws-modern-application-workshop/module-2/aws-cli/service-definition.json in the IDE and replace the indicated values of REPLACE_ME. Save it, then run the following command to create the service:

aws ecs create-service --cli-input-json file://~/environment/aws-modern-application-workshop/module-2/aws-cli/service-definition.json

After your service is created, ECS will provision a new task that's running the container you've pushed to ECR, and register it to the created NLB.
Test the Service
Copy the DNS name you saved when creating the NLB and send a request to it using the preview browser in Cloud9 (or by simply any web browser, since this time our service is available on the Internet). Try sending a request to the mysfits resource:
http://mysfits-nlb-123456789-abc123456.elb.us-east-1.amazonaws.com/mysfits
A response showing the same JSON response we received earlier when testing the docker container locally in Cloud9 means your Flask API is up and running on AWS Fargate.
Note: This Network Load Balancer only supports HTTP (http://) requests since no SSL/TLS certificates are installed on it. For this tutorial, be sure to submit requests using http:// only, https:// requests will not work properly.
Update Mythical Mysfits to Call the NLB
[ THIS IS WHEN THE FIRST ISSUE REGARDING 
Replace the API Endpoint
Next, we need to integrate our website with your new API backend instead of using the hard coded data that we previously uploaded to S3. You'll need to update the following file to use the same NLB URL for API calls (do not inlcude the /mysfits path): /module-2/web/index.html
Open the file in Cloud9 and replace the highlighted area below between the quotes with the NLB URL:
 
After pasting, the line should look similar to below:
 
Upload to S3
To upload this file to your S3 hosted website, use the bucket name again that was created during Module 1, and run the following command:
aws s3 cp ~/environment/aws-modern-application-workshop/module-2/web/index.html s3://INSERT-YOUR-BUCKET-NAME/index.html
Open your website using the same URL used at the end of Module 1 in order to see your new Mythical Mysfits website, which is retrieving JSON data from your Flask API running within a docker container deployed to AWS Fargate!
Module 2b: Automating Deployments using AWS Code Services
 
Creating the CI/CD Pipeline
Create a S3 Bucket for Pipeline Artifacts
Now that you have a service up and running, you may think of code changes that you'd like to make to your Flask service. It would be a bottleneck for your development speed if you had to go through all of the same steps above every time you wanted to deploy a new feature to your service. That's where Continuous Integration and Continuous Delivery or CI/CD come in!
In this module, you will create a fully managed CI/CD stack that will automatically deliver all of the code changes that you make to your code base to the service you created during the last module.
First, we need to create another S3 bucket that will be used to store the temporary artifacts that are created in the middle of our CI/CD pipeline executions. Choose a new bucket name for these artifacts and create one using the following CLI command:
aws s3 mb s3://mythmisfitartifactsbucket
Next, this bucket needs a bucket policy to define permissions for the data stored within it. But unlike our website bucket that allowed access to anyone, only our CI/CD pipeline should have access to this bucket. We have provided the JSON file needed for this policy at ~/environment/aws-modern-application-workshop/module-2/aws-cli/artifacts-bucket-policy.json. Open this file, and inside you will need to replace several strings to include the ARNs that were created as part of the MythicalMysfitsCoreStack earlier, as well as your newly chosen bucket name for your CI/CD artifacts.
Once you've modified and saved this file, execute the following command to grant access to this bucket to your CI/CD pipeline:
aws s3api put-bucket-policy --bucket mythartifactsbucket --policy file://~/environment/aws-modern-application-workshop/module-2/aws-cli/artifacts-bucket-policy.json
Create a CodeCommit Repository
You'll need a place to push and store your code in. Create an AWS CodeCommit Repository using the CLI for this purpose:
aws codecommit create-repository --repository-name MythicalMysfitsService-Repository
Create a CodeBuild Project
With a repository to store our code in, and an S3 bucket that will be used for our CI/CD artifacts, lets add to the CI/CD stack with a way for a service build to occur. This will be accomplished by creating an AWS CodeBuild Project. Any time a build execution is triggered, AWS CodeBuild will automatically provision a build server to our configuration and execute the steps required to build our docker image and push a new version of it to the ECR repository we created (and then spin the server down when the build is completed). The steps for our build (which package our Python code and build/push the Docker container) are included in the ~/environment/aws-modern-application-workshop/module-2/app/buildspec.yml file. The buildspec.yml file is what you create to instruct CodeBuild what steps are required for a build execution within a CodeBuild project.
To create the CodeBuild project, another CLI input file is required to be updated with parameters specific to your resources. It is located at ~/environment/aws-modern-application-workshop/module-2/aws-cli/code-build-project.json. Similarly replace the values within this file as you have done before from the MythicalMysfitsCoreStackOutput. Once saved, execute the following with the CLI to create the project:
aws codebuild create-project --cli-input-json file://~/environment/aws-modern-application-workshop/module-2/aws-cli/code-build-project.json
Create a CodePipeline Pipeline
Finally, we need a way to continuously integrate our CodeCommit repository with our CodeBuild project so that builds will automatically occur whenever a code change is pushed to the repository. Then, we need a way to continuously deliver those newly built artifacts to our service in ECS. AWS CodePipeline is the service that glues these actions together in a pipeline you will create next.
Your pipeline in CodePipeline will do just what I described above. Anytime a code change is pushed into your CodeCommit repository, CodePipeline will deliver the latest code to your AWS CodeBuild project so that a build will occur. When successfully built by CodeBuild, CodePipeline will perform a deployment to ECS using the latest container image that the CodeBuild execution pushed into ECR.
All of these steps are defined in a JSON file provided that you will use as the input into the AWS CLI to create the pipeline. This file is located at ~/environment/aws-modern-application-workshop/module-2/aws-cli/code-pipeline.json, open it and replace the required attributes within, and save the file.
Once saved, create a pipeline in CodePipeline with the following command:
aws codepipeline create-pipeline --cli-input-json file://~/environment/aws-modern-application-workshop/module-2/aws-cli/code-pipeline.json
Enable Automated Access to ECR Image Repository
We have one final step before our CI/CD pipeline can execute end-to-end successfully. With a CI/CD pipeline in place, you won't be manually pushing container images into ECR anymore. CodeBuild will be pushing new images now. We need to give CodeBuild permission to perform actions on your image repository with an ECR repository policy*. The policy document needs to be updated with the specific ARN for the CodeBuild role created by the MythicalMysfitsCoreStack, and the policy document is located at ~/environment/aws-modern-application-workshop/module-2/aws-cli/ecr-policy.json. Update and save this file and then run the following command to create the policy:
aws ecr set-repository-policy --repository-name mythicalmysfits/service --policy-text file://~/environment/aws-modern-application-workshop/module-2/aws-cli/ecr-policy.json

When that has been created successfully, you have a working end-to-end CI/CD pipeline to deliver code changes automatically to your service in ECS.
Test the CI/CD Pipeline
Using Git with AWS CodeCommit
To test out the new pipeline, we need to configure git within your Cloud9 IDE and integrate it with your CodeCommit repository.
AWS CodeCommit provides a credential helper for git that we will use to make integration easy. Run the following commands in sequence the terminal to configure git to be used with AWS CodeCommit (neither will report any response if successful):
git config --global user.name "REPLACE_ME_WITH_YOUR_NAME"
git config --global user.email REPLACE_ME_WITH_YOUR_EMAIL@example.com
git config --global credential.helper '!aws codecommit credential-helper $@'
git config --global credential.UseHttpPath true
Next change directories in your IDE to the environment directory using the terminal:
cd ~/environment/
Now, we are ready to clone our repository using the following terminal command:
git clone https://git-codecommit.REPLACE_REGION.amazonaws.com/v1/repos/MythicalMysfitsService-Repository
This will tell us that our repository is empty! Let's fix that by copying the application files into our repository directory using the following command:
cp -r ~/environment/aws-modern-application-workshop/module-2/app/* ~/environment/MythicalMysfitsService-Repository/
Pushing a Code Change
Now the completed service code that we used to create our Fargate service in Module 2 is stored in the local repository that we just cloned from AWS CodeCommit. Let's make a change to the Flask service before committing our changes, to demonstrate that the CI/CD pipeline we've created is working. In Cloud9, open the file stored at ~/environment/MythicalMysfitsService-Repository/service/mysfits-response.json and change the age of one of the mysfits to another value and save the file.
After saving the file, change directories to the new repository directory:
cd ~/environment/MythicalMysfitsService-Repository/
Then, run the following git commands to push in your code changes.
git add .
git commit -m "I changed the age of one of the mysfits."
git push
After the change is pushed into the repository, you can open the CodePipeline service in the AWS Console to view your changes as they progress through the CI/CD pipeline. After committing your code change, it will take about 5 to 10 minutes for the changes to be deployed to your live service running in Fargate. During this time, AWS CodePipeline will orchestrate triggering a pipeline execution when the changes have been checked into your CodeCommit repository, trigger your CodeBuild project to initiate a new build, and retrieve the docker image that was pushed to ECR by CodeBuild and perform an automated ECS Update Service action to connection drain the existing containers that are running in your service and replace them with the newly built image. Refresh your Mythical Mysfits website in the browser to see that the changes have taken effect.
You can view the progress of your code change through the CodePipeline console here (no actions needed, just watch the automation in action!): AWS CodePipeline
Module 3 - Adding a Data Tier with Amazon DynamoDB
 
Time to complete: 20 minutes
Services used:
•	Amazon DynamoDB
Overview
Now that you have a service deployed and a working CI/CD pipeline to deliver changes to that service automatically whenever you update your code repository, you can quickly move new application features from conception to available for your Mythical Mysfits customers. With this increased agility, let's add another foundational piece of functionality to the Mythical Mysfits website architecture, a data tier. In this module you will create a table in Amazon DynamoDB, a managed and scalable NoSQL database service on AWS with super fast performance. Rather than have all of the Mysfits be stored in a static JSON file, we will store them in a database to make the websites future more extensible and scalable.
Adding a NoSQL Database to Mythical Mysfits
Create a DynamoDB Table
To add a DynamoDB table to the architecture, we have included another JSON CLI input file that defines a table called MysfitsTable. This table will have a primary index defined by a hash key attribute called MysfitId, and two more secondary indexes. The first secondary index will have the hash key of GoodEvil and a range key of MysfitId, and the second secondary index will have the hash key of LawChaos and a range key of MysfitId. These two secondary indexes will allow us to execute queries against the table to retrieve all of the mysfits that match a given Species or Alignment to enable the filter functionality you may have noticed isn't yet working on the website. You can view this file at ~/environment/aws-modern-application-workshop/module-3/aws-cli/dynamodb-table.json. No changes need to be made to this file and it is ready to execute. To learn more about indexes in DynamoDB and other core concepts, visit this page.
To create the table using the AWS CLI, execute the following command in the Cloud9 terminal:
aws dynamodb create-table --cli-input-json file://~/environment/aws-modern-application-workshop/module-3/aws-cli/dynamodb-table.json
After the command runs, you can view the details of your newly created table by executing the following AWS CLI command in the terminal:
aws dynamodb describe-table --table-name MysfitsTable
If we execute the following command to retrieve all of the items stored in the table, you'll see that the table is empty:
aws dynamodb scan --table-name MysfitsTable
{
    "Count": 0,
    "Items": [],
    "ScannedCount": 0,
    "ConsumedCapacity": null
}
Add Items to the DynamoDB Table
Also provided is a JSON file that can be used to batch insert a number of Mysfit items into this table. This will be accomplished through the DynamoDB API BatchWriteItem. To call this API using the provided JSON file, execute the following terminal command (the response from the service should report that there are no items that went unprocessed):
aws dynamodb batch-write-item --request-items file://~/environment/aws-modern-application-workshop/module-3/aws-cli/populate-dynamodb.json
Now, if you run the same command to scan all of the table contents, you'll find the items have been loaded into the table:
aws dynamodb scan --table-name MysfitsTable
Committing The First Real Code change
Copy the Updated Flask Service Code
Now that we have our data included in the table, let's modify our application code to read from this table instead of returning the static JSON file that was used in Module 2. We have included a new set of Python files for your Flask microservice, but now instead of reading the static JSON file will make a request to DynamoDB.
The request is formed using the AWS Python SDK called boto3. This SDK is a powerful yet simple way to interact with AWS services via Python code. It enables you to use service client definitions and functions that have great symmetry with the AWS APIs and CLI commands you've already been executing as part of this workshop. Translating those commands to working Python code is simple when using boto3. To copy the new files into your CodeCommit repository directory, execute the following command in the terminal:
cp ~/environment/aws-modern-application-workshop/module-3/app/service/* ~/environment/MythicalMysfitsService-Repository/service/
Push the Updated Code into the CI/CD Pipeline
Now, we need to check in these code changes to CodeCommit using the git command line client. Run the following commands to check in the new code changes and kick of your CI/CD pipeline:
cd ~/environment/MythicalMysfitsService-Repository
git add .
git commit -m "Add new integration to DynamoDB."
git push
Now, in just 5-10 minutes you'll see your code changes make it through your full CI/CD pipeline in CodePipeline and out to your deployed Flask service to AWS Fargate on Amazon ECS. Feel free to explore the AWS CodePipeline console to see the changes progress through your pipeline.
Update The Website Content in S3
Finally, we need to publish a new index.html page to our S3 bucket so that the new API functionality using query strings to filter responses will be used. The new index.html file is located at ~/environment/aws-modern-application-workshop/module-3/web/index.html. Open this file in your Cloud9 IDE and replace the string indicating “REPLACE_ME” just as you did in Module 2, with the appropriate NLB endpoint. Remember do not inlcude the /mysfits path. Refer to the file you already edited in the /module-2/ directory if you need to. After replacing the endpoint to point at your NLB, upload the new index.html file by running the following command (replacing with the name of the bucket you created in Module 1:

aws s3 cp --recursive ~/environment/aws-modern-application-workshop/module-3/web/ s3://myth-misfit-aws-proj/

Re-visit your Mythical Mysfits website to see the new population of Mysfits loading from your DynamoDB table and how the Filter functionality is working!
That concludes module 3
Module 4: Adding User and API features with Amazon API Gateway and AWS Cognito
 
Time to complete: 60 minutes
Services used:
•	Amazon Cognito
•	Amazon API Gateway
•	Amazon Simple Storage Service (S3)
Overview
In order to add some more critical aspects to the Mythical Mysfits website, like allowing users to vote for their favorite mysfit and adopt a mysfit, we need to first have users register on the website. To enable registration and authentication of website users, we will create a User Pool in AWS Cognito - a fully managed user identity management service. Then, to make sure that only registered users are authorized to like or adopt mysfits on the website, we will deploy an REST API with Amazon API Gateway to sit in front of our NLB. Amazon API Gateway is also a managed service, and provides commonly required REST API capabilities out of the box like SSL termination, request authorization, throttling, API stages and versioning, and much more.
Adding a User Pool for Website Users
Create the Cognito User Pool
To create the Cognito User Pool where all of the Mythical Mysfits visitors will be stored, execute the following CLI command to create a user pool named MysfitsUserPool and indicate that all users who are registered with this pool should automatically have their email address verified via confirmation email before they become confirmed users.
aws cognito-idp create-user-pool --pool-name MysfitsUserPool --auto-verified-attributes email
Copy the response from the above command, which includes the unique ID for your user pool that you will need to use in later steps. Eg: Id: us-east-1_ab12345YZ
Create a Cognito User Pool Client
Next, in order to integrate our frontend website with Cognito, we must create a new User Pool Client for this user pool. This generates a unique client identifier that will allow our website to be authorized to call the unauthenticated APIs in cognito where website users can sign-in and register against the Mythical Mysfits user pool. To create a new client using the AWS CLI for the above user pool, run the following command (replacing the --user-pool-id value with the one you copied above):

aws cognito-idp create-user-pool-client --user-pool-id Replace_Me --client-name MysfitsUserPoolClient
Adding a new REST API with Amazon API Gateway
Create an API Gateway VPC Link
Next, let's turn our attention to creating a new RESTful API in front of our existing Flask service, so that we can perform request authorization before our NLB receives any requests. We will do this with Amazon API Gateway, as described in the module overview. In order for API Gateway to privately integrate with our NLB, we will configure an API Gateway VPC Link that enables API Gateway APIs to directly integrate with backend web services that are privately hosted inside a VPC. Note: For the purposes of this workshop, we created the NLB to be internet-facing so that it could be called directly in earlier modules. Because of this, even though we will be requiring Authorization tokens in our API after this module, our NLB will still actually be open to the public behind the API Gateway API. In a real-world scenario, you should create your NLB to be internal from the beginning (or create a new internal load balancer to replace the existing one), knowing that API Gateway would be your strategy for Internet-facing API authorization. But for the sake of time, we'll use the NLB that we've already created that will stay publicly accessible.
Create the VPC Link for our upcoming REST API using the following CLI command (you will need to replace the indicated value with the Load Balancer ARN you saved when the NLB was created in module 2):
aws apigateway create-vpc-link --name MysfitsApiVpcLink --target-arns REPLACE_ME_NLB_ARN > ~/environment/api-gateway-link-output.json

The above command will create a file called api-gateway-link-output.json that contains the id for the VPC Link that is being created. It will also show the status as PENDING, similar to below. It will take about 5-10 minutes to finish being created, you can copy the id from this file and proceed on to the next step.

{
    "status": "PENDING",
    "targetArns": [
        "YOUR_ARN_HERE"
    ],
    "id": "abcdef1",
    "name": "MysfitsApiVpcLink"
}

With the VPC link creating, we can move on to create the actual REST API using Amazon API Gateway.
Create the REST API using Swagger
Your MythicalMysfits REST API is defined using Swagger, a popular open-source framework for describing APIs via JSON. This Swagger definition of the API is located at ~/environment/aws-modern-applicaiton-workshop/module-4/aws-cli/api-swagger.json. 

Open this file and you'll see the REST API and all of its resources, methods, and configuration defined within.
There are several places within this JSON file that need to be updated to include parameters specific to your Cognito User Pool, as well as your Network Load Balancer.
The securityDefinitions object within the API definition indicates that we have setup an apiKey authorization mechanism using the Authorization header. You will notice that AWS has provided custom extensions to Swagger using the prefix x-amazon-api-gateway-, these extensions are where API Gateway specific functionality can be added to typical swagger files to take advantage of API Gateway-specific capabilities.
CTRL-F through the file to search for the various places REPLACE_ME is located and awaiting your specific parameters. Once the edits have been made, save the file and execute the following AWS CLI command:

aws apigateway import-rest-api --parameters endpointConfigurationTypes=REGIONAL --body file://~/environment/aws-modern-application-workshop/module-4/aws-cli/api-swagger.json --fail-on-warnings

Copy the response this command returns and save the id value for the next step:
{
    "name": "MysfitsApi",
    "endpointConfiguration": {
        "types": [
            "REGIONAL"
        ]
    },
    "id": "abcde12345",
    "createdDate": 1529613528
}
Deploy the API
Now, our API has been created, but it's yet to be deployed anywhere. To deploy our API, we must first create a deployment and indicate which stage the deployment is fore. A stage is a named reference to a deployment, which is a snapshot of the API. You use a Stage to manage and optimize a particular deployment. For example, you can set up stage settings to enable caching, customize request throttling, configure logging, define stage variables or attach a canary release for testing. We will call our stage prod. To create a deployment for the prod stage, execute the following CLI command:
aws apigateway create-deployment --rest-api-id REPLACE_ME_WITH_API_ID --stage-name prod
With that, our REST API that's capable of user Authorization is deployed and available on the Internet... but where?! Your API is available at the following location:
https://REPLACE_ME_WITH_API_ID.execute-api.REPLACE_ME_WITH_REGION.amazonaws.com/prod
Copy the above, replacing the appropriate values, and add /mysfits to the end of the URI. Entered into a browser address bar, you should once again see your Mysfits JSON response. But, we've added several capabilities like adopting and liking mysfits that our Flask service backend doesn't have implemented yet.
Let's take care of that next.
Updating the Mythical Mysfits Website
Update the Flask Service Backend
To accommodate the new functionality to view Mysfit Profiles, like, and adopt them, we have included updated Python code for your backend Flask web service. Let's overwrite your existing codebase with these files and push them into the repository:
cd ~/environment/MythicalMysfitsService-Repository/
cp -r ~/environment/aws-modern-application-workshop/module-4/app/* .
git add .
git commit -m "Update service code backend to enable additional website features."
git push

While those service updates are being automatically pushed through your CI/CD pipeline, continue on to the next step.
Update the Mythical Mysfits Website in S3
Open the new version of the Mythical Mysfits index.html file we will push to S3 shortly, it is located at: ~/environment/aws-modern-application-workshop/module-4/app/web/index.html In this new index.html file, you'll notice additional HTML and JavaScript code that is being used to add a user registration and login experience. This code is interacting with the AWS Cognito JavaScript SDK to help manage registration, authentication, and authorization to all of the API calls that require it.
In this file, replace the strings REPLACE_ME inside the single quotes with the OutputValues you copied from above and save the file:
 
Also, for the user registration process, you have an additional two HTML files to insert these values into. register.html and confirm.html. Insert the copied values into the REPLACE_ME strings in these files as well.
Now, lets copy these HTML files, as well as the Cognito JavaScript SDK to the S3 bucket hosting our Mythical Mysfits website content so that the new features will be published online.
aws s3 cp --recursive ~/environment/aws-modern-application-workshop/module-4/web/ s3://YOUR-S3-BUCKET/
Refresh the Mythical Mysfits website in your browser to see the new functionality in action!
This concludes Module 4.
Module 5: Capturing User Behavior

Time to complete: 30 minutes
Services used:
•	AWS CloudFormation
•	AWS Kinesis Data Firehose
•	Amazon S3
•	Amazon API Gateway
•	AWS Lambda
•	AWS CodeCommit
•	AWS Serverless Appliation Model (AWS SAM)
•	AWS SAM Command Line Interface (SAM CLI)
Overview
Now that your Mythical Mysfits site is up and running, let's create a way to better understand how users are interacting with the website and its Mysfits. It would be very easy for us to analyze user actions taken on the website that lead to data changes in our backend - when mysfits are adopted or liked. But understanding the actions your users are taking on the website before a decision to like or adopt a mysfit could help you design a better user experience in the future that leads to mysfits getting adopted even faster. To help us gather these insights, we will implement the ability for the website frontend to submit a tiny request, each time a mysfit profile is clicked by a user, to a new microservice API we'll create. Those records will be processed in real-time by a serverless code function, aggregated, and stored for any future analysis that you may want to perform.
Modern application design principles prefer focused, decoupled, and modular services. So rather than add additional methods and capabilities within the existing Mysfits service that you have been working with so far, we will create a new and decoupled service for the purpose of receiving user click events from the Mysfits website. This full stack has been represented using a provided CloudFormation template.
The serverless real-time processing service stack you are creating includes the following AWS resources:
•	An AWS Kinesis Data Firehose delivery stream: Kinesis Firehose is a highly available and managed real-time streaming service that accepts data records and automatically ingests them into several possible storage destinations within AWS, examples including an Amazon S3 bucket, or an Amazon Redshift data warehouse cluster. Kinesis Firehose also enables all of the records received by the stream to be automatically delivered to a serverless function created with AWS Lambda This means that code you've written can perform any additional processing or transformations of the records before they are aggregated and stored in the configured destination.
•	An Amazon S3 bucket: A new bucket will be created in S3 where all of the processed click event records are aggregated into files and stored as objects.
•	An AWS Lambda function: AWS Lambda enables developers to write code functions that only contain what their logic requires and have their code be deployed, invoked, made highly reliable, and scale without having to manage infrastructure whatsoever. Here, a Serverless code function is defined using AWS SAM. It will be deployed to AWS Lambda, written in Python, and then process and enrich the click records that are received by the delivery stream. The code we've written is very simple and the enriching it does could have been accomplished on the website frontend without any subsequent processing at all. The function retrieves additional attributes about the clicked on Mysfit to make the click record more meaningful (data that was already retrieved by the website frontend). But, for the purpose of this workshop, the code is meant to demonstrate the architectural possibilities of including a serverless code function to perform any additional processing or transformation required, in real-time, before records are stored. Once the Lambda function is created and the Kinesis Firehose delivery stream is configured as an event source for the function, the delivery stream will automatically deliver click records as events to code function we've created, receive the responses that our code returns, and deliver the updated records to the configured Amazon S3 bucket.
•	An Amazon API Gateway REST API: AWS Kinesis Firehose provides a service API just like other AWS services, and in this case we are using its PutRecord operation to put user click event records into the delivery stream. But, we don't want our website frontend to have to directly integrate with the Kinesis Firehose PutRecord API. Doing so would require us to manage AWS credentials within our frontend code to authorize those API requests to the PutRecord API, and it would expose to users the direct AWS API that is being depended on (which may encourage malicious site visitors to attempt to add records to the delivery stream that are malformed, or harmful to our goal of understanding real user behavior). So instead, we will use Amazon API Gateway to create an AWS Service Proxy to the PutRecord API of Kinesis Firehose. This allows us to craft our own public RESTful endpoint that does not require AWS credential management on the frontend for requests. Also, we will use a request mapping template in API Gateway as well, which will let us define our own request payload structure that will restrict requests to our expected structure and then transform those well-formed requests into the structure that the Kinesis Firehose PutRecord API requires.
•	IAM Roles: Kinesis Firehose requires a service role that allows it to deliver received records as events to the created Lambda function as well as the processed records to the destination S3 bucket. The Amazon API Gateway API also requires a new role that permits the API to invoke the PutRecord API within Kinesis Firehose for each received API request.
Before we launch the CloudFormaiton template described above, we need to update and modify the Lambda function code it will deploy.
Copy the Streaming Service Code
Create a new CodeCommit Repository
This new stack you will deploy using CloudFormation will not only contain the infrastructure environment resources, but the application code itself that AWS Lambda will execute to process streaming events. To bundle the creation of our infrastructure and code together in one deployment, we are going to use another AWS tool that comes pre-installed in the AWS Cloud9 IDE - AWS SAM CLI. Code for AWS Lambda functions is delivered to the service by uploading the function code in a .zip package to an Amazon S3 bucket. The SAM CLI automates that process for us. Using it, we can create a CloudFormation template that references locally in the filesystem where all of the code for our Lambda function is stored. Then, the SAM CLI will package it into a .zip file, upload it to a configured Amazon S3 bucket, and create a new CloudFormation template that indicates the location in S3 where the created .zip package has been uploaded for deployment to AWS Lambda. We can then deploy that SAM CLI-generated CloudFormation template to AWS and watch the environment be created along with the Lambda function that uses the SAM CLI-uploaded code package.
First, let's create a new CodeCommit repository where the streaming service code will live:
aws codecommit create-repository --repository-name MythicalMysfitsStreamingService-Repository
In the response to that command, copy the value for "cloneUrlHttp". It should be of the form: https://git-codecommit.REPLACE_ME_REGION.amazonaws.com/v1/repos/MythicalMysfitsStreamingService-Repository
Next, let's clone that new and empty repository into our IDE:
cd ~/environment/
git clone REPLACE_ME_WITH_ABOVE_CLONE_URL
Copy the Streaming Service Code Base
Now, let's move our working directory into this new repository:
cd ~/environment/MythicalMysfitsStreamingService-Repository/
Then, copy the module-5 application components into this new repository directory:
cp -r ~/environment/aws-modern-application-workshop/module-5/app/streaming/* .

And let's copy the CloudFormation template for this module as well.
cp ~/environment/aws-modern-application-workshop/module-5/cfn/* .
Update the Lambda Function Package and Code
Use pip to Install Lambda Function Dependencies
Now, we have the repository directory set with all of the provided artifacts:
•	A CFN template for creating the full stack.
•	A Python file that contains the code for our Lambda function: streamProcessor.py
This is a common approach that AWS customers take - to store their CloudFormation templates alongside their application code in a repository. That way, you have a single place where all changes to application and it's environment can be tracked together.
But, if you look at the code inside the streamProcessor.py file, you'll notice that it's using the requests Python package to make an API requset to the Mythical Mysfits service you created previously. External libraries are not automatically included in the AWS Lambda runtime environment, because different AWS customers may depend on different versions of various libraries, etc. You will need to package all of your library dependencies together with your Lambda code function prior to it being uploaded to the Lambda service. We will use the Python package manager pip to accomplish this. In the Cloud9 terminal, run the following command to install the requests package and it's dependencies locally alongside your function code:
pip install requests -t .
Once this command completes, you will see several additional python package folders stored within your repository directory.
Update the Lambda Function Code
Next, we have one code change to make prior to our Lambda function code being completely ready for deployment. There is a line within the streamProcessor.py file that needs to be replaced with the ApiEndpoint for your Mysfits service API - the same service ApiEndpoint that you created in module-4 and used on the website frontend. Be sure to update the file you have copied into the new StreamingService repository directory.
 
That service is responsible for integrating with the MysfitsTable in DynamoDB, so even though we could write a Lambda function that directly integrated with the DynamoDB table as well, doing so would intrude upon the purpose of the first microservice and leave us with multiple/separate code bases that integrated with the same table. Instead, we will integrate with that table through the existing service and have a much more decoupled and modular application architecture.
Push Your Code into CodeCommit
Let's commit our code changes to the new repository so that they're saved in CodeCommit:
git add .
git commit -m "New stream processing service."
git push
Creating the Streaming Service Stack
Create an S3 Bucket for Lambda Function Code Packages
With that line changed in the Python file, and our code committed, we are ready to use the AWS SAM CLI to package all of our function code, upload it to S3, and create the deployable CloudFormation template to create our streaming stack.
First, use the AWS CLI to create a new S3 bucket where our Lambda function code packages will be uploaded to. S3 bucket names need to be globally unique among all AWS customers, so replace the end of this bucket name with a string that's unique to you:
aws s3 mb s3://REPLACE_ME_YOUR_BUCKET_NAME/
Use the SAM CLI to Package your Code for Lambda
With our bucket created, we are ready to use the SAM CLI to package and upload our code and transform the CloudFormation template, be sure to replace the last command parameter with the bucket name you just created above (this command also assumes your terminal is still in the repository working directory):
sam package --template-file ./real-time-streaming.yml --output-template-file ./transformed-streaming.yml --s3-bucket REPLACE_ME_YOUR_BUCKET_NAME
If successful, you will see the newly created transformed-streaming.yml file exist within the ./MythicalMysfitsStreamingService-Repository/ directory, if you look in its contents, you'll see that the CodeUri parameter of the serverless Lambda function has been updated with the object location where the SAM CLI has uploaded your packaged code.
Deploy the Stack using AWS CloudFormation
Also returned by the SAM CLI command is the CloudFormation command needed to be executed to create our new full stack. But because our stack creates IAM resources, you'll need to add one additional parameter to the command. Execute the following command to deploy the streaming stack:
aws cloudformation deploy --template-file /home/ec2-user/environment/MythicalMysfitsStreamingService-Repository/transformed-streaming.yml --stack-name MythicalMysfitsStreamingStack --capabilities CAPABILITY_IAM
Once this stack creation is complete, the full real-time processing microservice will be created.
In future scenarios where only code changes have been made to your Lambda function, and the rest of your CloudFormation stack remains unchanged, you can repeat the same AWS SAM CLI and CloudFormation commands as above. This will result in the infrastructure environment remaining unchanged, but a code deployment occurring to your Lambda function.
Sending Mysfit Profile Clicks to the Service
Update the Website Content
With the streaming stack up and running, we now need to publish a new version of our Mythical Mysfits frontend that includes the JavaScript that sends events to our service whenever a mysfit profile is clicked by a user.
The new index.html file is included at: ~/environment/aws-modern-application-workshop/module-5/web/index.html
This file contains the same placeholders as module-4 that need to be updated, as well as an additional placeholder for the new stream processing service endpoint you just created. For the previous variable values, you can refer to the previous index.html file you updated as part of module-4.
Perform the following command for the new streaming stack to retrieve the new API Gateway endpoint for your stream processing service:
aws cloudformation describe-stacks --stack-name MythicalMysfitsStreamingStack
Push the New Site Version to S3
Replace the final value within index.html with the streamingApiEndpoint and you are ready to publish your final Mythical Mysfits home page update:

aws s3 cp ~/environment/aws-modern-application-workshop/module-5/web/index.html s3://myth-mmisfit-aws-proj/
Refresh your Mythical Mysfits website in the browser once more and you will now have a site that records and publishes each time a user clicks on a mysfits profile!
To view the records that have been processed, they will arrive in the destination S3 bucket created as part of your MythicalMysfitsStreamingStack. Visit the S3 console here and explore the bucket you created for the streaming records (it will be prefixed with mythicalmysfitsstreamings-clicksdestinationbucket): Amazon S3 Console
This concludes Module 5.
Module 6: Tracing Application Requests
 
Time to complete: 45 minutes
Services used:
•	AWS CloudFormation
•	AWS X-Ray
•	Amazon DynamoDB
•	Amazon Simple Notification Service (AWS SNS)
•	Amazon S3
•	Amazon API Gateway
•	AWS Lambda
•	AWS CodeCommit
•	AWS Serverless Appliation Model (AWS SAM)
•	AWS SAM Command Line Interface (SAM CLI)
Overview
Next, we will show you how to deeply inspect and analyze request behavior on new functionality for the Mythical Mysfits site, using AWS X-Ray. The new functionality will enable users to contact the Mythical Mysfits staff, via a Contact Us button we'll place on the site. Since much of the steps required to create a new microservice to handle receiving user questions mimics activities you've performed earlier in this workshop, we have provided a CloudFormation template that will programmatically create the new service using AWS SAM.
The CloudFormation template includes:
•	An API Gateway API: A new microservice will be created that has a single REST resource, /questions. This API will receive the text of a user question and the email address for the user who submitted it.
•	A DynamoDB Table: A new DynamoDB table where the user questions will be stored and persisted. This DynamoDB table will be created with a DynamoDB Stream enabled. The stream will provide a real-time event stream for all of the new questions that are stored within the database so that they can be immediately processed.
•	An AWS SNS Topic: AWS SNS allows applications to publish messages and to subscribe to message topics. We will use a new topic as a way to send notifications to a subscribed email address for a email address.
•	Two AWS Lambda Functions: One AWS Lambda function will be used as the service backend for the question API requests. The other AWS Lambda function will receive events from the questions DynamoDB table and publish a message for each of them to the above SNS topic. If you view the CloudFormation resource definitions for these functions in the file ~/environment/aws-modern-application-workshop/module-6/app/cfn/customer-question.yml, you'll see a Property listed that indicates Tracing: Active. This means that all invocations of the Lambda function will automatically be traced by AWS X-Ray.
•	IAM Roles required for each of the above resources and actions.
Bring your command line terminal back to your root Cloud9 environment directory so that our subsequent commands are executed from the same place:
cd ~/environment/
First, let's create another new AWS CodeCommit repository for the new microservice:
aws codecommit create-repository --repository-name MythicalMysfitsQuestionsService-Repository
Next, clone the new repository into your IDE using the cloneUrlHttp value taken from the response to the above create-repository command you just ran:
git clone REPLACE_ME_WITH_ABOVE_CLONE_URL
Then move your terminal to the new repository directory:
cd ~/environment/MythicalMysfitsQuestionsService-Repository
Now, copy the new QuestionsService application code into the repository directory, followed by the CloudFormation Template necessary to deploy the infrastructure required by the QuestionsService:
cp -r ~/environment/aws-modern-application-workshop/module-6/app/* .
 cp -r ~/environment/aws-modern-application-workshop/module-6/cfn/* .
For this new microservice, we have included all of the packages necessary for the AWS Lambda functions to be deployed and invoked. Before deploying it, you are required to create another S3 bucket to be used by AWS SAM as a destination for your packaged QuestionService code (remember all S3 bucket names need to be unique and have naming constraints):
aws s3 mb s3://REPLACE_ME_NEW_QUESTIONS_SERVICE_CODE_BUCKET_NAME
and execute the following command to transform the SAM template into CloudFormation...
sam package --template-file ~/environment/MythicalMysfitsQuestionsService-Repository/customer-questions.yml --output-template-file ~/environment/MythicalMysfitsQuestionsService-Repository/transformed-questions.yml --s3-bucket REPLACE_ME_NEW_QUESTIONS_SERVICE_CODE_BUCKET_NAME
...and then deploy the CloudFormation stack. 
Note: provide an email address you have access to as the REPLACE_ME_EMAIL_ADDRESS parameter (replace it prior to pasting this command, the stack creation will fail if you execute the command without providing a valid email address). This will be the email address that user questions are published to by the SNS topic:
aws cloudformation deploy --template-file /home/ec2-user/environment/MythicalMysfitsQuestionsService-Repository/transformed-questions.yml --stack-name MythicalMysfitsQuestionsService-Stack --capabilities CAPABILITY_IAM --parameter-overrides AdministratorEmailAddress=REPLACE_ME_YOUR_EMAIL_ADDRESS




When this command completes, let's capture the output of the stack so that we can reference its values in subsequent steps (will create a file in your IDE called questions-service-output.json):

aws cloudformation describe-stacks --stack-name MythicalMysfitsQuestionsService-Stack > ~/environment/questions-service-output.json
Next, visit the email address provided and CONFIRM your subscription to the SNS topic:  
Now, with the new backend service running, let's make the required changes to index.html so that the frontend can include the new Contact Us button. Open ~/environment/aws-modern-application-workshop/module-6/web/index.html and insert the API endpoint for the new Questions API, retrieve the output value of REPLACE_ME_QUESTIONS_API_ENDPOINT from the above CloudFormation stack (located at ~/environment/questions-service-output.json). Remember that you'll also need to paste the same values that you previously used prior to this module for the other Mysfits microservices endpoints and user pool.
Once you've made the necessary change to index.html run the following command to copy it to your website S3 bucket.
aws s3 cp ~/environment/aws-modern-application-workshop/module-6/web/index.html s3://YOUR-S3-BUCKET/
Now that the new Contact Us functionality is deployed, visit the website and submit a question or two. If you've confirmed the subscription to SNS in the step above, you'll start to see those questions arrive in your inbox! When you've seen that email arrive, you can move on to explore and analyze the request lifecycle.
Now, to start seeing the request behavior for this microservice, visit the AWS X-Ray console to explore:
AWS X-Ray Console
Upon visiting the X-Ray Console you'll be immediately viewing a service map, which shows the dependency relationship between all the components that X-Ray receives trace segments for:
 
At first, this service map only includes our AWS Lambda functions. Feel free to explore the X-Ray console to learn more about drilling into the data automatically made visible just by listing the Tracing: Active property in the CloudFormation template you deployed.
Next, we're going to instrument more of the microservice stack so that all of the service dependencies are included in the service map and recorded trace segments.
First, we will instrument the API Gateway REST API. Issue the following command inserting the value for REPLACE_ME_QUESTIONS_REST_API_ID that is located in the questions-service-output.json file created but the most recent CloudFormation descrbe-stacks command you just ran. The below command will enable tracing to start at the API Gateway level of the service stack:

aws apigateway update-stage --rest-api-id REPLACE_ME_QUESTIONS_REST_API_ID --stage-name prod --patch-operations op=replace,path=/tracingEnabled,value=true

Now, submit another question to the Mythical Mysfits website and you'll see that the REST API is also included in the service map!
 
Next, you will use the AWS X-Ray SDK for Python so that the services being called by the two Lambda functions as part of the questions stack are also represented in the X-Ray service map. The code has been written already to accomplish this, you just need to uncomment the relevant lines (uncommenting is performed by deleting the preceding # in a line of python code). In the Lambda function code, you will see comments that indicate #UNCOMMENT_BEFORE_2ND_DEPLOYMENT or #UNCOMMENT_BEFORE_3RD_DEPLOYMENT.
You've already completed the first deployment of these functions using CloudFormation, so this will be your 2nd Deployment. Uncomment each of the lines indicated below all cases of UNCOMMENT_BEFORE_2ND_DEPLOYMENT in the following files, and save the files after making the required changes:
•	~/environment/MythicalMysfitsQuestionsStack-Repository/PostQuestionsService/mysfitsPostQuestion.py
•	~/environment/MythicalMysfitsQuestionsStack-Repository/ProcessQuestionsStream/mysfitsProcessStream.py
Note: The changes you've uncommented enable the AWS X-Ray SDK to instrument the AWS Python SDK (boto3) to capture tracing data and record it within the Lambda service anytime an AWS API call is made. Those few lines of code are all that's required in order for X-Ray to automatically trace your AWS service map throughout a serverless application using AWS Lambda!
With those changes made, deploy an update to the Lambda function code by issuing the following two commands:
First, use SAM to create new Lambda function code bundles and upload the packaged code to S3:
sam package --template-file ~/environment/MythicalMysfitsQuestionsService-Repository/customer-questions.yml --output-template-file ~/environment/MythicalMysfitsQuestionsService-Repository/transformed-questions.yml --s3-bucket REPLACE_ME_NEW_QUESTIONS_SERVICE_CODE_BUCKET_NAME
Second, use CloudFormation to deploy the changes to the running stack:
aws cloudformation deploy --template-file /home/ec2-user/environment/MythicalMysfitsQuestionsService-Repository/transformed-questions.yml --stack-name MythicalMysfitsQuestionsService-Stack --capabilities CAPABILITY_IAM --parameter-overrides AdministratorEmailAddress=REPLACE_ME_YOUR_EMAIL_ADDRESS

Once that command completes, submit an additional question to the Mythical Mysfits website and take a look at the X-Ray console again. Now you're able to trace how Lambda is interacting with DynamoDB as well as SNS!
 
The final step in this module is to familiarize yourself with using AWS X-Ray to triage problems in your application. To accomplish this, we're going to by mysfits ourselves and have you add some terrible code to your application. All this code will do is cause your web service to add 5 seconds of latency and throw an exception for randomized requests :) .
Go back into the following file and remove the comments indicated by #UNCOMMENT_BEFORE_3RD_DEPLOYMENT:
•	~/environment/MythicalMysfitsQuestionsStack-Repository/PostQuestionsService/mysfitsPostQuestion.py
This is the code that will cause your Lambda function to throw an exception. Also, you can note above the hangingException() function that we're using out-of-the-box functionality of the AWS X-Ray SDK to record a trace subsegment each time that function is called. Now when you drill into the Trace for a particular request, you'll be able to see that all requests are stuck sitting within this function for at least 5 seconds before they throw the exception.
Using this functionality within your own applications will help you identify similar latency bottlenecks within your code, or places where exceptions are being thrown.
After you make the required code changes and save the mysfitsPostQuestion.py file, run the same two commands as before to package and deploy your changes to CloudFormation. Use the UP arrow key within your Cloud9 terminal to see past commands and first execute the sam package command in your history, and then the aws cloudformation deploy command subsequently.
After you've issued those two commands, submit another few questions on your Mysfits website. Some of these questions will fail to show up in your inbox. Because your new and terrible code has thrown an error!
If you visit the X-Ray console again, you'll notice that the service map for the MysfitPostQuestionsFunction Lambda function has a ring around it that is no longer only Green. That's because Error responses have been generated there. X-Ray will give you this visual representation of overall service health across all of the instrumented services in your service map:
 
If you click on that service within the service map, you'll notice on the right side of the X-Ray console, you have the ability to view the traces that either match the highlighted overall latency shown within the service latency graph and/or the status code you're interested in. Zoom the latency graph so that the blip around 5 seconds is within the graph and/or select the Error check box and click View traces:
 
This will take you to the Trace dashboard where you can explore that specific requests lifecycle, see the latency spend on each segment of the service, and view the exception reported and associated stack trace. Click on any of the IDs for the Traces where the response is reported as a 502, then on the subsequent Trace Details page, click on the hangingException to view that specific subsegment where the exception was thrown in our code:
 
Congratulations, you've completed module 6!

Module 7: Using Machine Learning to Recommend a Mysfit
 
Time to complete: 45 minutes
Services used:
•	Amazon SageMaker
•	AWS CloudFormation
•	Amazon S3
•	Amazon API Gateway
•	AWS Lambda
•	AWS CodeCommit
•	AWS Serverless Appliation Model (AWS SAM)
•	AWS SAM Command Line Interface (SAM CLI)
Overview
One of the fastest growing areas of technology is machine learning. As the cost to leverage high performance computing environments has continued to decrease, the number of use cases where Machine Learning algorithms can be economically applied has grown astronomically. We can even use machine learning to help the visitors of MythicalMysfits.com discover which Mysfit is perfect for them. And that's just what you will do in this module.
With Amazon SageMaker, a fully managed machine learning service, you are going to introduce a new machine learning based recommendation engine on the Mythical Mysfits site. This will enable site visitors to provide us with details about themselves, and we will use that information to invoke a machine learning model and predict which Mysfit would best suit them. Amazon SageMaker will give you the tools needed to:
•	Prepare data for model training (using sample data we've created and made available in S3)
•	Train a machine learning model using one of the many already-implemented machine learning algorithms that SageMaker provides, and evaluate the model's accuracy.
•	Store and Deploy the created model so that it can be invoked at-scale to provide inferences to your application (the Mythical Mysfits website).
Building a Machine Learning Model
The Importance of Data
A prerequisite to beginning any machine learning journey is gathering data. The data used will define your algorithm's understanding of the use case its being asked to play a role in, and its ability to make accurate predictions/inferences. If you introduce machine learning into your application using insufficient, irrelevant, or inaccurate data, you risk bringing more harm than benefit to your application.
However, for our Mysfits site, we obviously do not have vast quantities of accurate and historical adoption data to make mysfit recommendations. So instead, we generated loads of data randomly. This means the model that you'll build will be making predictions based on randomized data, and its "accuracy" will suffer because the data was randomly generated. This data set will still allow you to become familiar with how to use SageMaker in a real application... but we will be glossing over all the critical steps required in the real world to identify, gather, and curate an appropriate data set to be used with machine learning successfully.
Creating a Hosted Notebook instance with SageMaker
Data Scientists and developers that want to curate data, define and run algorithms, build models, and more, all while thoroughly documenting their work can do so in a single place called a notebook. Through AWS SageMaker, you can create an EC2 instance that is preconfigured and optimized for Machine Learning and already has the Jupyter Notebooks application running on it, this is called a notebook instance. In order to create a notebook instance, we must first create some prerequisites that the notebook requires, namely an IAM Role that will give the notebook instance the permissions it needs to perform everything required. We have provided another CloudFormation template in order for you to create this new IAM Role, execute the following command to create the stack:
aws cloudformation deploy --stack-name MythicalMysfits-SageMaker-NotebookRole --template ~/environment/aws-modern-application-workshop/module-7/cfn/notebook-role.yml --capabilities CAPABILITY_NAMED_IAM
Then, run the following command to create a hosted notebook instance with SageMaker (replacing your Account_Id in the Role Arn):
aws sagemaker create-notebook-instance --notebook-instance-name MythicalMysfits-SageMaker-Notebook --instance-type ml.t2.medium --role arn:aws:iam::REPLACE_ME_ACCOUNT_ID:role/MysfitsNotbookRole
Note: It will take about 10 minutes for your notebook instance to move from Pending state to InService. You may proceed on to the next steps while the notebook is being provisioned.
Finally, there is a file in your cloned repository that will be used in the next steps that you should download. In the File Explorer in Cloud9, find MythicalMysfitsIDE/aws-modern-application-workshop/module-7/sagemaker/mysfit_recommendations_knn.ipynb, right-click it, and select Download. Save this file to your local workstation and remember where it has been saved.
Using Amazon SageMaker
Next, open up a new browser tab and visit the SageMaker console (be sure the region selected at the top-right of your AWS Management Console matches the region you're building within): Amazon SageMaker Console
Then, click on Notebook Instances.
Click the radio button next to the MythicalMysfits-SageMaker-Notebook instance that you just created via the CLI, and click Open Jupyter. This will redirect you to the Jupyter Notebook application running on your notebook instance.
 
NOTE: that for this workshop, we have created the notebook instance to allow you access it directly via the Internet, running in a service-managed VPC. For more details about accessing a notebook instance through a VPC Interface Endpoint, should you desire to for a future use case, visit this documentation page..
With Jupyter open, you will be presented with the following home page for your notebook instance:  
Click the Upload button, and then find the file you downloaded in the previous section mysfit_recommendations_knn.ipynb, then click Upload on the file line. This will create a new Notebook document on the notebook instance within Jupyter that uses the notebook file you've just uploaded.
We have pre-written the notebook required that will guide you through the code required to build a model.
Using the Hosted Notebook to Build, Train, and Deploy a Model
Click on the filename within the Jupyter application, for the file you've just uploaded, and you a new browser tab will be opened for you to work within the notebook document.
STOP!
Follow the instructions within the notebook document in order to deploy a SageMaker endpoint for predicting the best Mythical Mysfit for a user based on their quesitonnaire responses.
Once you have completed the steps within the notebook, return here to proceed with the workshop.
Creating a Serverless REST API for Model Predictions
Now that you have a deployed SageMaker endpoint, let's wrap our own Mythical Mysfits serverless REST API around the endpoint. This allows us to define the API exactly to our specifications, and for our frontend application code to continue integrating with APIs that we ourselves define rather than the native AWS service API. We'll build the microservice to be serverless using API Gateway and AWS Lambda.
Let's create another new CodeCommit repository for where you recommendations service code could be committed:
aws codecommit create-repository --repository-name MythicalMysfitsRecommendationService
Then clone the new repository to your Cloud9 environment using the cloneUrlHttp attribute in the above response:
First, set the current directory to your home directory.
cd ~/environment
Then clone the repository:
git clone REPLACE_ME_CLONE_URL
Now copy the service code and SAM template to the new repository:
cp -r ~/environment/aws-modern-application-workshop/module-7/app/service/ ~/environment/MythicalMysfitsRecommendationService/
There is one code change that you must make to the service Python code before we can deploy the API. Open ~/environment/MythicalMysfitsRecommendationService/service/recommendation.py in Cloud9. You will see a single entry that needs replacing: REPLACE_ME_SAGEMAKER_ENDPOINT
To retrieve the value required, run the following CLI command to describe your SageMaker endpoints:
aws sagemaker list-endpoints > ~/environment/sagemaker-endpoints.json
Open sagemaker-endpoints.json and copy the EndpointName value that is prefixed with: knn-ml-m4-xlarge- (this is what we prefixed our endpoint name with inside the Jupyter notebook).
Paste the EndpointValue name in the recommendation.py file and save the file.
The last step we need before using SAM to deploy our microservice is a new S3 bucket that SAM can use to package up our source code as part of package and deployment. Choose a new bucket name like before and run the following command:
aws s3 mb s3://REPLACE_ME_RECOMMENDATION_NEW_BUCKET_NAME
Now use SAM to deploy your recommendations microservice, first packaging your Python code to be used in the Lambda function:
sam package --template-file ~/environment/MythicalMysfitsRecommendationService/service/cfn/recommendations-service.yml --output-template-file ~/environment/MythicalMysfitsRecommendationService/transformed-recommendations.yml --s3-bucket REPLACE_ME_RECOMMENDATION_BUCKET_NAME
Then, deploy the stack using CloudFormation:
aws cloudformation deploy --template-file ~/environment/MythicalMysfitsRecommendationService/transformed-recommendations.yml --stack-name MythicalMysfitsRecommendationsStack --capabilities CAPABILITY_NAMED_IAM
When this command completes, you have deployed the REST API microservice wrapper for the SageMaker endpoint you created through the Jupyter notebook.
Run the following command to retrieve what the API endpoint is for your new service and save it to a new JSON file recommendation-endpoint.json:
aws cloudformation describe-stacks --stack-name MythicalMysfitsRecommendationsStack > ~/environment/recommendation-endpoint.json
Let's test the new service with the following CLI command that uses curl (a Linux tool for making web requests). This will show you the recommendations in action for a new data point matching the CSV lines we used for training data. You'll use the OutputValue from the recommendation-endpoint.json value above to invoke your own REST API, be sure to append /recommendations after the endpoint, as shown below:
curl -d '{"entry": [1,2,3,4,5]}' REPLACE_ME_RECOMMENDATION_API_ENDPOINT/recommendations -X POST
You should get a response like the following:
{"recommendedMysfit": "c0684344-1eb7-40e7-b334-06d25ac9268c"}
You're now ready to integrate this new backend functionality into the Mythical Mysfits website.
Updating the Mythical Mysfits Site
A new index.html file has been included in Module 7 that contain the code required to present users with the Mysfit Recommendation questionnaire and present them with their recommended Mysfit.

Remember that you'll need to copy the existing values from previous modules and insert them as required for the existing websites functionality.
Open ~/environment/aws-modern-application-workshop/module-7/web/index.html to edit the required REPLACE_ME_ values, the newest one being REPLACE_ME_RECOMMENDATION_API_ENDPOINT that can be retrieved as the output value contained within ~/environment/recommendation-endpoint.json
After completing the required edits in index.html, run the following command once again to update your Mythical Mysfits website bucket:
aws s3 cp ~/environment/aws-modern-application-workshop/module-7/web/index.html s3://YOUR-S3-BUCKET/
Now you should be able to see a new Recommend a Mysfit button on the website, which will present you with the questionnaire, capture your selections, and submit those selections to our recommendations microservice.
Congratulations, you've completed module 7!
aws cloudformation delete-stack --stack-name STACK-NAME-HERE
aws cloudformation delete-stack --stack-name MythicalMysfitsStreamingStack
aws cloudformation delete-stack --stack-name MythicalMysfitsCoreStack
aws cloudformation delete-stack --stack-name MythicalMysfitsQuestionsService-Stack
aws cloudformation delete-stack --stack-name MythicalMysfitsRecommendationsStack
