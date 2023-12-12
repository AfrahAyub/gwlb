
------
TASK 1

## Background

You started the journey by conducting a comprehensive audit of the existing AWS infrastructure. With a discerning eye, you created a detailed diagram of the AWS environment.

<br />
<p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/diagram-new.png" alt="Network Diagram" width="1500" /></p>
<br />
Your Application development team found some very strange behaviour on the Beer Vault Server and asked you to have a deeper look into it to figure out what's going on. They suspect that our competitor “Sneaky Suds” is exfiltrating our secret recipes. After some investigations, you immediately realized that no outbound traffic gets analyzed by the Palo Alto Networks Firewall. That's something that we have to fix. <br />
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
<br />

## Task
1. Login to the Firewall at first. (**see Helpful info**)
2. Check the traffic logs verify if you can see any traffic from the Beer Vault Server. (**see Helpful info**()
3. Let's redirect the Beer Vault VPC traffic for outbound inspections by changing the AWS routing.
4. Check the Firewall Monitor logs to see the logs from the Beer Vault VPC.
5. You should see the following example log in the firewall monitoring. By adding the following filter **( zone.src eq internal ) and ( zone.dst eq external )** <br />
   Some fields in the example log were removed.
<p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/task1-deny2.png" alt="VPC Logs" width="1500" /></p>
<br />

## Task Validation
- Once you made the appropriate changes in the AWS routing you can log into the **Beer Store Data Database Server** via the SSM service and test with the **curl** command if the EC2 instance has still internet access 
  - example curl command **sudo curl www.google.de** 
- If the curl command isn't working in the **Beer Store Data Database Server**, check the Palo Alto Networks Firewall Monitor Logs to see which Application get's now blocked from the Firewall. Type the Application Name in the answer field. <br />
<br />

## Helpful info
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
  - Security VM-Series (Palo Alto Networks Firewall)

###############################CLUES#######################################

Clue 1
Did you checked the Route Table?

You should check if the Route Tables of the Beer Store Data VPC is pointing to the correct Ressource 

Clue 2
Traffic still not showing in the Firewall?

If you still can't see the traffic in the Firewall monitoring. Please do the following:
1. Login into the AWS console
2. Go to VPC
3. Select in **Filter by VPC** field the "Beer Store Data VPC"
4. As next go to Route Tables and select the **Beer Store Data Private** route table
5. In the route table click on **Routes** (see below)
   <p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/task1-routes.png" alt="Clue2" width="500" /></p>
6. Click Edit routes and do the following changes:
   1. Remove the route 10.0.0.0/8 -> Target TGW
   2. Change the route 0.0.0.0/0 -> TGW
   3. click Save
7. Once you made the changes your routle should looks like the example below
   <p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/task1-clue2-new.png" alt="Clue2" width="500" /></p>


########################################################################################################################
TASK 2

## Background

Now we have blocked Sneaky Suds stealing our receipies by exfiltrating data from the Beer Vault Server. We need to figure out how did Sneaky Suds managed to get to the Beer Vault in the first place.
Below is the updated AWS Network Diagram after making the previous changes

<br />
<p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/diagram2-new.png" alt="Network Diagram2" width="1500" /></p>
<br />
Time to find out how BrewGuardians Webstore became the weak link in our brew chain. In the Firewall logs we only saw the Outbound logs from the Beer Store Data Database. Time to investigate! Let’s  redirect the East-West traffic between our web frontend and the database to the Palo Alto Networks Firewall to see what's going on.
<br />
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
<br />

## Task
1. First check on the Firewall if you can see any logs between the Beer Store Data Database Server and the Beer Store Frontend Webserver. You can add the following filter into the Firewall Monitor **( zone.src eq internal ) and ( zone.dst eq internal )**
2. After seeing no Logs in the Firewall, you recongized that you have to solve some AWS routing issues. For any help, see Clue 1
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

###############################CLUES#######################################

Clue 1
Did you looked into the TGW routing?

1. Login into the AWS console
2. Go to VPC Services and select under Transit Gateways the **Transit gateway route tables**
   <p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/task2-tgw-rt.png" alt="TGW RT" width="1000" /></p>
3. Select the **Spoke TGW Route Table**
4. In the Route table click on Propagations
   <p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/task2-tgw2.png" alt="TGW RT" width="1000" /></p>
5. Select each propagations one by one and click Clete propagations. Repeat it until both are deleted.
6. Your TGW Route table should looks like the following after the deletion
   <p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/task2-clue1.png" alt="TGW RT" width="1000" /></p>

Clue 2
Can't find the logs inside the Firewall Monitor?

1. Log into the Palo Alto Networks VM-Series Firewall
2. Go to Monitor -> Traffic
   <p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/example.png" alt="Monitor Logs" width="1500" /></p>
3. In the Filter field paste the the following filter **( zone.src eq internal ) and ( zone.dst eq internal )  and ( app eq ssh )**
   <p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/task2-filter.png" alt="Monitor Logs" width="1500" /></p>

Clue 3
Last Clue will provide the answer

1. In the Monitor logs use the same filter as in Clue 2 and have a look at the column **TO PORT**
   <p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/task2-clue3.png" alt="clue 3" width="1000" /></p>

########################################################################################################################


Task 3

## Background

Well done on stopping the lateral propagation between the Beer Frontend Store and our Beer Store Data Database! Time to unmask how the Sneaky Suds exploited our Storefront.
We have to figure out how they could get into our Beer Frontend Server. For that, we have to make some more changes to our AWS Infrastructure. Below are the updated AWS Network Diagram after making the previous changes

<br />
<p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/diagram3-new.png" alt="Network Diagram2" width="1500" /></p>
<br />
Well done on stopping the lateral propagation between the Beer Frontend Store and our Beer Store Data Database. Time to unmask how the Sneaky Suds exploited our Storefront.
<br />
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
<br />

## Task
1. You realized that you have no Inbound inspection on the Beer Store by looking into the Firewall monitor logs and adding the following filter  **(( zone.src eq frontend ) and ( zone.dst eq frontend )) or (( zone.src eq external ) and ( zone.dst eq internal ))**. You should now redirect the traffic to the Firewall
2. Now after you redirect inbound traffic through the Firewall, you should connect to the **Beer Store Frontend Webserver** over the Public IP. You should be able to see the following Webpage
   <p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/beerstore.png" alt="Beer Store" width="500" /></p>
   and in the Firewall Monitor Logs, you should see the following log by entering the Filter (replace YOUR-PIP with your Public IP) **( addr.src in YOUR-PIP )**
   <p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/task3-logs.png" alt="Logs" width="500" /></p>
3. Next we will check the Firewall Monitor Threat logs, to see if any unexpected behaviour is happening. In the Threat Logs, you can see some **Log4j** Attacks. But for some reason, the Firewall isn't blocking them, just alerting. See the picture below as an example.
   <p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/alert-log4j-new.png" alt="alert" width="500" /></p>
4. Now you have to change/update the Threat Profile on the Security Policy to block/reset the vulnerable traffic. To perform the change on the Security Policy follow the instructions below:
   1. On the Firewall tab on the browser, navigate to the Policies tab, and select Secuirty on the left pane
   <p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/pol1.png" width="500" /></p>
   2. Now you can see all the Security rules. Click on the Rule **Allow inbound** frontend rule, and a new window will open
   <p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/pol2.png" width="500" /></p>
   3. On the new window click on **Actions** tab
   <p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/pol3.png" width="500" /></p>
   4. On the **Profile Setting** section you can see that under **Vulnerability Protection**,the **alert** profile is selected. That profile wil lonly alert and not block or restet any communication.
   <p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/pol4.png" width="500" /></p>
   5. Change the **Vulnerability Protection** from **alert** to **strict**.
   <p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/pol5.png" width="500" /></p>
   6. Click **OK**, and the window will close Automatically.
   7. Next we have to commit the changes you made to the firewall. Click on the **Commit** button in the top right corner.
   <p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/pol6.png" width="500" /></p>
   8. A new window will open. Here you will have to click on **Commit** button
   <p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/pol7.png" width="500" /></p>
   9. Wait for Status Complete and Result Successful and close the Window
   <p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/pol8.png" width="500" /></p>
5. After changing the **Vulnerability Protection** Profile from **alert** to **strict** you should see the following logs under the **Threat section** of the logs
   <p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/blocked-log4j.png" width="500" /></p>
<br />
<br /> 

## Task Validation
- After you made the appropriate chenges in AWS routing and on the Palo Alto Networks Firewall should be successfully blocked the attack to the **Beer Store Frontend Webserver** and you should be able to see a **block** log entry in the Palo Alto Networks Monitor Logs - Threat.
- If yes, type the Threat Name/ID of the Log4j Attack in the answer field (The answer is case sensitiv).
<br />
<br />

### Helpful info
How to see the Threat Logs inside the Firewall
- Log into the firewall
- Inside the Firewall Navigate to Monitor -> Threat
- See the following picture as an example <p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/threat.png" alt="Monitor Threat Logs" width="500" /></p>


###############################CLUES#######################################

Clue 1

Did you checked the AWS route table?

1. Login into the AWS console
2. Go to VPC
3. Select in **Filter by VPC** field the "Beer Store Frontend VPC"
4. As next go to Route Tables and select the **Beer Store Frontend Public** route table
5. In the route table click on **Routes** (see below)
   <p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/task3-clue1-pup-rt.png" alt="Clue2" width="1000" /></p>
6. Click Edit routes and do the following change:
   1. Change the route 0.0.0.0/0 -> Gateway Load Balancer Endpoint
   2. click Save
7. Once you made the changes your routle should looks like the example below
   <p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/task3-clue1.png" alt="Task3 Clue1" width="1500" /></p>


Clue 2

Did you checked the Internet Route Table?

1. Login into the AWS console
2. Go to VPC
3. Select in **Filter by VPC** field the "Beer Store Frontend VPC"
4. As next go to Route Tables and select the **Beer Store Frontend IGW Edge** route table
5. In the route table click on **Routes** (see below)
   <p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/task3-clue2-igw-rt.png" alt="Clue2" width="1000" /></p>
6. Click Edit routes and do the following change:
   1. Add the route 10.1.2.0/24 -> Gateway Load Balancer Endpoint
   <p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/task3-clue2-igw-rt2.png" alt="Clue2" width="500" /></p>
   2. click Save
7. Once you made the changes your routle should looks like the example below
   <p><img src="https://aws-jam-challenge-resources.s3.amazonaws.com/panw-vmseries-gwlb/task3-clue2.png" alt="Task3 Clue2" width="1500" /></p>
