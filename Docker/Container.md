#docker #devops 
```table-of-contents
title: Index
style: nestedList # TOC style (nestedList|nestedOrderedList|inlineFirstLevel)
minLevel: 0 # Include headings from the specified level
maxLevel: 0 # Include headings up to the specified level
includeLinks: true # Make headings clickable
hideWhenEmpty: false # Hide TOC if no headings are found
debugInConsole: false # Print debug info in Obsidian console
```

***NOTE***: Virtualization is the process through which an application is isolated from other applications running on the same machine.
# What is a container?
+ Allow the **packaging** and **isolation** of applications + runtime environment i.e. all the files needed for the app to run.
+ Solves the problem of application not running on a different machine due to differences in configuration or library dependencies.
+ Virtualizes the OS i.e. all apps share the same OS and resources.
+ It is lightweight and portable, however Linux containers cannot run on Window containers and vice-versa.
+ Mac containers don't exist, however linux containers can be run on Mac.
![What is a Container? | Docker](https://www.docker.com/app/uploads/2021/11/docker-containerized-appliction-blue-border_2.png)
# Contrast with VM
+ Virtual Machines (VM) **virtualize the entire machine down to the hardware** unlike containers which virtualize the OS. i.e. for each app it will create "guest os" which is separate from the host operating system.
+ This increases the size of the application as it includes all the os files, unlike containers which are lightweight.
+ Resource sharing is not possible unlike containers. VM creates copy of all resources too.
![What is a Container? | Docker](https://www.docker.com/app/uploads/2021/11/container-vm-whatcontainer_2.png)

|                 |                                                                                                                                                 |                                                                                                         |
| --------------- | ----------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| Characteristics | Container                                                                                                                                       | Virtual machine                                                                                         |
| Definition      | Software code package containing an application’s code, its libraries, and other dependencies that make up the application running environment. | Digital replica of a physical machine. Partitions the physical hardware into multiple environments.     |
| Virtualization  | Virtualizes the operating system.                                                                                                               | Virtualizes the underlying physical infrastructure.                                                     |
| Encapsulation   | Software layer above the operating system required for running the application or application component.                                        | Operating system, all software layers above it, multiple applications.                                  |
| Technology      | Container engine coordinates with the underlying operating system for resources.                                                                | Hypervisor coordinates with underlying operating system or hardware.                                    |
| Size            | Lighter weight (think in terms of MB).                                                                                                          | Much larger (think in terms of GB).                                                                     |
| Control         | Less control of the environment outside the container.                                                                                          | More control over the entire environment.                                                               |
| Flexibility     | More flexible. You can quickly migrate between on-premises and cloud-centered environments.                                                     | Less flexible. Migration has challenges.                                                                |
| Scalability     | Highly scalable. Granular scalability possible with microservices.                                                                              | Scaling can be costly. Requires switching from on-premises to cloud instances for cost-effective scale. |