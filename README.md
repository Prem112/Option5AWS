# Introduction

AWS Infrastructure as a code



# Deployment in GIt

1. Building infrastructure

  Environment: Windows 10 / Linux (Ubuntu 16.04)
  	1. Create GitHub account with a valid email address, link:https://github.com/
  	2. Create git a new repository, link: https://github.com/Prem112/Option5AWS
  	3. Install the git, link: https://git-scm.com/download/win
  	4. Open Git bash and configure it
  		1. git config --global user.name "Your Name"
          2. git config --global user.email "youremail@domain.com"
  	5. Clone the git repository code "git clone https://github.com/Prem112/Option6Git.git"

  ## Setup AWS

  Please follow link below for AWS CLI setup.

  https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2-windows.html

  1. Config AWS

     1. Type "aws configure"
     2. Type "AWS Access Key ID" 
     3. Type "AWS Secret Access Key"
     4. Type "ap-southeast-1"

     ```
     C:\WINDOWS\system32>aws configure
     AWS Access Key ID : [****************JV5S]
     AWS Secret Access Key : [****************iB3n]
     Default region name : ap-southeast-1
     Default output format [None]:
     ```

## Install Boto3

- Boto3: Boto3 can be installed using pip: `pip install boto3`

  

# Prerequisites for Python

This file can run on both Windows and Linux operating system.

Install Python3.9.6 and git clone this repo.

Running main.py from the command line and start to deploy the infrastructure.



## main.py 

Download::

``` 
git clone https://github.com/Prem112/Option5AWS.git
```



# Getting Started



Create Admin User using AWS GUI.

## Create AWS KMS Key 

Type `aws kms create-key --description "This is my key for test-devops-kms-key-dev-eu-west-1"` to create user.

> {
>     "KeyMetadata": {
>         "AWSAccountId": "386017078612",
>         "KeyId": "8e8fbda3-c1ee-40de-9f0c-3b1b4850d547",
>         "Arn": "arn:aws:kms:ap-southeast-1:386017078612:key/8e8fbda3-c1ee-40de-9f0c-3b1b4850d547",
>         "CreationDate": "2021-07-19T17:37:00.941000+08:00",
>         "Enabled": true,
>         "Description": "This is my key for test-devops-kms-key-dev-eu-west-1",
>         "KeyUsage": "ENCRYPT_DECRYPT",
>         "KeyState": "Enabled",
>         "Origin": "AWS_KMS",
>         "KeyManager": "CUSTOMER",
>         "CustomerMasterKeySpec": "SYMMETRIC_DEFAULT",
>         "EncryptionAlgorithms": [
>             "SYMMETRIC_DEFAULT"
>         ],
>         "MultiRegion": false
>     }
> }

Type `aws kms create-alias --alias-name alias/test-devops-kms-key-dev-eu-west-1 --target-key-id 8e8fbda3-c1ee-40de-9f0c-3b1b4850d547` to create access key

>{
>            "AliasName": "alias/test-devops-kms-key-dev-eu-west-1",
>            "AliasArn": "arn:aws:kms:ap-southeast-1:386017078612:alias/test-devops-kms-key-dev-eu-west-1",
>            "TargetKeyId": "8e8fbda3-c1ee-40de-9f0c-3b1b4850d547",
>            "CreationDate": "2021-07-19T17:41:01.035000+08:00",
>            "LastUpdatedDate": "2021-07-19T17:41:01.035000+08:00"
>        }



## Create dynamodb table



Create Dynamodb Table, Run `python ./Create_Dynamodb.py`

```
import boto3

# boto3 is the AWS SDK library for Python.
# We can use the low-level client to make API calls to DynamoDB.
client = boto3.client('dynamodb', region_name='ap-southeast-1')

try:
    resp = client.create_table(
        TableName="test-devops-dynamodb-dev-eu-west-1",
        # Declare your Primary Key in the KeySchema argument
        KeySchema=[
            {
                "AttributeName": "Author",
                "KeyType": "HASH"
            },
            {
                "AttributeName": "Title",
                "KeyType": "RANGE"
            }
        ],
        # Any attributes used in KeySchema or Indexes must be declared in AttributeDefinitions
        AttributeDefinitions=[
            {
                "AttributeName": "Author",
                "AttributeType": "S"
            },
            {
                "AttributeName": "Title",
                "AttributeType": "S"
            }
        ],
        # ProvisionedThroughput controls the amount of data you can read or write to DynamoDB per second.
        # You can control read and write capacity independently.
        ProvisionedThroughput={
            "ReadCapacityUnits": 1,
            "WriteCapacityUnits": 1
        }
    )
    print("Table created successfully!")
except Exception as e:
    print("Error creating table:")
    print(e)

```

GetItem from the table, Run `python ./Get_Item_Dynamodb.py`

```
import boto3

dynamodb = boto3.resource('dynamodb', region_name='ap-southeast-1')
table = dynamodb.Table('test-devops-dynamodb-dev-eu-west-1')

resp = table.get_item(Key={"Author": "John Grisham", "Title": "The Rainmaker"})

print(resp['Item'])
```

PutItem from the table, Run `python ./Insert_Item_DynamoDB.py`

```
import boto3

dynamodb = boto3.resource('dynamodb', region_name='ap-southeast-1')
table = dynamodb.Table('test-devops-dynamodb-dev-eu-west-1')

with table.batch_writer() as batch:
    batch.put_item(Item={"Author": "John Grisham", "Title": "The Rainmaker",
        "Category": "Suspense", "Formats": { "Hardcover": "J4SUKVGU", "Paperback": "D7YF4FCX" } })
    batch.put_item(Item={"Author": "John Grisham", "Title": "The Firm",
        "Category": "Suspense", "Formats": { "Hardcover": "Q7QWE3U2",
        "Paperback": "ZVZAYY4F", "Audiobook": "DJ9KS9NM" } })
    batch.put_item(Item={"Author": "James Patterson", "Title": "Along Came a Spider",
        "Category": "Suspense", "Formats": { "Hardcover": "C9NR6RJ7",
        "Paperback": "37JVGDZG", "Audiobook": "6348WX3U" } })
    batch.put_item(Item={"Author": "Dr. Seuss", "Title": "Green Eggs and Ham",
        "Category": "Children", "Formats": { "Hardcover": "GVJZQ7JK",
        "Paperback": "A4TFUR98", "Audiobook": "XWMGHW96" } })
    batch.put_item(Item={"Author": "William Shakespeare", "Title": "Hamlet",
        "Category": "Drama", "Formats": { "Hardcover": "GVJZQ7JK",
        "Paperback": "A4TFUR98", "Audiobook": "XWMGHW96" } })

```



GetRecords from the table `Get_Record_DynamoDB.Py`

```
import boto3
from boto3.dynamodb.conditions import Key

# boto3 is the AWS SDK library for Python.
dynamodb = boto3.resource('dynamodb', region_name='ap-southeast-1')
table = dynamodb.Table('test-devops-dynamodb-dev-eu-west-1')

# When making a Query API call, we use the KeyConditionExpression parameter to specify the hash key on which we want to query.
# We're using the Key object from the Boto3 library to specify that we want the attribute name ("Author")
# to equal "John Grisham" by using the ".eq()" method.
resp = table.query(KeyConditionExpression=Key('Author').eq('John Grisham'))

print("The query returned the following items:")
for item in resp['Items']:
    print(item)

```



## Create EC2 instance

***Check for Key pairs.***

```
C:\Users\PremStarZ>aws ec2 describe-key-pairs
{
    "KeyPairs": []
}

```

***Create new Key pairs.***

```
C:\Users\PremStarZ>aws ec2 create-key-pair --key-name 'test-devops-ec2-dev-eu-west-1'
{
    "KeyFingerprint": "c2:d3:1b:19:22:ff:b5:f6:cf:91:28:2c:1b:1c:47:ea:43:23:69:1d",
    "KeyMaterial": "-----BEGIN RSA PRIVATE KEY-----\nMIIEowIBAAKCAQEApkIcMRtu25UHwt9HmJqW1+MssoanXwLzF1d42u74NUKNIb9+\nbiVw4IWIFFurmz3UCapBJqRBa/w3SKhW31LVg2BFCExbmTpP3CUzMHodcRjOYuhy\n4Lc5cojzunyB64sVk4mILnhCa1tqnLdwme/gtpBLtkON0ufQpwKPdzyW2Lv85JAz\nYEj3U5I5y/mLhizNDastj04cwr/moV6/tMlJ499NF4nOWo5y+Qwc2za9Q9EjMN9F\nnqwuvNqRdg9k1sq7Npd6Gz9El8LuEy3KRDOhF/tSp6/8IpKM5FCcWjajBbcUtimH\nbcdHEEKUB3afhQPb3MIYwsYPxB3qMARy0QnoGQIDAQABAoIBAEFVlXKOzz+nDwe7\nao1Doqdv9K6rT7Z8mD7B9xJB+nQjlQaAFBUAzZzNnK4zX/p/j4XEzBl9TuA6mxcI\nYCseiB06haY/K3fJfHyMedNBCbTaBLDFm+9G0WJ7AxxztTJ006PI1iU8yQ0bPYgc\nZjl4FJxpk/cqEN+ijVI0JWC8BKNih7qipagk5yYouki7uxzECXz5uJdadGcFDyAz\npZw1xTYSeajHSs8xyxCjdXUDgU8a0kLVW+zqPIBUisNq87boV2yfS4TzuSay7dOd\nDDWz/+fCakSLXCTlQXylSqGsbEArp2RUS2upAU0xnvuXIOmtxrT3EBWOQTMF+SJj\nkmD6GikCgYEA9tCdabTRk3xtFF64TcSvrfNWVnbt7Y14Sht9OFu5qt8qz04UFrPP\n2mlBYgW2jkz5oRa/DKbiOSo1uZunxR4CkS3N/mZG733jsVRVtCRNwQYbsQ/ix/E3\nEIynGaD25eUbgJZYCS5AzY/e3uHql+Ls0xALK9t+3Nz3rklUZiNLhxMCgYEArHIJ\n4spvOebublBPD0U+ooWscAZGLupGvDg9hGgyOAUBoUfo3mnOGZ75r7dVI6BXRBk3\nE9QfMT+Ij/dLOTMvXC7Z0tIF2Z6h8NYx41KcXIYIFmurt+r3gNsz/B+pkBy7ZECf\nxlW/UrFnyAVhH3Y1ApwHnAstw6jK8Qw8GW+7XaMCgYBpR5VNyL54zeNtg/XT3mkd\npyWV3kchRG4iFlW0m5O4KleFdAWnpW2s+abVHCDB1F3K8/vsdqcawUGHebj3oRRx\nPvuWX6Q0dhuQwauJGs873dIkFeWjaeYWHhkNGpcWe0Le98J3sA4eYRctNWqu/TIN\n2dMaExerOGpCIq9onfeSlwKBgGaQcSbNZjgXDOrZoxwfADDjtnrukGGo/6dE//m3\ni17cQ2rsfSmD3oxIjJMhRGcrH5wmLycA/AhrlEqkN75unhWC3BVSyx3zBrhafVOK\nN+uj4D2NjpWWD905AwNKxawsGpx2/1CUgXWqhGJoKKrwwHir2q7Jy09bHlEeCMTM\nOrFnAoGBAMr5zog9xwLcjkdoTUd02WVDHj0eUA6BQEEW0PucFjJj+ZFnaUZ6nvrF\ntkHUUXOzkFN1tnj0vm1QwF9+v5yS2OmXOXM57LQG9HHMGQd0Ca266HfFAJblRa/a\n2Jc3iRlaexbObI4aYr3DtUK80DLkAqFWd3V61m0itOJco9AKXK25\n-----END RSA PRIVATE KEY-----",
    "KeyName": "'test-devops-ec2-dev-eu-west-1'",
    "KeyPairId": "key-0755602fd0e5d21f7"
}
```



***Check for Vpcs.***

```
C:\Users\PremStarZ>aws ec2 describe-vpcs
{
    "Vpcs": [
        {
            "CidrBlock": "172.31.0.0/16",
            "DhcpOptionsId": "dopt-d2ba77b4",
            "State": "available",
            "VpcId": "vpc-69915b0f",
            "OwnerId": "386017078612",
            "InstanceTenancy": "default",
            "CidrBlockAssociationSet": [
                {
                    "AssociationId": "vpc-cidr-assoc-da6c0db1",
                    "CidrBlock": "172.31.0.0/16",
                    "CidrBlockState": {
                        "State": "associated"
                    }
                }
            ],
            "IsDefault": true
        }
    ]
}
```

***Create new Security group.***

```
C:\Users\PremStarZ>aws ec2 create-security-group --group-name test-devops-ec2-dev-eu-west-1 --description "test-devops-ec2-dev-eu-west-1 security group" --vpc-id vpc-69915b0f
{
    "GroupId": "sg-06130b1739b3581a0"
}
```

```
{
    "SecurityGroups": [
        {
            "Description": "test-devops-ec2-dev-eu-west-1 security group",
            "GroupName": "test-devops-ec2-dev-eu-west-1",
            "IpPermissions": [],
            "OwnerId": "386017078612",
            "GroupId": "sg-06130b1739b3581a0",
            "IpPermissionsEgress": [
                {
                    "IpProtocol": "-1",
                    "IpRanges": [
                        {
                            "CidrIp": "0.0.0.0/0"
                        }
                    ],
                    "Ipv6Ranges": [],
                    "PrefixListIds": [],
                    "UserIdGroupPairs": []
                }
            ],
            "VpcId": "vpc-69915b0f"
        },
        {
            "Description": "Security group for AWS Cloud9 environment aws-cloud9-test-devops-role-dev-eu-west-1-93e9a58876d249919fbe53ed7ce9301f",
            "GroupName": "aws-cloud9-test-devops-role-dev-eu-west-1-93e9a58876d249919fbe53ed7ce9301f-InstanceSecurityGroup-IUX5ZK8GAMKY",
            "IpPermissions": [
                {
                    "FromPort": 22,
                    "IpProtocol": "tcp",
                    "IpRanges": [
                        {
                            "CidrIp": "13.250.186.128/27"
                        },
                        {
                            "CidrIp": "13.250.186.160/27"
                        }
                    ],
                    "Ipv6Ranges": [],
                    "PrefixListIds": [],
                    "ToPort": 22,
                    "UserIdGroupPairs": []
                }
            ],
            "OwnerId": "386017078612",
            "GroupId": "sg-0a2066c4df945e284",
            "IpPermissionsEgress": [
                {
                    "IpProtocol": "-1",
                    "IpRanges": [
                        {
                            "CidrIp": "0.0.0.0/0"
                        }
                    ],
                    "Ipv6Ranges": [],
                    "PrefixListIds": [],
                    "UserIdGroupPairs": []
                }
            ],
            "Tags": [
                {
                    "Key": "aws:cloud9:environment",
                    "Value": "93e9a58876d249919fbe53ed7ce9301f"
                },
                {
                    "Key": "aws:cloud9:owner",
                    "Value": "386017078612"
                },
                {
                    "Key": "aws:cloudformation:logical-id",
                    "Value": "InstanceSecurityGroup"
                },
                {
                    "Key": "aws:cloudformation:stack-name",
                    "Value": "aws-cloud9-test-devops-role-dev-eu-west-1-93e9a58876d249919fbe53ed7ce9301f"
                },
                {
                    "Key": "Name",
                    "Value": "aws-cloud9-test-devops-role-dev-eu-west-1-93e9a58876d249919fbe53ed7ce9301f"
                },
                {
                    "Key": "aws:cloudformation:stack-id",
                    "Value": "arn:aws:cloudformation:ap-southeast-1:386017078612:stack/aws-cloud9-test-devops-role-dev-eu-west-1-93e9a58876d249919fbe53ed7ce9301f/f0ea9670-e739-11eb-80ab-06d6e8ae7814"
                }
            ],
            "VpcId": "vpc-69915b0f"
        },
        {
            "Description": "default VPC security group",
            "GroupName": "default",
            "IpPermissions": [
                {
                    "IpProtocol": "-1",
                    "IpRanges": [],
                    "Ipv6Ranges": [],
                    "PrefixListIds": [],
                    "UserIdGroupPairs": [
                        {
                            "GroupId": "sg-5df41612",
                            "UserId": "386017078612"
                        }
                    ]
                }
            ],
            "OwnerId": "386017078612",
            "GroupId": "sg-5df41612",
            "IpPermissionsEgress": [
                {
                    "IpProtocol": "-1",
                    "IpRanges": [
                        {
                            "CidrIp": "0.0.0.0/0"
                        }
                    ],
                    "Ipv6Ranges": [],
                    "PrefixListIds": [],
                    "UserIdGroupPairs": []
                }
            ],
            "VpcId": "vpc-69915b0f"
        }
    ]
}
```

***Create Inbound.***

```
C:\Users\PremStarZ>aws ec2 authorize-security-group-ingress --group-id sg-06130b1739b3581a0 --protocol tcp --port 22 --cidr 180.129.43.55/32
{
    "Return": true,
    "SecurityGroupRules": [
        {
            "SecurityGroupRuleId": "sgr-0960f9b0de2a0badf",
            "GroupId": "sg-06130b1739b3581a0",
            "GroupOwnerId": "386017078612",
            "IsEgress": false,
            "IpProtocol": "tcp",
            "FromPort": 22,
            "ToPort": 22,
            "CidrIpv4": "180.129.43.55/32"
        }
    ]
}
```



```
C:\Users\PremStarZ>aws ec2 describe-security-groups
{
    "SecurityGroups": [
        {
            "Description": "test-devops-ec2-dev-eu-west-1 security group",
            "GroupName": "test-devops-ec2-dev-eu-west-1",
            "IpPermissions": [
                {
                    "FromPort": 22,
                    "IpProtocol": "tcp",
                    "IpRanges": [
                        {
                            "CidrIp": "180.129.43.55/32"
                        }
                    ],
                    "Ipv6Ranges": [],
                    "PrefixListIds": [],
                    "ToPort": 22,
                    "UserIdGroupPairs": []
                }
            ],
            "OwnerId": "386017078612",
            "GroupId": "sg-06130b1739b3581a0",
            "IpPermissionsEgress": [
                {
                    "IpProtocol": "-1",
                    "IpRanges": [
                        {
                            "CidrIp": "0.0.0.0/0"
                        }
                    ],
                    "Ipv6Ranges": [],
                    "PrefixListIds": [],
                    "UserIdGroupPairs": []
                }
            ],
            "VpcId": "vpc-69915b0f"
        },
        {
            "Description": "Security group for AWS Cloud9 environment aws-cloud9-test-devops-role-dev-eu-west-1-93e9a58876d249919fbe53ed7ce9301f",
            "GroupName": "aws-cloud9-test-devops-role-dev-eu-west-1-93e9a58876d249919fbe53ed7ce9301f-InstanceSecurityGroup-IUX5ZK8GAMKY",
            "IpPermissions": [
                {
                    "FromPort": 22,
                    "IpProtocol": "tcp",
                    "IpRanges": [
                        {
                            "CidrIp": "13.250.186.128/27"
                        },
                        {
                            "CidrIp": "13.250.186.160/27"
                        }
                    ],
                    "Ipv6Ranges": [],
                    "PrefixListIds": [],
                    "ToPort": 22,
                    "UserIdGroupPairs": []
                }
            ],
            "OwnerId": "386017078612",
            "GroupId": "sg-0a2066c4df945e284",
            "IpPermissionsEgress": [
                {
                    "IpProtocol": "-1",
                    "IpRanges": [
                        {
                            "CidrIp": "0.0.0.0/0"
                        }
                    ],
                    "Ipv6Ranges": [],
                    "PrefixListIds": [],
                    "UserIdGroupPairs": []
                }
            ],
            "Tags": [
                {
                    "Key": "aws:cloud9:environment",
                    "Value": "93e9a58876d249919fbe53ed7ce9301f"
                },
                {
                    "Key": "aws:cloud9:owner",
                    "Value": "386017078612"
                },
                {
                    "Key": "aws:cloudformation:logical-id",
                    "Value": "InstanceSecurityGroup"
                },
                {
                    "Key": "aws:cloudformation:stack-name",
                    "Value": "aws-cloud9-test-devops-role-dev-eu-west-1-93e9a58876d249919fbe53ed7ce9301f"
                },
                {
                    "Key": "Name",
                    "Value": "aws-cloud9-test-devops-role-dev-eu-west-1-93e9a58876d249919fbe53ed7ce9301f"
                },
                {
                    "Key": "aws:cloudformation:stack-id",
                    "Value": "arn:aws:cloudformation:ap-southeast-1:386017078612:stack/aws-cloud9-test-devops-role-dev-eu-west-1-93e9a58876d249919fbe53ed7ce9301f/f0ea9670-e739-11eb-80ab-06d6e8ae7814"
                }
            ],
            "VpcId": "vpc-69915b0f"
        },
        {
            "Description": "default VPC security group",
            "GroupName": "default",
            "IpPermissions": [
                {
                    "IpProtocol": "-1",
                    "IpRanges": [],
                    "Ipv6Ranges": [],
                    "PrefixListIds": [],
                    "UserIdGroupPairs": [
                        {
                            "GroupId": "sg-5df41612",
                            "UserId": "386017078612"
                        }
                    ]
                }
            ],
            "OwnerId": "386017078612",
            "GroupId": "sg-5df41612",
            "IpPermissionsEgress": [
                {
                    "IpProtocol": "-1",
                    "IpRanges": [
                        {
                            "CidrIp": "0.0.0.0/0"
                        }
                    ],
                    "Ipv6Ranges": [],
                    "PrefixListIds": [],
                    "UserIdGroupPairs": []
                }
            ],
            "VpcId": "vpc-69915b0f"
        }
    ]
}
```



***Check for Subnets***

```
C:\Users\PremStarZ>aws ec2 authorize-security-group-ingress --group-id sg-06130b1739b3581a0 --protocol tcp --port 22 --cidr 180.129.43.55/32
{
    "Return": true,
    "SecurityGroupRules": [
        {
            "SecurityGroupRuleId": "sgr-0960f9b0de2a0badf",
            "GroupId": "sg-06130b1739b3581a0",
            "GroupOwnerId": "386017078612",
            "IsEgress": false,
            "IpProtocol": "tcp",
            "FromPort": 22,
            "ToPort": 22,
            "CidrIpv4": "180.129.43.55/32"
        }
    ]
}
```

```
C:\Users\PremStarZ>aws ec2 describe-subnets
{
    "Subnets": [
        {
            "AvailabilityZone": "ap-southeast-1c",
            "AvailabilityZoneId": "apse1-az3",
            "AvailableIpAddressCount": 4090,
            "CidrBlock": "172.31.0.0/20",
            "DefaultForAz": true,
            "MapPublicIpOnLaunch": true,
            "MapCustomerOwnedIpOnLaunch": false,
            "State": "available",
            "SubnetId": "subnet-aa7d17f3",
            "VpcId": "vpc-69915b0f",
            "OwnerId": "386017078612",
            "AssignIpv6AddressOnCreation": false,
            "Ipv6CidrBlockAssociationSet": [],
            "SubnetArn": "arn:aws:ec2:ap-southeast-1:386017078612:subnet/subnet-aa7d17f3"
        },
        {
            "AvailabilityZone": "ap-southeast-1a",
            "AvailabilityZoneId": "apse1-az1",
            "AvailableIpAddressCount": 4091,
            "CidrBlock": "172.31.16.0/20",
            "DefaultForAz": true,
            "MapPublicIpOnLaunch": true,
            "MapCustomerOwnedIpOnLaunch": false,
            "State": "available",
            "SubnetId": "subnet-8f6ed1e9",
            "VpcId": "vpc-69915b0f",
            "OwnerId": "386017078612",
            "AssignIpv6AddressOnCreation": false,
            "Ipv6CidrBlockAssociationSet": [],
            "SubnetArn": "arn:aws:ec2:ap-southeast-1:386017078612:subnet/subnet-8f6ed1e9"
        },
        {
            "AvailabilityZone": "ap-southeast-1b",
            "AvailabilityZoneId": "apse1-az2",
            "AvailableIpAddressCount": 4091,
            "CidrBlock": "172.31.32.0/20",
            "DefaultForAz": true,
            "MapPublicIpOnLaunch": true,
            "MapCustomerOwnedIpOnLaunch": false,
            "State": "available",
            "SubnetId": "subnet-4fb61907",
            "VpcId": "vpc-69915b0f",
            "OwnerId": "386017078612",
            "AssignIpv6AddressOnCreation": false,
            "Ipv6CidrBlockAssociationSet": [],
            "SubnetArn": "arn:aws:ec2:ap-southeast-1:386017078612:subnet/subnet-4fb61907"
        }
    ]
}
```



***Create Instances***

```
aws ec2 run-instances --image-id ami-0e5182fad1edfaa68 --count 1 --instance-type t2.micro --key-name 'test-devops-ec2-dev-eu-west-1' --security-group-ids sg-06130b1739b3581a0 --subnet-id subnet-4fb61907
```

```
C:\Users\PremStarZ>aws ec2 run-instances --image-id ami-0e5182fad1edfaa68 --count 1 --instance-type t2.micro --key-name 'test-devops-ec2-dev-eu-west-1' --security-group-ids sg-06130b1739b3581a0 --subnet-id subnet-4fb61907
{
    "Groups": [],
    "Instances": [
        {
            "AmiLaunchIndex": 0,
            "ImageId": "ami-0e5182fad1edfaa68",
            "InstanceId": "i-0cba5b99e311742d6",
            "InstanceType": "t2.micro",
            "KeyName": "'test-devops-ec2-dev-eu-west-1'",
            "LaunchTime": "2021-07-19T11:31:57+00:00",
            "Monitoring": {
                "State": "disabled"
            },
            "Placement": {
                "AvailabilityZone": "ap-southeast-1b",
                "GroupName": "",
                "Tenancy": "default"
            },
            "PrivateDnsName": "ip-172-31-45-66.ap-southeast-1.compute.internal",
            "PrivateIpAddress": "172.31.45.66",
            "ProductCodes": [],
            "PublicDnsName": "",
            "State": {
                "Code": 0,
                "Name": "pending"
            },
            "StateTransitionReason": "",
            "SubnetId": "subnet-4fb61907",
            "VpcId": "vpc-69915b0f",
            "Architecture": "x86_64",
            "BlockDeviceMappings": [],
            "ClientToken": "84ba4270-f38e-452a-bb2f-16db44e39b58",
            "EbsOptimized": false,
            "EnaSupport": true,
            "Hypervisor": "xen",
            "NetworkInterfaces": [
                {
                    "Attachment": {
                        "AttachTime": "2021-07-19T11:31:57+00:00",
                        "AttachmentId": "eni-attach-0ca625cacfd60d0e3",
                        "DeleteOnTermination": true,
                        "DeviceIndex": 0,
                        "Status": "attaching",
                        "NetworkCardIndex": 0
                    },
                    "Description": "",
                    "Groups": [
                        {
                            "GroupName": "test-devops-ec2-dev-eu-west-1",
                            "GroupId": "sg-06130b1739b3581a0"
                        }
                    ],
                    "Ipv6Addresses": [],
                    "MacAddress": "06:0b:08:c4:cc:90",
                    "NetworkInterfaceId": "eni-0688439b283b9d9da",
                    "OwnerId": "386017078612",
                    "PrivateDnsName": "ip-172-31-45-66.ap-southeast-1.compute.internal",
                    "PrivateIpAddress": "172.31.45.66",
                    "PrivateIpAddresses": [
                        {
                            "Primary": true,
                            "PrivateDnsName": "ip-172-31-45-66.ap-southeast-1.compute.internal",
                            "PrivateIpAddress": "172.31.45.66"
                        }
                    ],
                    "SourceDestCheck": true,
                    "Status": "in-use",
                    "SubnetId": "subnet-4fb61907",
                    "VpcId": "vpc-69915b0f",
                    "InterfaceType": "interface"
                }
            ],
            "RootDeviceName": "/dev/xvda",
            "RootDeviceType": "ebs",
            "SecurityGroups": [
                {
                    "GroupName": "test-devops-ec2-dev-eu-west-1",
                    "GroupId": "sg-06130b1739b3581a0"
                }
            ],
            "SourceDestCheck": true,
            "StateReason": {
                "Code": "pending",
                "Message": "pending"
            },
            "VirtualizationType": "hvm",
            "CpuOptions": {
                "CoreCount": 1,
                "ThreadsPerCore": 1
            },
            "CapacityReservationSpecification": {
                "CapacityReservationPreference": "open"
            },
            "MetadataOptions": {
                "State": "pending",
                "HttpTokens": "optional",
                "HttpPutResponseHopLimit": 1,
                "HttpEndpoint": "enabled"
            },
            "EnclaveOptions": {
                "Enabled": false
            }
        }
    ],
    "OwnerId": "386017078612",
    "ReservationId": "r-0cefbf2a81cc23f5f"
}
```



***Create Tag with Name***

```
aws ec2 create-tags --resources i-0cba5b99e311742d6 --tags Key=Name,Value=test-devops-ec2-dev-eu-west-1
```



*** Successful Attachment of role to EC2 instance**

```
C:\Users\PremStarZ>aws s3 ls
2021-07-17 20:40:10 test-devops-role-dev-eu-west-1
```



## Create IAM Role

Type `aws iam create-user --user-name test-devops-User-dev-eu-west-1` to create user.

> {
>     "User": {
>         "Path": "/",
>         "UserName": "test-devops-User-dev-eu-west-1",
>         "UserId": "AIDAXXXXXXXXXXXXXXX",
>         "Arn": "arn:aws:iam::386017078612:user/test-devops-User-dev-eu-west-1",
>         "CreateDate": "2021-07-19T08:33:29+00:00"
>     }
> }

Type `aws iam create-access-key --user-name test-devops-User-dev-eu-west-1` to create access key

>{
>    "AccessKey": {
>        "UserName": "test-devops-User-dev-eu-west-1",
>        "AccessKeyId": "AIDAXXXXXXXXXXXXXXX",
>        "Status": "Active",
>        "SecretAccessKey": "hAVUfXa3uxxxxxxxxxxxxxxxxxxxxxxxxxx",
>        "CreateDate": "2021-07-19T08:34:31+00:00"
>    }
>}

Type `aws iam create-group --group-name Developers` Groups for Developers.

>{
>    "Group": {
>        "Path": "/",
>        "GroupName": "Developers",
>        "GroupId": "AGPAVTYDJFVKC2NUGPNHW",
>        "Arn": "arn:aws:iam::386017078612:group/Developers",
>        "CreateDate": "2021-07-19T08:44:12+00:00"
>    }
>}
>
>

Type `aws iam add-user-to-group --user-name test-devops-User-dev-eu-west-1 --group-name Developers` to add the user `test-devops-User-dev-eu-west-1` to groups `Developers`.

>aws iam add-user-to-group --user-name test-devops-User-dev-eu-west-1 --group-name Developers



Assign group policy `AmazonEC2FullAccess `to Developers group.

>aws iam attach-group-policy --policy-arn arn:aws:iam::aws:policy/AmazonEC2FullAccess --group-name Developers
>
>

Type `aws iam list-attached-group-policies --group-name Developers` to list the policy.

>{
>    "AttachedPolicies": [
>        {
>            "PolicyName": "AmazonEC2FullAccess",
>            "PolicyArn": "arn:aws:iam::aws:policy/AmazonEC2FullAccess"
>        }
>    ]
>}



Attach role policy from a policy file (.json). Type `aws iam create-role --role-name test-devops-role-dev-eu-west-1 --assume-role-policy-document file://d:/AWS/Option5AWS/Role/test-devops-role-dev-eu-west-1-trust-policy.json`

>{
>            "Path": "/",
>            "RoleName": "test-devops-role-dev-eu-west-1",
>            "RoleId": "AROAVTYDJFVKEQQGX7WYH",
>            "Arn": "arn:aws:iam::386017078612:role/test-devops-role-dev-eu-west-1",
>            "CreateDate": "2021-07-19T09:17:40+00:00",
>            "AssumeRolePolicyDocument": {
>                "Version": "2012-10-17",
>                "Statement": [
>                    {
>                        "Effect": "Allow",
>                        "Principal": {
>                            "Service": "ec2.amazonaws.com"
>                        },
>                        "Action": "sts:AssumeRole"
>                    }
>                ]
>            },
>            "Description": "Allows EC2 instances to call AWS services on your behalf.",
>            "MaxSessionDuration": 3600



# Design Choices

You use the AWS SDK for Python (Boto3) to create, configure, and manage AWS services, such as Amazon Elastic Compute Cloud (Amazon EC2) and Amazon Simple Storage Service (Amazon S3). The SDK provides an object-oriented API as well as low-level access to AWS services.

This module provides functions for encoding binary data to printable ASCII characters and decoding such encodings back to binary data. It provides encoding and decoding functions for the encodings specified in [**RFC 3548**](https://tools.ietf.org/html/rfc3548.html), which defines the Base16, Base32, and Base64 algorithms, and for the de-facto standard Ascii85 and Base85 encodings.

```
import boto3
import base64
```



Generate random numbers with a range between 1 to 100.

```
RandomNo = randrange(1, 100)
```



I have defined counter for `Maxcount` for 10 loops which link with `Left` variable, so that it is used to count down in order to display the number of remaining tries.

```
# Initializing the number of guesses.
count = 0
MaxCount = 10
Left = MaxCount
while count < MaxCount:
	count += 1
	Left  -= 1
```



`UserInput` variable will get the user input in integer format.

```
UserInput = int(input("Enter guess what the target number is:- "))
```



The code below is for `If`, `else` statement for decision-making.

```
	if RandomNo == UserInput:
		print("Good Job! You guessed it!")
		# Once guessed, loop will break
		break
		
	elif RandomNo > UserInput:
		print("Oops, Your guess was LOW.")
		print("You have only left",Left,"Chances")
	elif RandomNo < UserInput:
		print("Oops, Your guess was HIGH.")
		print("You have only left",Left,"Chances")
```



If the maximum number of count >=10, the game will end and display the target number.

```
if count >= MaxCount:
	print("\nSorry, You didn't guess my number. It was %d" % RandomNo)
```



# Built in

* python 3.x







# Author

Natchathiram Premkumar
