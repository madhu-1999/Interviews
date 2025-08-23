#aws #cloud 
```table-of-contents
title: Index
style: nestedList # TOC style (nestedList|nestedOrderedList|inlineFirstLevel)
minLevel: 0 # Include headings from the specified level
maxLevel: 0 # Include headings up to the specified level
include: 
exclude: 
includeLinks: true # Make headings clickable
hideWhenEmpty: false # Hide TOC if no headings are found
debugInConsole: false # Print debug info in Obsidian console
```
# Prerequisite
[[Authorization]]
[[Authentication]]
# What is IAM?
+ Identity and access Management (IAM) is a tool to manage access and type of access to resources.
+ It is a global service i.e. not region specific. When an IAM user is created, it is available everywhere. This is in contrast to other AWS services like EC2 where region has to be specified.
# User
+ Individual or application that needs access to AWS resource. 
+ Each user receives unique credentials such as passwords or access keys.
+ **Root user** created by default (identified by account id). Should not be used or shared.
+ An IAM user account has same account id as root + displays `IAM user: <username>`.
+ Each IAM user has a unique id called (User ARN).
# Group
+ Collection of users.
+ Instead of assigning permission to each individual, assign permission to group.
+ Users in that group get the assigned permission.
+ Easier to add/remove users to a group to grant/revoke permissions instead of individual assignment.
# Roles
+ Roles use **temporary** security credentials (i.e. they expire after sometime) to grant access to a user.
+ Meant to be assumed by AWS services, users from another AWS account or external users through SSO who needs them to perform some action.
+ Temporary credentials are provided to the instance through the AWS Security Token Service(STS). These credentials include an access key ID, secret access key, and session token, and they’re valid for a short duration, typically a few hours.
+ Easier to manage permissions because you can update the role’s policies in one place, which automatically applies to all entities assuming the role.
+ Need to define two main things:
## Trust Policies
+ Defines who can assume the role.
+ For example, you might create a trust policy that allows an [[Amazon Elastic Compute Cloud (AWS EC2)|EC2 instance]] or an [[AWS Lambda]] function to assume the role.
```json
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Effect": "Allow",
			"Action": [
				"sts:AssumeRole"
			],
			"Principal":{
				"Service": [
					"ec2.amazonaws.com"
				]
			}
		}
	]
}
```
## Permission Policies
+ Defines the actions that are allowed or denied when role is assumed. Same as [[#Policy]].
## 
>[!example]+
>Suppose you have an application running on an EC2 instance that needs to read from an S3 bucket. Instead of storing access keys on the instance, you can create an IAM role with a policy that allows reading from S3. You then attach this role to your EC2 instance. When the instance runs, it assumes the role and gets temporary credentials to access the S3 bucket.
# Policy
+ JSON document to define permissions.
+ Specify what actions are allowed or denied on which resources.
+ Can be attached to users, groups or roles.
```json
​​{
    "Version": "2012-10-17",
    "Id": "s3-Account-Permissions",
    "Statement": [
        {
	        "Sid": "1",
            "Effect": "Allow",
            "Principal": {
	            "AWS": ["arn:aws:iam:123456789012:root"]
            },
            "Action": [
	            "s3:GetObject",
	            "s3:PutObject"
            ],
            "Resource": ["arn:aws:s3::mybucket/*"]
        }
    ]
}
```
`Version` specifies policy language version.
`Id`: Optional identifier for the policy
`Statement` is collection of statements that define permissions. A policy can have multiple statements. Each statement has:
	`Sid` : Optional identifier for statement.
	`Effect`: Specifies whether the permission is of type "Allow" or "Deny".
	`Principal`:  Defines accounts, users or roles to which policy applies. In example, it is the root account.
	`Action`: Defines the action(s) that are allowed or denied. AWS has specific actions for each resource.
	`Resource`: Specifies the resource that the action applies to. Identified by unique identifiers called Amazon Resource Names (ARNs). In example, it is a S3 bucket.
	`Condition` : Optional conditions that specify when the statement applies.
## Policy Types
### **Managed**
+ AWS managed policies are created and maintained by AWS. Good for commonly used permission sets.
+ Customer Managed policies are custom JSON documents created by you. More flexible.
### **Inline**
+ Policies attached to a **single** user, group or role.
+ Helpful for specific permissions that **shouldn't be reused**.
+ Can be tricky to manage, so less preferred.
# Authentication in IAM
## Password policy
+ Can specify requirements for [[IAM#User|IAM User's]] password, like:
	+ Include uppercase letters
	+ lowercase letters
	+ numbers
	+ non-alphanumeric characters
+ Can Auto generate password at time of IAM user creation and allow user to change their password on login.
+ Can Require users to change password after some time. (say every month).
+ Can Prevent password re-use
>[!info]+
>IAM default password policy:
>8 characters (min)
>Include a min of 3 character types from below:
>	Uppercase
>	Lowercase
>	Numbers
>	Non alphanumeric characters
>Password never expires
>Must not be same as AWS account name or email
## Multi Factor Authentication (MFA)
+ Password you know + security device you own.
+ If password is stolen, account is not compromised.
+ MFA device options in AWS:
	+ Virtual MFA: Like Google Authenticator. supports multiple tokens (i.e. a token per IAM user) on a single device.
	+ Universal 2nd Factor (U2F) Security Key: Supports multiple users using a single security key.
	+ Hardware Key Fob MFA device
	+ Hardware Key Fob MFA device for AWS GovCloud (US)
+ ==Root account can also be protected using MFA.==
## IAM Identity Center
+ Also called AWS SSO, allows SSO login instead of managing username and passwords.
+ Can connect existing identity sources such as Microsoft Active Directory or other identity providers that support SAML 2.0.
+ Configuration done in AWS Management console. Rest is same when IAM user is created.
# IAM  Security Tools
## IAM Credentials Report
+ Account-level 
+ IAM -> Credential Report
+ Reports all users in account and the status of their credentials.
+ Some things that the report contains:
	+ User account name
	+ Creation date
	+ Password enabled
	+ Password last used
	+ Password last changed
	+ MFA active
	+ [[Interacting with AWS Services|Access Keys]] generated
	+ Last use of access keys
	+ Creation date of access keys
	+ Certificates
## IAM Last Access 
+ User level
+ Shows service permissions granted to user, when those services was last accessed and what policy granted the access.
+ IAM->Users->User->Last Access 
# Best practices
+ don't use root account except for setup
+ One physical user = One IAM user
+ Assign users to groups and assign permissions to groups
+ Create a strong password policy
+ Enforce use of MFA
+ Use roles to give permissions to AWS services
+ Use access keys for programmatic access ([[Interacting with AWS Services|CLI/SDK]])
+ Audit permissions of account using [[#IAM Credentials Report]] and [[#IAM Last Access]].
+ Never share IAM users and access keys
# Shared Responsibility Model for IAM
# AWS Responsibility
1. Infrastructure and global network security
2. Configuration and vulnerability analysis
3. Compliance validation
# User Responsibility
1. User, groups, roles, policies management and monitoring
2. Enable MFA on all accounts
3. Rotating keys often
4. Use IAM tools to apply appropriate permissions
5. Analyze access patterns and review permissions.