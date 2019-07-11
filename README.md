## Table of contents
* [General info](#general-info)
* [Useful Command](#usefule-command)
* [Setup](#setup)

## General info
This is cloudformation template. The purpose of this template is to automate general deployment for all three-tier, two-DC deployments. NAT instance sits in public subnet and serves as jumpbox. All other nodes sits in private subnet.

Application load balancer will be configured if I have the time.
	
## Useful Commands
Cloudformation template

<!--- aws cloudformation validate-template template-body file://SolutionStack.yml -->
aws cloudformation validate-template --template-body file:///home/user/CloudFormation/SolutionStack.yaml

aws cloudformation create-stack --stack-name solstack --template-body file:///home/user/CloudFormation/SolutionStack.yaml --capabilities CAPABILITY_NAMED_IAM

--disable-rollback option to disable rollback on failure

aws cloudformation delete-stack --stack-name solstack

aws cloudformation describe-stack-events --stack-name solstack

aws cloudformation list-stacks --stack-status-filter CREATE_COMPLETE

aws s3 cp large_file.zip s3://ylu-deep-archive/large_file.zip --storage-class DEEP_ARCHIVE

## Setup
Run AWS cli command
Note: user executing the aws cli command needs to have the following permissions:

1. CloudFormation full privilege

2. Privilege to allow cloudformation tempalte to do what it needs to do (Administrator Access should be given if there are too many low privilege accesses are needed)

```
