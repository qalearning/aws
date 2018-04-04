# Lab 5L: Managing Resources with Tagging (Linux)


This lab is divided into two parts:

- In the **Task** portion of this lab, you will use the AWS Command Line Interface (CLI) to inspect the tags assigned to a number of Amazon EC2 instances. You will then use pre-provided scripts to shut down and start up a number of Amazon EC2 instances simultaneously, based on their tags.
- In the **Challenge** portion of this lab, you will be challenged to think of a way to terminate instances that fail to implement specific tags.


**Objectives**
After completing this lab, you will be able to:

-   Apply tags to existing AWS resources.
-   Find resources based on tags.
-   Use the AWS CLI or AWS SDK for PHP to stop and terminate Amazon EC2 instances based on certain attributes of the resource.


**Prerequisites**
This lab requires:

- Access to a notebook computer with Wi-Fi running Microsoft Windows, Mac OS X, or Linux (Ubuntu, SuSE, or Red Hat).
- The Qwiklabs lab environment is not accessible using an iPad or tablet device, but you can use these devices to access the student guide.
- For Microsoft Windows users: Administrator access to the computer.
- An Internet browser such as Chrome, Firefox, or IE9 (previous versions of Internet Explorer are not supported).
- An SSH client such as PuTTY.

**Duration**

This lab will require approximately **30 minutes** to complete.

**Scenario**

The environment for this lab (pictured below) consists of an Amazon VPC named Lab VPC with a public and a private subnet.
The public subnet contains one Amazon Linux instance named CommandHost; the AWS Command Line Interface (CLI) tools have been pre-installed and configured for you on this instance.
The private subnet contains 10 Amazon EC2 Linux instances. Each private instance has three custom tags applied to it:

| Tag Name | content |
| :------------- | :------------- |
| Project | The project that the instance belongs to. The instances inthis lab belong to one of two projects: **ERPSystem** and **Experiment1**. |
|Version|The version of the project that this instance belongs to. All Version tags are currently set to 1.0.|
|Environment|One of three values: **development**, **staging**, or **production**.|

<br>

In the Task portion of this lab, you will log in to the Command Host and run some commands to find and change the Version tag on all development instances. You will run several examples that show how you can use the JMESPath syntax supported by the AWS CLI `--query` option to return richly formatted output. You will then use a set of pre-provided scripts to stop and re-start all instances that are tagged as belonging to the **development** environment.

![](http://us-west-2-aws-training.s3.amazonaws.com/awsu-ilt/AWS-100-SYS/v2.6/lab-5-resources-linux/scripts/lab-5-resources-linux-architecture.png)

___
## Accessing the AWS Management Console

- To the right of the lab title, click **Start Lab** to launch your Qwiklabs.

	![](http://us-west-2-aws-training.s3.amazonaws.com/awsu-ilt/AWS-100-SYS/v2.6/lab-1-compute-linux/scripts/start.png)

- On the **Connect** tab of the Qwiklabs page, copy the **Password** to the clipboard and then click **Open Console**.

	![](http://us-west-2-aws-training.s3.amazonaws.com/awsu-ilt/AWS-100-SYS/v2.6/lab-1-compute-linux/scripts/open.png)

- Sign in to the AWS Management Console using the following steps:
  - For **User Name**, type **awsstudent**
  - For **Password**, paste the password copied from the clipboard.
  - Click **Sign In**.

___
## Task 1: Using Tags to Manage Resources

**Note** These instructions are for performing the lab in a **Linux** environment. If you would like to use Windows, please refer to the other set of instructions for Lab 5 Windows.

In this task, you will log in to the Command Host, and use the AWS CLI to find a set of resources according to their tags. You will then use the AWS CLI to change the value of one of the tags.


### Task 1.1a: Connecting to Your Existing Command Host Instance From A Windows Machine

In this procedure, you will connect to the **Command Host** instance, from a Windows machine using the PuTTY secure shell (SSH) client and configure the AWS CLI to use the correct region.

**If you are using a Macintosh or Linux system, please see the next subtask 1.1b.**

1. [1]In the **AWS Management Console**, on the **Services** menu, click **EC2** and then click **Instances**.
2. [2]Obtain the **IPv4 Public IP** address of the **Command Host** instance to which you will connect.
3. [3]On the **Description** pane, copy the **Public IP** and **Availability zone** value.
4. [4]From the Qwiklabs page for your lab, on the **Connect** tab, click the drop-down menu next to **EC2 Key Pair Private Key**, and click **Download PPK**.
5. [5]Download both the PuTTY.exe and the Pageant.exe clients to a folder of our choice from the following URL: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html. Run both applications.
6. [6]Open ***Pageant.exe*** from your system tray, and click **Add Key**. Select the PPK key that you downloaded from the Qwiklabs lab page.

    **Note** Whenever you add a new PPK file, you should delete all previous PPK files from prior labs. Otherwise, you may encounter an error that authentication has failed after too many attempts.
7. [7]Open ***PuTTY.exe***. In the Session window, find the Host name (or IP address) field, and type in ***ec2-user@ip-address***, where ip-address is the Public IP address of your Amazon EC2 instance that you copied down earlier. This will log you in to the remote server as the user ***ec2-user***, which is the default Linux user on Amazon EC2.
8. [8]In the left navigation menu, click **Connection -> SSH -> Auth**. Verify that the checkbox **Attempt authentication using Pageant** is selected.
![](http://us-west-2-aws-training.s3.amazonaws.com/awsu-ilt/AWS-100-SYS/v2.6/lab-1-compute-linux/scripts/lab-1-compute-linux-img3.png)
9. [9]In the same window, under **Authentication parameters**, select **Allow agent forwarding**.
Agent forwarding will allow you to perform â€œpass-throughâ€ authentication, where the private keys stored on your local computer are passed through an existing connection (e.g., to a Bastion instance in your public subnet) and on to other instances (e.g., in your private subnet).
10. [10]Click **Open** to open the remote session. PuTTY will ask whether you wish to cache the serverâ€™s host key. Click **Yes**.
11. [11]Skip to **Task 1.2**


### Task 1.1b: Connecting to Your Existing Command Host Instance from a MacOS or Linux

In this procedure, you will connect to the **Command Host** instance, from a MacOS or Linux machine and configure the AWS CLI to use the correct region.

12. [12]In the **AWS Management Console**, on the **Services** menu, click **EC2** and then click **Instances**.
13. [13]Obtain the **IPv4 Public IP** address of the **Command Host** instance to which you will connect.
14. [14]On the Description pane, copy the **Public IP** and **Availability zone** value.
15. [15]From the Qwiklabs page for your lab, click the **Connect** tab and click the drop-down list next to **EC2 Key Pair Private Key**, and click **Download PEM**.
16. [16]Open the Terminal application.
17. [17]Enter the commands below. In both commands, replace ***\<path-to-pem-file\>*** with a reference to the PEM file that you downloaded in previous step and replace the ***\<public-ip\>*** with your EC2 instance public IP.

```
chmod 400 <path-to-pem-file>
ssh -i <path-to-pem-file> ec2-user@<public-ip>
```


### Task 1.2: Finding Development Instances For The Project

Now that you are logged in, you can use the AWS CLI to find the resources in your private subnet that belong to the **ERPSystem** project and are in the Environment named **development**. You will also see how to use the AWS CLI **--query** option to produce richly formatted results.

18. [18]To find all instances in your account that are tagged with a tag of **Project** and a value of **ERPSystem**, copy the following command and execute it at your Linux prompt window:

```
aws ec2 describe-instances --filter "Name=tag:Project,Values=ERPSystem"
```

The command should output the full set of parameters available for all eight instances that are tagged **Project=ERPSystem**. This is a lot of output, and most of it does not apply to this lab. In the next step, you will use the **--query** parameter to narrow down the results.

19. [19]Use the `--query` parameter to limit the output of the previous command to only the instance ID of the discovered instance:

```
aws ec2 describe-instances --filter "Name=tag:Project,Values=ERPSystem" --query 'Reservations[*].Instances[*].InstanceId'
```

Your output entries will now consist of a list of instance IDs:

<p style = "font-family:'Courier New'">
[ <br>
&nbsp;[<br>
&nbsp;&nbsp;"i-135b491e" <br>
&nbsp;], <br>
&nbsp;[ <br>
&nbsp;&nbsp;"i-3e584a33" <br>
&nbsp;], <br>
&nbsp;â€¦ <br>
]
</p>

The **--query** command used in this example uses the JMESPath wildcard syntax to specify that the command should iterate through all reservations and all instances and return the InstanceId for each instance in the return results.
This is an improvement over returning every property of our instances. But what if you want to include multiple fields in the output?

20. [20]Copy the following command and execute it in Linux prompt window to include both the instance ID and the Availability Zone of each instance in your return result:

```
aws ec2 describe-instances --filter "Name=tag:Project,Values=ERPSystem" --query 'Reservations[*].Instances[*].{ID:InstanceId,AZ:Placement.AvailabilityZone}'
```

Two name/value pairs are returned for each result.
This command builds on the previous commandâ€™s use of the JMESPath syntax by using curly braces to specify a query for multiple properties on each instance returned:

`object.{Alias1:PropertyName1,Alias2:PropertyName2,[â€¦]}`

As seen here, you can specify an alias for each property in order to return a more abbreviated output format.

With this output, you can clearly see that your filter worked, and you are only seeing instances that are associated with the project **ERPSystem**. However, you still will probably not be able to identify which instances are being returned, based on this information. In the next steps, you will see how to include the value of your custom tags in the return output.

21. [21]To include the value of the **Project** tag in your output, copy and execute the following command in Linux:

```
aws ec2 describe-instances --filter "Name=tag:Project,Values=ERPSystem" --query 'Reservations[*].Instances[*].{ID:InstanceId,AZ:Placement.AvailabilityZone,Project:Tags[?Key==`Project`] | [0].Value}'
```

Your output now includes the value of the Project tag:

<p style = "font-family:'Courier New'">
[[{ <br>
&nbsp;&nbsp;&nbsp;"Project": "ERPSystem", <br>
&nbsp;&nbsp;&nbsp;"AZ": "us-west-2a", <br>
&nbsp;&nbsp;&nbsp;"ID": "i-3250b838" <br>
    }],...]
</p>

The value of a specific named tag can be retrieved via a JMESPath query, using the following syntax:

***Tags[?Key==\`Project\`] | [0].Value***

This syntax instructs JMESPath to find all elements within the **Tags** array that have a **Key** value of **Project**. The output of that commandâ€”which will be a single Tags elementâ€”is then piped to another command that selects the first instance of this filtered set and selects the named parameter **Value**, which is the value of the **Project** tag. This result is then assigned the alias **Project**.

22. [22]Copy and execute the following command to also include the Environment and Version tags in your output:

```
aws ec2 describe-instances --filter "Name=tag:Project,Values=ERPSystem" --query 'Reservations[*].Instances[*].{ID:InstanceId,AZ:Placement.AvailabilityZone,Project:Tags[?Key==`Project`] | [0].Value,Environment:Tags[?Key==`Environment`] | [0].Value,Version:Tags[?Key==`Version`] | [0].Value}'
```

The results will give you a fuller picture of the instances currently associated with the project named **ERPSystem**:

<p style = "font-family:'Courier New'">
[[{ <br>
&nbsp;"Environment": "production", <br>
&nbsp;"Project": "ERPSystem", <br>
&nbsp;"Version": "1.0", <br>
&nbsp;"AZ": "us-west-2a", <br>
&nbsp;"ID": "i-3250b838" <br>
&nbsp;}], <br>
  â€¦ <br>
]
</p>

23. [23]Finally, add a second tag filter to see only the instances associated with the project named **ERPSystem** that belong to the Environment named **development**:

```
aws ec2 describe-instances --filter "Name=tag:Project,Values=ERPSystem" "Name=tag:Environment,Values=development" --query 'Reservations[*].Instances[*].{ID:InstanceId,AZ:Placement.AvailabilityZone,Project:Tags[?Key==`Project`] | [0].Value,Environment:Tags[?Key==`Environment`] | [0].Value,Version:Tags[?Key==`Version`] | [0].Value}'
```

You should see only two instances returned by this command, both with a **Project** tag value of ERPSystem and an **Environment** tag value of development:

<p style = "font-family:'Courier New'">
[[{ <br>
&nbsp;"Environment": "development", <br>
&nbsp;"Project": "ERPSystem", <br>
&nbsp;"Version": "1.0", <br>
&nbsp;"AZ": "us-west-2a", <br>
&nbsp;"ID": "i-9552ba9f" <br>
&nbsp;}], <br>
   ... <br>
]
</p>


### Task 1.3: Changing Version Tag for Development Process

In this procedure, you will change all of the **Version** tags on the instances marked as **development** for the project **ERPSystem**.

You could individually set these properties on each affected instance, but an automated approach is more practical. You can use a simple Linux Bash shell script to build on the queries you built earlier and modify tag entries as a batch operation.


24. [24]On the CommandHost, open the file **/home/ec2-user/change-resource-tags.sh**:

```
nano change-resource-tags.sh
```

25. [25]Examine the contents of the script:

```
#!/bin/bash

ids=$(aws ec2 describe-instances --filter "Name=tag:Project,Values=ERPSystem" "Name=tag:Environment,Values=development" --query 'Reservations[*].Instances[*].InstanceId' --output text)

aws ec2 create-tags --resources $ids --tags 'Key=Version,Value=1.1'
```

This script first uses the command `aws ec2 describe-instances` to return only a list of instance IDs for the development machines that belong to the **ERPSystem** project. It then passes those values to the `aws ec2 create-tags` command, which either creates a new tag or (in this case) overwrites an existing tag.  

Notice how the first command uses the **--output text** option to manipulate the return results as text instead of as JSON. Using this command instead of JSON on a simple return resultâ€”in this case, a list of IDsâ€”can make it easier to manipulate the return result and pass it to other commands.

26. [26]Close the nano editor and run this command from the Linux command prompt:

```
./change-resource-tags.sh
```

27. [27]To verify that the version number on these instances has been incremented and that other non-development boxes in the **ERPSystem** project have been unaffected, copy and run the following command:

```
aws ec2 describe-instances --filter "Name=tag:Project,Values=ERPSystem" --query 'Reservations[*].Instances[*].{ID:InstanceId, AZ:Placement.AvailabilityZone, Project:Tags[?Key==`Project`] |[0].Value,Environment:Tags[?Key==`Environment`] | [0].Value,Version:Tags[?Key==`Version`] | [0].Value}'
```

___
## Task 2: Stop and Start Resources by Tag

In this task, you will use a pre-provided script to stop and start a set of instances tagged as development instances.  


### Task 2.1: Examining the Stopinator Script

28. [28]On the Command Host Instance, cd into the directory aws-tools in the home directory:

```
cd aws-tools
```

29. [29]Open the file **stopinator.php** and examine its contents:

```
nano stopinator.php
```

The **stopinator.php** script is a simple script that uses the AWS SDK for PHP to stop and restart instances based on a set of tags. This enables scenarios such as shutting off your development environment servers at the end of the day and restarting them the next morning. The script will look in every AWS region for instances that match the specified tags.
The script takes the following arguments:

- **-t**: A set of tags in the following format: `name=value;name=value`
      The script converts these tags into the format expected by the AWS PHP call `Ec2::DescribeInstance()`. If this optional parameter is absent, the script will identify and shut down all running Amazon EC2 instances in the account.
- **-s**: A Boolean parameter; no arguments are required. When this parameter is present, instances identified by **-t** are started instead of stopped.

30. [30]Exit your nano editor.

### Task 2.2: Stopping and Restarting ERPProject Development Process

In this task, you will use the stopinator.php script to bring down and bring back up your development environment for the **ERPSystem** project.

31. [31]From the Linux shell, run the stopinator.php script:

```
./stopinator.php -t"Project=ERPSystem;Environment=development"
```
The output should look like this, indicating that two instances will be stopped in your current AWS region. (Your results will differ depending on the region in which your lab is running.)

<p style = "font-family: 'Courier New'">Region is us-east-1 <br>
  No instances to stop in region <br>
Region is us-west-1 <br>
  No instances to stop in region <br>
Region is us-west-2 <br>
  Found instance i-9552ba9f <br>
  Found instance i-d35fb7d9 <br>
Stopping all identified instances... <br>
[â€¦] <br>
  No instances to stop in region <br>
Region is sa-east-1 <br>
  No instances to stop in region <br>
</p>

32. [32]On the **Services** menu, click **EC2**.
33. [33]In the navigation pane, click **Instances**.
34. [34]Verify that two instances are stopping or have already been stopped.
35. [35]Return to the SSH session for Command Host, and from the Linux prompt, restart your instances with the following command:

```
./stopinator.php -t"Project=ERPSystem;Environment=development" -s
```
36. [36]Return to the EC2 Management Console window and verify that the two instances that were previously shut down are now restarting.


___
## Task 3: Generate a Custom Metric From an Instance

In this Challenge, you will be asked to find a way to terminate instances that do not conform to certain security guidelines.

**Note** If you are already familiar with AWS, we recommend that you try this challenge yourself **before** reading the detailed solution provided in the next section. When you have completed the challenge, check your work by reviewing the detailed solution.

**Challenge Description**
**Scenario**: Your company wants you to create automated processes that will automatically terminate instances that might allow a possible security breach. You have identified a list of security risks and are now deciding how to implement them efficiently by using either AWS CLI commands or the PHP SDK for AWS.
**Challenge**: Your first security task is simple: find all instances in your private subnet that do not implement the **Environment** tag, and terminate them (i.e., a â€œtag-or-terminateâ€ policy).

**Hints**:

- If you are not comfortable with PHP or a similar programming language (such as Python or Ruby) for which an AWS SDK is available, try to use a series of AWS CLI commands to perform this task.
- The AWS PHP call `Ec2::terminateInstances()` can terminate instances. The equivalent AWS CLI command is `aws ec2 terminate-instances`.
- You can use the **stopinator** script from section 2 as a reference for any code you write.

**Challenge Solution Overview**

There are multiple ways to approach this problem using a variety of programmatic or command-line solutions. The general solution to the problem consists of the following steps:

- Identify all of the instances that currently have the **Environment** tag defined.
- Compare this against the list of all available instances, and record the instance IDs of any instances that are not part of the list obtained from Task 1.
- Supply the instance IDs of the non-tagged instances to AWS by using the aws ec2 stop-instance command (AWS CLI) or the **Ec2::terminateInstances()** API call (PHP).

The following solution demonstrates how this problem could be solved using a PHP script.


### Task 3.1: Review the Tag-Or-Terminate Script

37. [37]Open the file **terminate-instances.php**.
38. [38]Examine the **params** block for this script. Note that it takes two arguments: the current region that you are running in (**region**), and the ID of a subnet (**subnetid**). The code uses the **subnetid** argument to determine where to look for non-compliant instances.
39. [39]Examine the first block of code, beginning with the comment `# Obtain a list of all instances with the Environment tag set`.
This block of code uses the **describeInstances()** method, a filter to find all instances that have the **Environment** tag defined, regardless of the tagâ€™s value. It stores all of the instance IDs that it finds in a hash table.
40. [40]Examine the second block of code.
This code examines all instances within your subnet and compares them to the list of instances that are tagged with the **Environment** tag. If an instance is not in the tagged list, then its instance ID is added to a list of instances to terminate.
41. [41]Examine the last section of the script.
These lines use the list of non-compliant instance IDs as an argument to the **terminateInstances** method.


### Task 3.2: Configuring Environment to Test Script

Before running the script, you will need to alter a couple of instances in your lab so that they no longer have the **Environment** tag defined.

42. [42]Return to your EC2 Management Console and observe the instances running in your lab environment.
43. [43]Select one of the instances in your private subnet.
44. [44]On the **Tags** tab for the instance, click **Add/Edit Tags**.
45. [45]Find the Environment tag, and click the **remove** icon.
46. [46]Click **Save**. Repeat this process for one other instance in your private subnet.


### Task 3.3: Run the Script

47. [47]In the EC2 Management Console, select one of the instances in your private subnet.
48. [48]On the **Description** tab for your instance, find the **Availability Zone** field, and copy all but the last letter to a text file. This value will be referred to as region in a subsequent procedure.
49. [49]Find the **Subnet ID** field, and copy its value to a text file. This value will be referred to as subnet-id in a subsequent procedure.
50. [50]Return to your SSH session, and run the **terminate-instances.php** script (replacing the \<region\> with your region and \<subnet-id\> with your subnet-id):

```
./terminate-instances.php -region <region> -subnetid <subnet-id>
```

<p style = "font-family:'Courier New'">You should see something similar to the following results: <br>
Checking i-dd3a90d1 <br>
Checking i-a4248ea8 <br>
Checking i-793a9075 <br>
Checking i-a9248ea5 <br>
Checking i-aa248ea6 <br>
Checking i-da3a90d6 <br>
Checking i-a13b91ad <br>
Checking i-a23b91ae <br>
Checking i-ab248ea7 <br>
Terminating instances... <br>
Instances terminated.
</p>

___
## Lab Complete
Congratulations! You have completed this lab. To clean up your lab environment, do the following:

51. [51]To sign out of the AWS Management Console, click **awsstudent**  at the top of the console, and then click **Sign Out** .
52. [52]On the Qwiklabs page, click **End**.
