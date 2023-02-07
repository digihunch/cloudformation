# VPC Baseline

Just a CloudFormation stack to quickly spin up a VPC for my experiment. The VPC has two subnets, a public subnet and a private subnet. 

The NAT instance is placed in the public subnet. Apart from its own role, it also serves as a Bastion host. The Bastion Host will add pre-uploaded public key specified in the template for SSH access. In the meantime, a new RSA key pair is generated dynamically during the boot process and the public key is automatically added to other instances. Once the cloudformation stack is created, it will be able to SSH to any server from jumpbox with RSA key authorization.

Here are the VM instances involved:

---------------------------------------------------------------------------------------------------------------
| ResourceName    | Platform | Subnet  |  LaunchTemplate            | Typical Server Role      |
| --------------- | -------- | ------- | -------------------------- | ------------------------ |
| NATInstance     | Linux    | Public  | N/A                        | Jumpbox and NAT Instance |
| KNodeWebInst    | Linux2   | Public  | PublicNodeLaunchTemplate  | Frontend Web Server      |
| KNodeBkndInst   | Linux2   | Private | PrivateNodeLaunchTemplate | Backend App Server       |
---------------------------------------------------------------------------------------------------------------

Amazon Linux distribution is built to be compatible with RedHat/CentOS. All commands in this lab work with CentOS 7 and RedHat 7 platforms. 

## Prerequisite for KFormation.yml
AWSCLI should be configured in local SSH client (access key, secret key, region specified). In addition, the CloudFormation template requires
- PubKeyName to specify the public key stored in the AWS account
- InstanceType (default is t2.micro)

## Common commands 
- Upload public key from my MacBook
```sh
aws ec2 import-key-pair --key-name cskey --public-key-material "$(base64 < ~/.ssh/id_rsa.pub)"
```

- Build a stack with mykcluster name 
```sh
aws cloudformation create-stack --stack-name mykcluster --capabilities CAPABILITY_NAMED_IAM --template-body file://VPCBaseline.yaml
```
- Describe stack
```sh
aws cloudformation describe-stacks --stack-name mykcluster 
aws cloudformation describe-stacks --stack-name mykcluster | jq ".Stacks[].Outputs[]"
```
The describe-stacks command returns hostname of the newly created instances.

- Delete the stack created
```sh
aws cloudformation delete-stack --stack-name mykcluster
```
- List the stacks
```sh
aws cloudformation list-stacks --stack-status-filter DELETE_IN_PROGRESS CREATE_IN_PROGRESS CREATE_FAILED CREATE_COMPLETE
```
A keypar is created as part of stack creation for communication between bastion host and other servers. This key should be manually deleted after the lab. The key name is provided in stack output.
```sh
aws ec2 delete-key-pair --key-name MyKeyPair
```