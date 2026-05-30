#aws #cloud 
```table-of-contents
title:Index 
style: nestedList # TOC style (nestedList|nestedOrderedList|inlineFirstLevel)
minLevel: 0 # Include headings from the specified level
maxLevel: 0 # Include headings up to the specified level
include: 
exclude: 
includeLinks: true # Make headings clickable
hideWhenEmpty: false # Hide TOC if no headings are found
debugInConsole: false # Print debug info in Obsidian console
```
# Infrastructure As Code (IaC)
+ Say you have resources in Region A, and you want to launch them in Region B for high availability. Manual setup is: **slow, error-prone and hard to reproduce**.
+ In IaC, infrastructure is defined in a file, (think blueprint) which can then be built using automation. Ensures same setup is created each time and, changes to infrastructure can be tracked using version control.
# How does CloudFormation help?
+ It is a IaC service where we can define infrastructure in a declarative way (what to achieve instead of how).
+ Create text-based documents called CloudFormation templates, which are parsed to provisions resources **in the correct order** by calling AWS API's in the background.
+ Based on the template, auto creates a diagram that shows how all the components specified in the template relate to each other.
+ 
>[!example]
>Suppose in the template we specify that we want:
>1. A security group
>2. Two EC2 instances which use, said security group
>3. Two Elastic IPs for the two EC2 instances
>4. A S3 bucket
>5. A load balancer (ELB)
><br/>
>Then CloudFormation will create them in the right order with the specified configuration.![[www.udemy.com_course_aws-certified-developer-associate-dva-c01_learn_lecture_11851310.png]]

# Template
+ JSON/YAML file that describes a set of resources, their properties and relationships with other resources.
+ Serves as a _blueprint_ for creating and managing infrastructure.
+ Output is creation of a stack or multiple stacks.
+ __Must be uploaded to [[Amazon S3|S3]]__ , and then referenced in CloudFormation.
+ Cannot be edited, so a new version of the updated file must be uploaded to S3.
![[www.udemy.com_course_aws-certified-developer-associate-dva-c01_learn_lecture_11851310 (1).png]]
## Deploying Templates
1. Manual way
	+ Edit template in Infrastructure Composer or code editor
	+ Use the Management console to input parameters, etc..
2. Automated way
	+ Use AWS CLI or CD (Continuous Delivery) tool
# Stack
+ An instance of a CloudFormation template and a fundamental unit in CloudFormation.
+ The template is parsed by CloudFormation to create a stack(s), which provisions the resources specified in the template.
+ When deleted, all provisioned resources are also deleted.
	+ To prevent a stack from being deleted, enable termination protection.
+ To update a stack, we have to replace the template used to create it.
	+ Before updating, CloudFormation shows a __change set__, which is a summary of all the additions, deletions and modifications that will be applied to the stack.
## Stack policy
+ By default, during a stack update, all actions are allowed on all resources. To restrict actions, use a stack policy (JSON doc).
+ All actions are denied by default, when using a stack policy.
	+ Specify allowed actions on allowed resources.
+ Protects resources from unintended updates.
+ Ex: Allow update on all resources by all users except for `ProductionDatabase`.
```json
{
	"Statement": [
		{
			"Effect": "Allow",
			"Action": "Update:*",
			"Principal": "*",
			"Resource": "*"
		},
		{
			"Effect": "Deny",
			"Action": "Update:*",
			"Principal": "*",
			"Resource": "LogicalResourceId/ProductionDatabase"
		}
	]
}
```
## Rollback
In either case, we can see what failed in event logs.
+ Stack creation fails:
	+ Default: All resources (successfully provisioned or not) are deleted.
	+ Option to disable rollback and troubleshoot what happened.
+ Stack update fails:
	+ Default: Stack rolls back to previous known working state.
	+ If rollback was disabled during creation, we can troubleshoot.
+ Rollback failure:
	+ Fix resources manually and issue _ContinueUpdateRollback_ API call from management console or CLI or call API directly.
# StackSets
+ Create, update or delete stacks across **multiple accounts and regions** using a single template.
+ When a stack set is updated, all associated stack instances across regions and accounts are updated.
+ Only Admin account or Delegated Admin can create stack sets
![[www.udemy.com_course_aws-certified-developer-associate-dva-c01_learn_lecture_42314138.png]]
# Template Format
Each template contains one or more of the following sections:
## Resources
+ __Mandatory__ section
+ Specify AWS resources and their configurations.
	+ Resources can reference each other
+ Only a few AWS services are ___NOT___ supported. Workaround using [[#CloudFormation Custom Resources]]
```yaml
Resources:
# Logical id that can be used to reference this resource
  Ec2Instance:
  # Resource type identifiers are of the form: 
	  # service-provider :: service-name :: data-type
    Type: 'AWS::EC2::Instance'
    Properties:
      SecurityGroupIds:
      # Referencing a resource using `Ref`
        - !Ref InstanceSecurityGroup
        - sg-12a4c434
      KeyName: MyKey
      ImageId: ami-1234567890abcdef0
      # Fetching value of a resource attribute using `GetAtt`.
      AvailabilityZone: !GetAtt Ec2Instance.AvailabilityZone
  InstanceSecurityGroup:
    Type: 'AWS::EC2::SecurityGroup'
    Properties:
      GroupDescription: Enable SSH access via port 22
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: 22
          ToPort: 22
          CidrIp: 0.0.0.0/0
```
### __DeletionPolicy__
+ Attribute of a resource that specifies what happens when to created resources when the template is deleted or when a resource is removed from the template.
	+ DeletionPolicy = Delete
		+ Default action is to delete resources.
		+ Does not work for S3 bucket if it is not empty.
	+ DeletionPolicy = Retain
		+ Preserves the resource on template deletion or removal of resource from the template.
	+ DeletionPolicy = Snapshot
		+ Creates a snapshot for supported resources before deleting it. 
```yaml
Resources:
# Default deletion policy: delete applies
 Ec2Instance:
    Type: 'AWS::EC2::Instance'
    Properties:
      SecurityGroupIds:
        - !Ref MySg
        - sg-12a4c434
  MySg:
    Type: 'AWS::EC2::SecurityGroup'
    DeletionPolicy: Retain
    Properties:
      GroupDescription: Enable SSH access via port 22
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: 22
          ToPort: 22
          CidrIp: 0.0.0.0/0
          
  MyEBS:
    Type: 'AWS::EC2::Volume'
    DeletionPolicy: Snapshot
    Properties:
      AvailabilityZone: us-east-1a
      Size: 1
      VolumeType: gp2
```
## Parameters
+ __Optional__ section
+ Input custom values into the template when creating or updating a stack.
+ Use if:
	+ You want to reuse the template with some values changed
	+ If inputs cannot be determined ahead of time
+ A parameter contains a list of attributes that defines its values and constraints. [Allowed attributes](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/parameters-section-structure.html#parameters-section-structure-properties)	
	+ The only __required__ attribute is _Type_.
```yaml
Parameters:
# Logical Id of the parameter
  InstanceTypeParameter:
    Description: Enter t2.micro, m1.small, or m1.large. Default is t2.micro.
    Type: String
    Default: t2.micro
    # At runtime user can choose from the list of values
    AllowedValues:
      - t2.micro
      - m1.small
      - m1.large
  
  DBPwd:
   # Prevents value from being logged 
    NoEcho: true
    Description: The database admin account password
    Type: String
    MinLength: 1
    MaxLength: 41
    AllowedPattern: ^[a-zA-Z0-9]*$

Resources:
  Ec2Instance:
    Type: AWS::EC2::Instance
    Properties:
    # Reference parameter using `Ref`
      InstanceType: !Ref InstanceTypeParameter
      ImageId: ami-0ff8a91507f77f867    
```
### __Pseudo Parameters__
+ AWS provided parameters that can be referenced in templates.
+ Their values are resolved when a stack is created

| Reference Value       | Example returned value                                      |
| --------------------- | ----------------------------------------------------------- |
| AWS::AccountId        | 123456789012                                                |
| AWS::Region           | us-east-1                                                   |
| AWS::StackId          | arn:aws:cloudformation:us-east-1:123456789012:stack/MyStack |
| AWS::StackName        | MyStack                                                     |
| AWS::NotificationARNs | [ arn:aws:sns:us-east-1:123456789012:MyTopic ]              |
| AWS::NoValue          | _No value_                                                  |
## Mappings
+ __Optional__ section
+ Hardcoded variables in the template.
+ Can be used to differentiate between different regions, environments (dev vs prod), AMI types etc..
+ Use when:
	+ all the values that can be taken are known in advance (dev vs prod)
	+ values can be derived using pseudo parameters (AWS region, AZ)
```yaml
AWSTemplateFormatVersion: 2010-09-09
Parameters:
  EnvironmentType: 
    Description: The environment type (Dev or Prod)
    Type: String
    Default: Dev
    AllowedValues: 
      - Dev
      - Prod
        
Mappings:
# Logical id to reference a mapping
  RegionAndEnvironmentToInstanceType:
    # Top level key
    us-east-1: 
    # Second level key
      Dev: t3.micro
      Prod: c5.large
    us-west-1: 
      Dev: t2.micro
      Prod: m5.large

  RegionAndEnvironmentToSecurityGroup: 
    us-east-1: 
      Dev: sg-12345678
      Prod: sg-abcdef01
    us-west-1: 
      Dev: sg-ghijkl23
      Prod: sg-45678abc
      
Resources:
  Ec2Instance:
    Type: AWS::EC2::Instance
    Properties:
      ImageId: '{{resolve:ssm:/aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2}}'
# Reference mapping using `FindInMap`
# Syntax: !FindInMap[ LogicalId, Top level key, Second Level Key]
# `Ref` used to derive region value from psuedo parameter
      InstanceType: !FindInMap [RegionAndEnvironmentToInstanceType, !Ref 'AWS::Region', !Ref EnvironmentType]
      SecurityGroupIds:
        - !FindInMap [RegionAndEnvironmentToSecurityGroup, !Ref 'AWS::Region', !Ref EnvironmentType]
```
## Outputs
+ __Optional__ section
+ Declare output values that capture important information about resources (when stack is created), such as S3 bucket name, VPC id, subnet id etc..
+ These outputs values can be referenced in other templates if we _export_ them and the other templates _import_ it.
+ A stack cannot be deleted until all other stacks that reference its output values are deleted.
````tabs
tab: Template1.yaml
```yaml
...
Outputs:
# Logical id
  SSHSecurityGroup:
    Description: The SSH Security group of our company
    # Value that we are storing as output 
    Value: !Ref MyCompanyWideSSHSecurityGroup  
    # To reference `Value` in other templates, we need this block
    Export:   
    # `SSHSecurityGroup` is used by other templates to refer to the output value
      Name: SSHSecurityGroup
      # The exported output name must be a unique for a region
```
tab: Template2.yaml
```yaml
...
Resources:
  MySecureInstance:
    Type: AWS::EC2::Instance
    Properties:
      ImageId: ami-0742b4e673072066f
      InstanceType: t2.micro
      AvailabilityZone: us-east-1a
      SecurityGroups:
      # Import output value `SSHSecurityGroup` 
        - !ImportValue SSHSecurityGroup
```
````
## Conditions
+ __Optional__ section
+ Used to control creation of resources or outputs based on a condition
+ A condition can reference another condition, parameter value or mapping.
	+ Some common ones are: 
		+ Environment (dev / test / prod)
		+ Pseudo parameters (AWS::Region)
+ The following functions can be used to define conditions:
	+ Fn::And
	+ Fn::Equals
	+ Fn::ForEach
	+ Fn::If
	+ Fn::Not
	+ Fn::Or
```yaml
Conditions:
# `IsProduction`: Logical id to reference in another condition

# Value of `Environment` parameter is being referred. 
  IsProduction: !Equals[!Ref Environment, "prod"]
  IsFeatureEnabled: !Equals[!Ref FeatureFlag, "enabled"]
# Referring to two conditions `IsProduction` and `IsFeatureEnabled` to create a new condition  
  IsProdAndFeatureEnabled: !And
    - !Condition IsProduction
    - !Condition IsFeatureEnabled
      
# Associate condition with a resource
Resources:
  NewVolume:
    Type: AWS::EC2::Instance
    # Only create resource if `IsProduction` is true
    Condition: IsProduction
    Properties:
      Size: 100
      
 # Associate condition with a output     
Outputs:
  VolumeId:
  # Store output value only if condition `IsProduction` is true
    Condition: IsProduction
    Value: !Ref NewVolume
```
## Other Template sections
1. Rules
2. Transform
3. Metadata
## CloudFormation Custom Resources
+ Used to:
	+ Define AWS resources not supported by CloudFormation
	+ Define custom provisioning of on-premises or 3<sup>rd</sup> party resources.
	+ Run custom scripts during create / update / delete of stacks, through [[AWS Lambda]] functions. (Ex: lambda function to empty a S3 bucket before deleting it)
+ Defined in template using `AWS::CloudFormation::CustomResource` or `Custom::<resource-name>` (recommended).
+ Backed by a Lambda function or [[Amazon SNS]] topic.
```yaml
Resources:
  MyCustomResource:
  Type: Custom::MyLambdaResource
  Properties:
  # Specifies where CloudFormation should send request to.
  # Lambda function or SNS topic must be in the same region
  # Mandatory property
    ServiceToken: <lambda arn or sns arn>
    # Optional Input values
    ExampleProperty: "ExampleValue"
```
# [[IAM]] permissions
+ Often the user executing a template i.e creating a stack from a template does not have the necessary permissions to create all the resources.
+ To avoid giving the user all the necessary permissions, we use _service roles_.
+ A service role is a role that a service assumes to perform actions on your behalf. ^1b71a7
	+ Assume we have a template that creates some EC2 and S3 resources.
	+ When creating a stack, we give CloudFormation a service role which has the permissions _EC2FullAccess_ and _S3FullAccess_. 
	+ If a service role is not provided, CloudFormation defaults to user permissions.![](https://miro.medium.com/v2/resize:fit:1400/1*mM8uwrqMUMWz5_y-1oHzmw.png)
+ However, to pass on a _service role_ and its permissions to a AWS service (here, CloudFormation), the user __must__ have an _iam::PassRole_ permission.
>[!note]
>A service role is just an IAM role that needs to be created separately.


![](https://miro.medium.com/v2/resize:fit:1400/1*g1KPIH5gQqtCbnAlRnraQA.png)
+ A user does not need to have _iam::PassRole_ permission to update or delete a stack.
# Capabilities
+ To create some resources through a template, we need to explicitly acknowledge that we know the implications of deploying such resources. This acknowledgement is done using capabilities.
+ If capabilities are not specified when necessary, CloudFormation throws a `InsufficientCapabilities` error.
+ It is specified when creating a stack, either in the console or in the CLI command
```bash
aws cloudformation create-stack \  
--stack-name MyStack \  
--template-body file://my-template.yaml \  
--capabilities CAPABILITY_IAM CAPABILITY_AUTO_EXPAND
```
## Types
+ __CAPABILITY_IAM__ and __CAPABILITY_NAMED_IAM__
	+ Use when template is creating or updating IAM resources like users, groups, role etc..
	+ If IAM resources are named, must use __CAPABILITY_NAMED_IAM__.
	+ If IAM resources are unnamed, can use either
+ __CAPABILITY_AUTO_EXPAND__
	+ Use when template contains macros or nested stacks.

# Advantages
+ Create, recreate and destroy infrastructure on the fly
+ Declarative programming (what not how), eliminates the need to handle order of creation and orchestration.
+ Create multiple stacks with the same template, where each stack represents a separation of concern (Ex: VPC stack, Network stack, App stack)