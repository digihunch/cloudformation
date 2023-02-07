# customer template
This helps customer create required s3 bucket and associated roles.

S3
```sh
BUCKET_NAME=dharc$(echo $RANDOM | md5sum | head -c 12)
S3_STACK_NAME=$BUCKET_NAME-stack

# Validate CloudFormation template:
aws cloudformation validate-template --template-body file://aws-s3-stack.yaml

# Create the S3 test stack:
aws cloudformation create-stack --template-body file://aws-s3-stack.yaml --stack-name $S3_STACK_NAME --parameters ParameterKey=S3BucketName,ParameterValue=$BUCKET_NAME --capabilities CAPABILITY_IAM

# print stack status
aws cloudformation describe-stacks --stack-name $S3_STACK_NAME --query "Stacks[0].StackStatus" --output text

# if stack status is CREATE_COMPLETE, print the outputs
aws cloudformation describe-stacks --stack-name $S3_STACK_NAME --query "Stacks[0].Outputs" --output table

# When test is completed, empty the bucket and delete the stack:
aws cloudformation delete-stack --stack-name $S3_STACK_NAME
```


Azure
```sh
az deployment group validate -g AutomationTest -f ./azure-blob.bicep
az deployment group what-if -g AutomationTest -f ./azure-blob.bicep

az deployment group create -g AutomationTest -f ./azure-blob.bicep
az deployment group delete -g AutomationTest -n ./azure-blob.bicep   # this does not delete the actual resources.
```
