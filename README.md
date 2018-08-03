# VM-MIGRATION-TO-AWS
vm to aws migration 


https://docs.aws.amazon.com/vm-import/latest/userguide/vmimport-image-import.html

1.generating OVA file form  vm

2.then upload file to s3 bucket
3.crate the iam role with the following command


to check the status of the vm export
aws ec2 describe-import-image-tasks --import-task-ids import-ami-abcd1234

ami-id
shows like pending 
converting
completed


1.create  trust-policy.json 

2.aws iam create-role --role-name vmimport --assume-role-policy-document file://trust-policy.json

3. create role-policy.json

aws iam put-role-policy --role-name vmimport --policy-name vmimport --policy-document file://role-policy.json

aws ec2 import-image --description "Windows 2008 OVA"--disk-containers file://containers.json

aws ec2 describe-import-image-tasks --import-task-ids import-ami-abcd1234

aws ec2 cancel-import-task --import-task-id import-ami-abcd1234




containers.json
[
  {
    "Description": "Windows 2008 OVA",
    "Format": "ova",
    "UserBucket": {
        "S3Bucket": "my-import-bucket",
        "S3Key": "vms/my-windows-2008-vm.ova"
    }
}]





trust polcy
{
   "Version": "2012-10-17",
   "Statement": [
      {
         "Effect": "Allow",
         "Principal": { "Service": "vmie.amazonaws.com" },
         "Action": "sts:AssumeRole",
         "Condition": {
            "StringEquals":{
               "sts:Externalid": "vmimport"
            }
         }
      }
   ]
}

 role-policy.json


{
   "Version":"2012-10-17",
   "Statement":[
      {
         "Effect":"Allow",
         "Action":[
            "s3:GetBucketLocation",
            "s3:GetObject",
            "s3:ListBucket" 
         ],
         "Resource":[
            "arn:aws:s3:::disk-image-file-bucket",
            "arn:aws:s3:::disk-image-file-bucket/*"
         ]
      },
      {
         "Effect":"Allow",
         "Action":[
            "ec2:ModifySnapshotAttribute",
            "ec2:CopySnapshot",
            "ec2:RegisterImage",
            "ec2:Describe*"
         ],
         "Resource":"*"
      }
   ]
}
