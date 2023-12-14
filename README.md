# aws-panw-vmseries-cft-deployment
Various CFT-based solutions are collected in this repository for puposes of deploying Software Firewall labs in the context of AWS events platform. Most recently used as AWS Re:invent 2023.

## Outline

### [vmseries-gwlb-2023](https://github.com/seanyoungberg/panw-vmseries-aws-jam/tree/main/vmseries-gwlb-2023)


![diagram](https://github.com/AfrahAyub/aws-panw-vmseries-cft-deployment/assets/93593501/abc4170d-dc38-4519-927b-a56bc5224527)


This is the version being used for Re:invent Jam 2023. It is structured in a specific way to meet the layout of the Jam Event platform.

- aws-jam-panw-gwlb-cfn-root.yaml. This is the root stack that will be deployed. The other template files are nested from this one.

To deploy in normal AWS account:

1. Prep S3 bucket

we will use bucket names `aws-jam-challenge-resources-us-east-1`

To deploy in a separate account, prepare a bucket with same format but apply a unique prefix to the bucket. For example, we used `panw-aws-jam-challenge-resources-us-east-1`


2. Upload assets to S3

Create a folder in the bucket named `panw-vmseries-gwlb/` In that folder, upload the following assets:

- The nested templates (combined, security, vmseries)
- authcodes
- bootstrap.xml
- init-cfg.txt

3. Deploy stack

Use aws-jam-panw-gwlb-cfn-root.yaml to create the stack. There is only one parameter that will need to be modified, which is the name of the bucket. Modify the bucket to include the prefix used in your account



## TASK 1

The "Hop & Code" owners are convinced that our competitor “Sneaky Suds” is exfiltrating our secret recipes.

Your Application development team found some very strange behaviour on the Beer Database Server and asked you to have a deeper look into it to figure out what's going on.  After some investigations, you realized that no outbound traffic gets analyzed by the Palo Alto Networks Firewall. That's something that we have to fix.

You started the journey by conducting a comprehensive audit of the existing AWS infrastructure. With a discerning eye, you created a detailed diagram of the AWS environment. You mapped out the route tables of every VPC and the Transit Gateway.

<br />
<p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/detailed-start.png" alt="Full Starting Diagram with Route Tables" width="1500" /></p>
<br />


## Task

**Redirect all outbound traffic from the Beer Store Data Database Server to the Palo Alto Networks Firewall**

1. First, login to the Firewall. (**Helpful Info Section**)

2. Check the Firewall Monitor traffic logs to verify if you can see any traffic from the Beer Store Data Database Server. ((**Helpful Info Section**)

3. Update AWS routing to redirect the Beer Store Data Database Server outbound traffic for inspection by VM-Series through the Transit Gateway. <br />
<br />

## Task Validation

- Once you made the appropriate changes in the AWS routing you can log into the **Beer Store Data Database Server** via the SSM service and test with the **curl** command if the EC2 instance has internet access.
  - example curl command **sudo curl www.google.de** 


- If the curl command isn't working in the **Beer Store Data Database Server**, check the Palo Alto Networks Firewall Monitor Logs to see which Application is now blocked from the Firewall. 

- You should see the following example log in the firewall monitoring. By adding the following filter **( zone.src eq internal ) and ( zone.dst eq external )** into the Monitor Logs filter bar.<br />
   Some fields in the example log were removed.
<p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/task1-deny2.png" alt="VPC Logs" width="1500" /></p>
<br />

- Input the Name of the blocked Application in the answer field to complete the task. <br />
<br />

## Helpful Info
**To Login into the VM Series Firewall Web UI**
- Identify the Elastic IP (Security VM-Series Management) of the EC2 Instance named "Security VM-Series"
- Open a browser window and navigate to https://("Security VM-Series-EIP")
- Login with the following credentials:
  - Username: admin
  - Password: Pal0Alt0@123

**How to see the Traffic Logs inside the Firewall**
- Login into the firewall
- Inside the firewall navigate to Monitor -> Traffic
- See the following picture as an example <p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/example.png" alt="Monitor Logs" width="1500" /></p>
- In the Monitor Traffic window change the refresh timer from **Manual** to **10 seconds** by clicking on the dropdown field on the top right as the picture below shows
<p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/task1-10.png" alt="Monitor Logs" width="1500" /></p>


**Login into the Beer Store Data Database Server**
- Use the Session Manager to log into the Server
- The name of the VM is "Beer Store Data Database"

**How to find the server's private IP?**
- On the AWS Console go to EC2
- On the EC2 Dashboard click on Instances
- The following EC2 instances are used by the lab:
  - Beer Store Data Database
  - Beer Store Frontend Webserver
  - Security VM-Series (Palo Alto Networks Firewall)<br />
<br />

## Inventory
- Palo Alto Networks NGFW VM-Series
- Amazon EC2
- Amazon VPC
- AWS Systems Manager (SSM)
- AWS Lambda
- AWS AWS Tranist Gateway
- AWS Gateway Load Balancer <br />
<br />

## Services You Should Use
- Palo Alto Networks NGFW VM-Series
- Amazon EC2
- Amazon VPC (Route tables) <br />







## TASK2

## Background

Now we have blocked Sneaky Suds from stealing our recipes by exfiltrating data from the Beer Vault Server. We need to figure out how did Sneaky Suds managed to get to the Beer Vault in the first place.

Below is the updated AWS Network Diagram after making the previous changes

<br />
<p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/after-task-1.png" alt="Network Diagram2" width="1500" /></p>
<br />
Time to find out how BrewGuardians Webstore became the weak link in our brew chain. In the Firewall logs we only saw the Outbound logs from the Beer Store Data Database. Time to investigate! Let’s  redirect the East-West traffic between our web frontend and the database to the Palo Alto Networks Firewall to see what's going on.
<br />
<br />


## Task
1. First check on the Firewall if you can see any logs between the Beer Store Data Database Server and the Beer Store Frontend Webserver. You can add the following filter into the Firewall Monitor **( zone.src eq internal ) and ( zone.dst eq internal )**
2. After seeing no Logs in the Firewall, you recongize that you have to solve some AWS routing issues. For any help, see Clue 1
3. After solving the AWS routing, you should be able to see the following Monitor Logs inside the Firewall
<p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/task2-ssh-new.png" alt="SSH Logs" width="1500" /></p>
<br />

## Task Validation
- Once you made the appropriate changes in AWS check if can see now traffic between the **Beer Store Data Database Server** and the **Beer Store Frontend Webserver** by typing the following filter in the Firewall Monitor **( zone.src eq internal ) and ( zone.dst eq internal )**
- If you can see now the traffic between both EC2 Instances, type the Port number of the ssh application which is used for communication between both EC2 Instances.
<br />
<br />

## Helpful info
**How to see the Traffic Logs inside the Firewall**
- Login into the firewall
- Inside the Firewall, and Navigate to Monitor -> Traffic
- See the following picture as an example <p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/example.png" alt="Monitor Logs" width="1500" /></p>
<br />

## Inventory
- Palo Alto Networks NGFW VM-Series
- Amazon EC2
- Amazon VPC
- AWS Systems Manager (SSM)
- AWS Lambda
- AWS Tranist Gateway
- AWS Gateway Load Balancer <br />
<br />

## Services You Should Use
- Palo Alto Networks NGFW VM-Series
- Amazon EC2
- AWS Transit Gateway (TGW route tables) <br />








## TASK3

## Background

Well done on stopping the lateral propagation between the Beer Frontend Store and our Beer Store Data Database! Time to unmask how the Sneaky Suds exploited our Storefront.
We have to figure out how they could get into our Beer Frontend Server. For that, we have to make some more changes to our AWS Infrastructure. Below are the updated AWS Network Diagram after making the previous changes

<br />
<p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/after-task-2.png" alt="Network Diagram2" width="1500" /></p>
<br />
<br />


## Task
1. You realized that you have no Inbound inspection on the Beer Store by looking into the Firewall monitor logs and adding the following filter  **(( zone.src eq frontend ) and ( zone.dst eq frontend )) or (( zone.src eq external ) and ( zone.dst eq internal ))**. 
2. You should now redirect the traffic from the Beer Frontend VPC to the Firewall. For help see Clue 1
3. Now after you redirect inbound traffic through the Firewall, you should connect to the **Beer Store Frontend Webserver** (HTTP) over the Public IP. You should be able to see the following Webpage. This is the entrypoint to the Log4j Attack. The attack is being automatically generated, you do not need to authenticate to the beer store frontend before proceeding to the next step
   <p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/beerstore.png" alt="Beer Store" width="500" /></p>
   In the Firewall Monitor Logs, you should see the following log by entering the Filter ( addr.src in YOUR-PIP ). Replace "YOUR-PIP" with your local Public IP (Logs can take up to 1 min to be shown in the Monitor) <br />
   In case you still don't see any traffic logs, check the Internet Edge route table or check Clue 2
   <p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/task3-logs.png" alt="Logs" width="1000" /></p>
4. Next we will check the Firewall Monitor Threat logs, to see if any unexpected behaviour is happening. In the Threat Logs, you can see some **Log4j** Attacks. But for some reason, the Firewall isn't blocking them, just alerting. See the picture below as an example.
   <p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/alert-log4j-new.png" alt="alert" width="1000" /></p>
5. Now you have to change/update the Threat Profile on the Security Policy to block/reset the vulnerable traffic. To perform the change on the Security Policy follow the instructions below:
   1. On the Firewall tab on the browser, navigate to the Policies tab, and select Secuirty on the left pane
   <p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/pol1.png" width="1000" /></p>
   2. Now you can see all the Security rules. Click on the Rule **Allow inbound** frontend rule, and a new window will open
   <p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/pol2.png" width="1000" /></p>
   3. On the new window click on **Actions** tab
   <p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/pol3.png" width="500" /></p>
   4. On the **Profile Setting** section you can see that under **Vulnerability Protection**,the **alert** profile is selected. That profile will only alert and not block or reset any communication.
   <p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/pol4.png" width="500" /></p>
   5. Change the **Vulnerability Protection** from **alert** to **strict**.
   <p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/pol5.png" width="500" /></p>
   6. Click **OK**, and the window will close automatically.
   7. Next, we have to commit the changes you made to the firewall. Click on the **Commit** button in the top right corner.
   <p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/pol6.png" width="500" /></p>
   8. A new window will open. Here you will have to click on **Commit** button
   <p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/pol7.png" width="500" /></p>
   9. Wait for Status Complete and Result Successful and close the Window
   <p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/pol8.png" width="500" /></p>
6. After changing the **Vulnerability Protection** Profile from **alert** to **strict** you should see the following logs under the **Threat section** of the logs
   <p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/blocked-log4j-new.png" width="1000" /></p>
<br />
<br /> 

## Task Validation
- After you make the appropriate changes in AWS routing and on the Palo Alto Networks Firewall  it should have successfully blocked the attack to the **Beer Store Frontend Webserver** and you should be able to see a **reset-both** log entry in the Palo Alto Networks Monitor Logs -> Threat.
- If yes, type the Threat Name/ID of the Log4j Attack in the answer field (The answer is case sensitive).
<br />
<br />

### Helpful info
How to see the Threat Logs inside the Firewall
- Log into the firewall
- Inside the Firewall Navigate to Monitor -> Threat
- See the following picture as an example <p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/threat.png" alt="Monitor Threat Logs" width="1000" /></p>
<br />

## Inventory
- Palo Alto Networks NGFW VM-Series
- Amazon EC2
- Amazon VPC
- AWS Systems Manager (SSM)
- AWS Lambda
- AWS Tranist Gateway
- AWS Gateway Load Balancer <br />
<br />

## Services You Should Use
- Palo Alto Networks NGFW VM-Series
- Amazon EC2
- Amazon VPC (Route tables / Internet Gateway)
- AWS Gateway Load Balancer (endpoints) <br />
