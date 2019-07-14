## Table of contents
* [General Info](#general-info)
* [How to use](#how-to-use)
* [Expand the template](#expand-the-template)
* [Expand the template](#limitations)
* [Useful Commands](#usefule-commands)

## General Info
Many applications that are not optimized for cloud deployment have multiple tiers but the installation of each nodes requires information about other nodes in the cluster of the same layer, or even other layers. For example, a three tier deployment (application tier, database tier and search engine tier) requires the application tier to know the IP addresses of each nodes in database and search engine tiers in order to correctly complete the initial configuration. 

Solution Formation is an attempt to address this challenge. It leverages AWS Cloudformation template to create resources in an controlled sequence and also introduces several bash script to coordinate with Cloudformation. The purpose is the installer are launched at the right time when autoscaling group are up and running.

This templates creates a public subnet with application nodes and NAT/Bastion instance; and a private subnet with database nodes and search engine nodes. After instance creation, it uses cfn-init to pull coordinator script, and run the coordinator script to collect layer and stack information using aws cli command. Once that information is collected, installer is launched in each instance.

Although autoscaling group is used, this solution does not actually allow expansion of the group. Neither does it configure load balancer at this point.

## How to use
1. Have your own keypair and upload the public key to EC2 Key Pairs e.g. "SolutionFormationPublicKey"
2. Have your own S3 bucket for installers e.g. "aws-cf-files", and upload the zip files containing coordinator scripts
3. Update the template with proper names for key pairs and bucket name

## Expand the template
1. If the stack is to be deployed across multiple AZs, more subnets should be created accordingly.
2. Further improving this template most likely requires configuring local aws cli environment. You will need an api access user created in your account and ensure that:
- the user has cloudformation full privilege
- the user has the privilege to allow cloudformation template to do what it needs to do.
- if the above is too much to micro-manage, then give the user administrator access, although considered bad security practice.

## Limitations
1. Most logical resource names are hard coded. Therefore you cannot use this template to create two under the same account. More work needed to ensure logical resource names do not overlap.

2. There are manual steps to create and upload public key and prepare the file in S3 bucket. This can be automated as well.

3. There is no load balancer involved in this template, although a flag is created to indicate weather load balancer should be created. More work is needed to configure load balancer and associate it with an autoscaling group.

## Useful Commands
<!--- aws cloudformation validate-template template-body file://SolutionStack.yml -->
```sh
$ aws cloudformation validate-template --template-body file:///home/user/CloudFormation/SolutionStack.yaml
```
```sh
$ aws cloudformation create-stack --stack-name solstack --template-body file:///home/user/CloudFormation/SolutionStack.yaml --capabilities CAPABILITY_NAMED_IAM --disable-rollback --parameters  ParameterKey=EnvironmentSize,ParameterValue=MEDIUM
```
```sh
$ aws cloudformation delete-stack --stack-name solstack
```
```sh
$ aws cloudformation describe-stack-events --stack-name solstack
```
```sh
$ aws cloudformation list-stacks --stack-status-filter CREATE_COMPLETE
```
```sh
$ zip -FSj ~/CloudFormation/cli_inst_coord_script.zip ~/workbench/*.sh && aws s3 cp ~/CloudFormation/cli_inst_coord_script.zip s3://aws-cf-files/cli_inst_coord_script.zip --storage-class STANDARD
```
