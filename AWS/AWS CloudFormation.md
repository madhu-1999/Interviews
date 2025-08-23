#aws #cloud 
# Infrastructure As Code (IaC)
+ Say you have resources in Region A, and you want to launch them in Region B for high availability. Manual setup is: **slow, error-prone and hard to reproduce**.
+ In IaC, infrastructure is defined in a file, (think blueprint) which can then be built using automation. Ensures same setup is created each time and, changes to infrastructure can be tracked using version control.

# How does CloudFormation help?
+ It is a IaC service where we can define infrastructure in a declarative way (what to achieve instead of how).
+ Create text-based documents called CloudFormation templates.
+ Parses template and provisions resources by calling AWS API's in the background.