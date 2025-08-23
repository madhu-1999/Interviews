#aws 

==In AWS, everything is an API call==
There are 3 ways to call AWS API's to interact with services:
# AWS Management Console
+ Browser based
+ Visual Navigation
+ Protected using username, password and if activated [[IAM#Multi Factor Authentication (MFA)|MFA]].
# AWS CLI
+ Manage services through CLI using text-based commands.
+ Can use AWS CloudShell (AWS provided terminal on browser) or own terminal.
+ Use automation scripts
```bash
# list all AZs in current region
aws ec2 describe-availability-zones
```
+ Protected by access keys (under an IAM user), thus only those commands work for which user has permission (Refer [[IAM]]).
+ Built on the AWS SDK for Python.
>[!note]+
>+ If using your own terminal, we first need to run `aws configure` command which prompts you to enter access key id, secret access key and AWS region.
>+ When using AWS CloudShell, it automatically uses the credentials of the currently logged in user and current region in account. i.e. no need to run `aws configure` command.
>+ The CloudShell terminal maintains state i.e if we create some files, refresh page, the files remain.
# AWS SDK
+ Use different programming languages like Python to write automation scripts or interact with AWS services programmatically.
+ Protected by access keys
#
>[!note]+
>Access keys are generated through the management console. (User profile -> Security Credentials -> Create Access Key)
>Access key id => username
>Secret access key => password