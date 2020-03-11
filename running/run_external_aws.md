---
layout: default
title: Using external executors (AWS/HPC)
nav_order: 2
permalink: /run/aws
parent: Executing
has_toc: false
---

## Using external executors
{: .no_toc}

Sometimes you have rules, that you can't execute, because your machine **can't** provide the required resources. With Snakemake you can create a simple export file, which you can then copy to something like a rented **[EC2 instance](https://aws.amazon.com/ec2/)** or **HPC node**.

While Snakemake supports the great [Tibanna project](https://snakemake.readthedocs.io/en/stable/executing/cluster-cloud.html#executing-a-snakemake-workflow-via-tibanna-on-amazon-web-services), the **missing support for** [**directory ouput in remote storage providers**](https://bitbucket.org/snakemake/snakemake/issues/996/directory-doesnt-work-with-default-remote)  (*S3* for example) makes it impossible to use the Knutt pipelines with it at the moment. Tracking all single files of the reference datasets increases Snakemake DAG calculation time significantly, making it almost **unusable**.

---

#### Table of contents
{: .no_toc }

1. TOC
{:toc}

---

### Exporting existing files

You can automatically export the files needed to run a specific rule/produce a specific file which already exist using the following command.

``` sh
snakemake --ri -S RULE_OR_FILE | awk '{if (NR!=1) {print $1}}' | tar -czvf input_files.tar.gz --ignore-failed-read -T -
```

On the other machine you also need the workflow files. You either can just clone the repository with git there and let it download the Conda packages, but you can also export the workflow files and conda packages.

``` sh
snakemake --use-conda RULE_OR_FILE --archive workflow_files.tar.gz
```

You will still need to install snakemake and conda on that machine.

### AWS Account Setup

This section will show you how to setup an AWS account and EC2 instances. First you have to create a [AWS account](https://aws.amazon.com/premiumsupport/knowledge-center/create-and-activate-aws-account/). 

The following steps can also be performed on the AWS webpage, but will be shown using the awscli package. It can be [installed using conda](https://anaconda.org/conda-forge/awscli), your distributions package manager or [manually](https://docs.aws.amazon.com/de_de/cli/latest/userguide/install-cliv2-linux.html). You will also need an [access key id and secret](https://aws.amazon.com/blogs/security/how-to-find-update-access-keys-password-mfa-aws-management-console/). You can also create a [admin IAM user](https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started_create-admin-group.html), this is often recommended. 

Depending on your location and current AWS pricing you want to choose [a default region](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Concepts.RegionsAndAvailabilityZones.html). You can use [this page](https://www.ec2instances.info/) to compare EC2 instance pricing.

Run the [AWS CLI configuration wizard](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html#cli-quick-configuration), set your output format to `json`:

``` sh
aws configure
ls -l ~/.aws
```

This will create the files `~/.aws/config` and `~/.aws/credentials`. They should have the 600 (`-rw-------`) access permissions.

### AWS EC2 SSH Key Setup

You need to have [key pairs](https://docs.aws.amazon.com/de_de/cli/latest/userguide/cli-services-ec2-keypairs.html#deleting-a-key-pair) setup to access your EC2 instances. The following commands lists your EC2 key pairs.

``` sh
aws ec2 describe-key-pairs
```

As an example, we will create a key pair with the name `analysis`.

``` sh
mkdir -p ~/.ssh
aws ec2 create-key-pair --key-name analysis --query 'KeyMaterial' --output text > ~/.ssh/ec2-analysis.pem
chmod 400 ~/.ssh/ec2-analysis.pem
```

The file `~/.ssh/ec2-analysis.pem` is the private key file. Make sure it is protected. You can also delete key pairs.

``` sh
aws ec2 delete-key-pair --key-name analysis
rm ~/.ssh/ec2-analysis.pem
```

### AWS EC2 VPC

EC2 accounts without the "classic" EC2 system need to use [virtual private clouds (VPCs)](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Subnets.html). They are virtual networks for EC2 instances. We will use your default VPC.

When listing your VPCs, there should be a default VPC.

``` sh
aws ec2 describe-vpcs
```

You can create the default VPC for your account with the following command.

``` sh
aws ec2 create-default-vpc
```

Note the `VpcId`.

### AWS EC2 Security Group Setup

[Security groups](https://docs.aws.amazon.com/de_de/cli/latest/userguide/cli-services-ec2-sg.html) are firewall configurations for EC2 instances. You need to have one with SSH access permitted. If you have a fixed IP adress, you want to limit connections to that adress.

List existing groups:

``` sh
aws ec2 describe-security-groups
```

Create a group and add an inbound rule for SSH:

``` sh
aws ec2 create-security-group --group-name public-ssh --description "Public SSH" --vpc-id "vpcid-here"
aws ec2 authorize-security-group-ingress --group-id "id-here" --protocol tcp --port 22 --cidr "ip-here/32"
# If you have a changing IP (not recommended) use 0.0.0.0/0
# 32 limits the subnet to a single ip4 adress
```

Delete a security group:

``` sh
aws ec2 delete-security-group --group-id "id-here"
```

### AWS S3 Bucket Setup

This step is optional. [S3](https://docs.aws.amazon.com/AmazonS3/latest/gsg/GetStartedWithS3.html) is an online file storage comparable to something like Dropbox. While you pay for the used space and operations on S3, uploading files to S3 and then downloading them to the EC2 instances may still be cheaper and faster than copying files to your EC2 instances using `scp`. S3 buckets can also be used to host files on the web and are available without a running EC2 instance, unlike EBS volumes.

Your S3 buckets can be listed with the following command:

``` sh
aws s3 ls
# List bucket contents
aws s3 ls s3://bucket-name/
# Copy a file to a bucket
aws s3 cp filehere s3://bucket-name/filehere
# Delete a file
aws s3 rm s3://bucket-name/file
```

Create a new bucket in your default region.

``` sh
aws s3 mb "s3://unique-test-bucket"
```

Delete a bucket:

``` sh
aws s3 rb "s3://unique-test-bucket"
```

### AWS S3 EC2 IAM Setup

This step is also optional. If you want to access your buckets from inside your EC2 instance you either have to configure `awscl` inside the machine again using your root secret key (not recommended) or create an [IAM (Identity and Access Management)](https://docs.aws.amazon.com/iam/) [instance profile with a role with an access policy](https://docs.aws.amazon.com/de_de/AWSEC2/latest/UserGuide/iam-roles-for-amazon-ec2.html#create-iam-role).

Existing roles and instance profiles can be listed with:

``` sh
aws iam list-roles
aws iam list-instance-profiles
```

First we will create a role for EC2 instances. Save the following as json file (`trust.json`).

``` json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": { "Service": "ec2.amazonaws.com"},
      "Action": "sts:AssumeRole"
    }
  ]
}
```

This command creates the role:

``` sh
aws iam create-role --role-name analysis-ec2 --assume-role-policy-document "file://trust.json"
```

Now you need to specify the policies for this role. Save this as `policy.json`.

``` json
{
  "Version": "2012-10-17",
  "Statement": [
    {
    "Effect": "Allow",
    "Action": "s3:*",
    "Resource": ["arn:aws:s3:::unique-test-bucket",
                 "arn:aws:s3:::unique-test-bucket/*"]
    }
  ]
}
```

Add the policy to the role.

``` sh
aws iam put-role-policy --role-name analysis-ec2 --policy-name s3-perm --policy-document file://policy.json
```

Create an instance profile and add this role.

``` sh
aws iam create-instance-profile --instance-profile-name ec2analysis_profile
aws iam add-role-to-instance-profile --instance-profile-name ec2analysis_profile --role-name analysis-ec2
```

### Start an EC2 instance

After all this configuration, you can start your instances. Select your [instance type](https://www.ec2instances.info/) and [image (AMI)](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/finding-an-ami.html). Usually I use the current Amazon Linux 2 Image:

``` sh
aws ec2 describe-images --owners amazon --filters 'Name=name,Values=amzn2-ami-hvm-2.0.????????.?-x86_64-gp2' 'Name=state,Values=available' --query 'reverse(sort_by(Images, &CreationDate))[:1].ImageId' --output text
# March 2019: ami-0df0e7600ad0913a9
```

You also need the subnet of your VPC, you can list the subnets with the following command:
``` sh
aws ec2 describe-subnets --filters "Name=vpc-id,Values=vpcid-here"
```
You will need the `SubnetId`.

This command spins up a new instance. Usage is billed by the minute. [**Keep an eye on your current balance**](https://docs.aws.amazon.com/de_de/awsaccountbilling/latest/aboutv2/invoice.html). You can list your EBS Volumes and EC2 Instances in the [EC2 online console](https://aws.amazon.com/de/console/).

Replace the values with yours from the previous steps and the 120 with the size of the root device (in GB). You will be charged for the size of the **full EBS device**, independent of your usage! Check your storage usage with `df -h`.

``` sh
aws ec2 run-instances --image-id ami-0df0e7600ad0913a9 --instance-type t2.micro --key-name "analysis" --security-group-ids "sec-id" --block-device-mapping "[ { \"DeviceName\": \"/dev/xvda\", \"Ebs\": { \"VolumeSize\": 120 } } ]" --iam-instance-profile "Name=ec2analysis_profile" 
# Leave out the instance profile if you aren't using one
```

Current instances are listed with:

``` sh
aws ec2 describe-instances
```

When the instance is running, you can connect to it using the following command with its `PublicDnsName` or `PublicDnsName`. This information is only available after the instance has been createda by AWS.

``` sh
ssh -i ~/.ssh/ec2-analysis.pem ec2-user@publicdnshere
```

Now you can install Conda, Snakemake and copy the workflow to it using `scp` or `aws s3 cp` from the configured bucket

Stopping instance shuts the instance down, but doesn't delete it. This means you are only billed for the root EBS (`/`) volume of this instance. Terminating removes the instance. If you run `shutdown` in the instance, it will only stop it. 

``` sh
aws ec2 stop-instances --instance-ids "idhere"
aws ec2 start-instances --instance-ids "idhere"
aws ec2 terminate-instances --instance-ids "idhere"
```