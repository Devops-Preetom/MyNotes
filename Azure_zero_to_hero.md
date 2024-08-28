Azure Zero to Hero :

Day01:
------

Cloud computing prerequisite
-----------------------------

Hybrid cloud = pvt cloud + public cloud	>> sensitive date in private cloud (owned by themself) 

Multi cloud = some services from aws + some services from azure etc

Hypervisor is a software which  implements the concept called virtualization.

virtualization - we can break a server to multiple servers logically, to utilize maximum of the resources.

The cloud provider will have the same virtualization and hypervisor technique, they have already few servers unallocated. when u raise a request it will provide u one free. All the cloud provider used the same technique to provide millions of servers in a day.

For using any app e.g azure or fb we can use UI, CLI or API , generally developers or administrators use the programmatic way UI or API. Scenario is below
for creating a vm in azure 
UI > we can use the UI to create a vm 
However for creating 1000 vms its a tidious task, so we can use the API method here. MS will provide a API , which can be called by a shell script to build 1000 VMs.

Regions > its consist of multiple availability zones. cloud provider segregate to avoid any natural calamity or power outage.  e.g US east and US west. US West again consist of multiple availability zones. which are fairly situated from each other. If incase one AZ is down other in the same region will be available. we can use load balancer to segregate the load of same infra in to two availability zones.

Load Balancer: It will have a mechanism where we will configure hoe many traffic any VM will take. If in case one VM is down, LB is capable of sending the respective traffic to another vm in LB infra.

Auto scaling- we will tell Azure platform that if one VM is occupied , then scale up another vm , if in case that is also occupied eventually , then scale up a new one.

Two types of auto scaling is there, Horizontal scaling and vertical scaling. 

Another perspective of types of scaling is manual scaling and auto scaling. Here auto scaling is also known as elasticity.

Disaster recovery or DR - its a technique where we need to have a plan or action, if something goes wrong.  e.g storing a backup or replica in another region DC.


Day02:
------

Creating Account with azure:

1. Create account with Git hub 

create free tier account , 13300 credit for first 30 days (can be used for Realtime projects)

Each Data Centre is nothing but a availability zone.

If users are from particular continent lets say Germany, France and London , we will setup the infra at Europe region only to avoid the latency.


IAAS PAAS and SAAS:
-------------------

Infra as a service	> vm , storage, network 

Platform as a service   > MySQL DB (only the service)

software as a service	> Mail services like outlook (everything else like deployment app will taken care by azure, just pay the subscription and use)


Day03:
------

Once we deploy a service in azure , it will be called  resource. e.g if we deploy a sql or vm service , it will become resource.

once we gave all the details in any create template, it will all went to Azure resource manager. Then resource manger will create a resource as per the requirements.

All the req inputs weather through UI, API, CLI it will go through the resource manager for the deployments.

RG: Resource Group:
------------------

Its a grouping of resources. Can be VM with DB VNET etc etc

why should we group resources > We can track resources, As a devops engg we have to support multiple teams requirement. With the help of group it will be easier to manage access, auditing, cost, monitoring, permissions , security etc

How you are managing the resource in your environment ?

We are using RG per project , one for project , one for transaction etc, we use this to control the cost, monitoring etc.

One resource can only tagged to one resource group. 1:1


Day04:
------

Azure VM type & deploying Jenkins on Azure VM:
----------------------------------------------

what is azure spot discount tab in vm create template ?

These are some of the unused resources , if we opt for that we will get some discounts. but catch is these resources can go down at any moment of time. can only be used for dev env.

Types of series of VM:

A series - for dev or test purposes , it will discontinue on aug 2024

B series - for free trial use cases only , or learning the azure platform, 1cpu, 1gb ram, economical burstable series.

D series - General purpose application.

E series - if memory intense application , 

F series - Gaming or high compute application

H Series - High performance application

L Series - High storage optimized server. DB related VM series. Read and write very fast

N Series - Running machine learning or lar scale app.

We can talk with support and ask for the advice.

Login method - ssh pvt key 

during the create we need to download the pvt key. 

Two ways to connect it 

1. Azure shell (not a recommend way)

2. Git bash or putty 

check Abhishek video of how to connect to ec2 on windows

then

ssh -i /path/to/pvtkey azureuser@publicipaddr

if the permission error came then give #chmod 600 /path/to/key		>> bkz 777 permission is not a good practice


Deploying Jenkins on Azure VM:
------------------------------

# sudo apt update
# sudo apt install openjdk-11-jre
# java -version
# curl -fsSL <link for jenkins>
# sudo apt-get update
# sudo apt-get install jenkins
# ps -ef | grep jenkins		>> it will show if its running along with on which port
give the http://public_ipaddr:port		>> it will open the jenkins url

before that allow port 8080 in network setting.

add an inbound rule :
source : any
port range: any 
destination: ipaddr of vm
service: custom
port: 8080
protocol: any
action: allow
priority: 400

Virtual machine scale set: VMSS :
---------------------------------

It will do auto scaling.

If dynamic traffic coming to application, depending on the load balance it will auto scale and descale the number of VMs. so if we have an unexpected traffic we can plan the server on VMSS.

 
Day05:
-----


Azure Vnet:
-----------

Virtual Network (Vnet)...why its introduced ?

Lets say 2 competators Puma and nike has requested for same config VM in same location, and somehow they got both got same host. If in case the nikes server is compromised or hacked , its quite possible that hacker can penatrate in to puma server too using the same network. So vnet is introduced. If nike has requested one vnet azure will provide an vnet on one host , and that is reserved only for nike, even if puma server comes in the same host, its not possible to get in to nike server using network anymore.

this is how one customer data is secured from others in public cloud.

for one org we can create multiple vnets depending upon the projects.

How to define size of the vnet ? 

The number of ipaddr need to be created (ip addr is equal to no of vm , or db etc)

It can be achieved by CIDR e.g /16 = 65536 ip addresses

How Vnet works from inside ?

Lets say in one Vnet there is a web server, app server and a DB. so connection requirement is different for diff resource here. like web server should be connected from internet , however app server should be connection by web logic and db too. here we need to segregate web server a part, app server a part and db a part, which is called as subnets , so that all will be isolated from each other.

	webserver subnet can be provided a rule for allowing the internet from outside, but app and db server are restricted with ssh. This rule can be applied through NSG.

	Here NSG is a part inside the subnet, either can put all the resources inside the subnet as one nsg or instance to instance also we can create multiple nsgs. depending upon the requirement.

What is ASG ?

Application security group. In real time we use NSG + ASG to more secure the env. ASG is one step ahead of NSG . Lets say in previous example we allow the CIDR of web subnet in NSG of DB subnet. but with ASG we can even pick one or few app in the web subnet and allow them to DB subnet. so that web instance in web subnet will be completely isolated from the db subnet. this we can do by futher segrigation of the subnets.

Route Table ?
A way through which the traffic flows from one subnet to another.

Buffer Class for Networking Concept:
------------------------------------

IP ADDR - ranges - 0-255.0-255.0-255.0-255

why 255 > bkz its 8 bytes = 2^7 + 2^6 + ...... + 2^0 = 255

Subnet > segrigating one network in multiple parts > e.g in a free school network finance and admin are in subnets , if incase the students network get compromised it can't compromise the devices of finance and admins.

CIDR > it will give a range for the different subnets > e.g 192.16.4.0/24

192.16.4.0/24 > why 24 > --------|--------|--------|0-255 > so 1st 3 tabs are static means 192.16.4.0-255 , so cancelling 3 , 8 bits data = 8+8+8 = 24

192.16.4.0/31 > why 31 > 8+8+8+7 > 32 - 31 = 1 = 2^1 = 2 > so only two ipaddr can be assigned 

192.16.4.0/16 > 32-16 = 16 = 2^16 = 65536 > this many ips can be assigned 

Pvt Ip Addr are starting with 172/192/10 , bkz generally the others like 8.8.8.8 is pvt dns for google like wise others too, which will create a conflict.

Ports > these are the concept through which we can bind an application to a ipaddr > 172.4.9.8:8080

OSI model > 


DAY06:
------

Azure Advanced Network :
------------------------

In a subnet how the traffic flows is managed by route tables and it flows through system routes.

Generally like a society compound wall , if u want to enter it should be allowed by main gate security then the block security then the navigation to reach the particular house. Like wise in azure , compound wall is vnet then the main gate security is firewall which will protect the complete set up , inside the subnets will be there like blocks of society then the block security is the NSG which will allow finally to check in.

in the above example the app gateway and a load balancer is setup. where the app gateway will host the load balancer , so once the internet search finds our app ip then it will hit the load balancer of our app gateway and it will decide in which server to route the traffic.

App gateway will have L7 traffic 

Load balancer will have L4 traffic 

L7 traffic > it will decide on a broader view where the traffic should route. e.g amazon.com/login , amazon.com/payment , amazon.com/prime etc are hosted on different VMs, so seeing the request the App gateway will decide where to route the traffic.

L4 traffic > it's decision are very few, like which VM to send traffic if the other VM is busy.


VNET Peering & VNET gateway :
-----------------------------

VNET peering : Lets say two servers are there one is for nike sales another is for nike billing , both are in different vnets . so for a efficient purchased cycle both the vnets should have a peering with each other. so that both could communicate with each other. that's called vnet peering. the gateway used here is known as vnet gateway.

VPN gateway: 
------------

Lets say our office network needs a direct connect to one of a onprime data centre, that can be done through a VPN gateway connection.


DAY07:
------

AZURE NETWORKING DEMO :
-----------------

Bastion : its a via media through which we can connect to a pvt ip address from the outside world.

Ddos network protection : its a method through which we can protect our resource if a hacker is sending millions of falls request to block or slow down our network or auto scale mis managemnet.

Bastion will have a IAM policy. Through which we can see who is accessing the vm through bastion.

How to connect through BASTION ?

select the bastion option from the left side pane of the VM , then give the username and the ssh public key and connect.




While creating the VM there is an advanced option, where custome data field and user data field option are there.

Custome Data: Here we can give the cloud init script , it will get triggered during the build, e.g if we put a script of nginx and html installation, automatically during the build nginx and html will be installed. this generally used during the auto scaling server, so that when new servers came up it will be provisioned with the desired software also.

User Data: If we want to pass some files or data inside the vm.

Install nginx inside the VM through apt-get install nginx -y 

As per the requirement if someone accessing the firewall ip with port ipaddr:4000 it will redirect to the vm. to do that do the config as below

go to firewall service. > select firewall policy in left pane > select DNAT (Network address translation) > select priority > add rule > 

give the user's ip or range of ip - SOURCE IP
Give the firewall ip 	- DESTINATION IP		public IPADDR of firewall
TCP - RULE
4000 here - PORT
Pvt ip of VM - TRANSLATED IP
port of nginx 80 - TRANSLATED PORT

Once the DNAT is rule is created use the firewallip:4000 in the browser, we will be able to get to the nginx





priority in the above is the number which varies from 100 - 65536 , which ever is having the least number is having the more priority. Lets say two engineers one allow on traffic one denies the traffic, who is having the least priority will be executed.


Day 08 :
--------

Networking Interview questions:
-------------------------------

Difference between NSG and ASG 
------------------------------

There are no such difference in NSG or ASG , ASG will work along with NSG to cater the security. 
We can create a ASG with different vm or tags , rather than mentioning multiple IP we can give the ASG name in the NSG , it will allow only those VMs which are part of the ASG. 
Advantage is : manual addition it tedious and to keep it updated addition and deletion is a tedious task. 

Can u block access to a subnet inside a common Vnet ?
---------------------------------------------------

N:B In one common vnet, all the subnet can communicate with each other.

The above NB has a priority number for inbound rules which allows all the traffic inside the vnet. We need to create a deny rule in which the priority will be lesser than the priority of the previous rule. then it will work. Lesser the number highest is the priority. 

Are NSG are stateful or stateless:
----------------------------------

yes its stateful.

stateful means: only the inbound traffic is allowed not the outbound traffic. Lets a say one user is sending a request to a vm to access internet in port 80. its allowed as in inbound. However the result of the request will not go back to the user . or he will not be able to see any http page. as outbound is blocked. we need to open it to see the.

Stateless means: both the inbound and the outbound is blocked. We need to open it to have the access.

wheather the diff between azure firewall and NSG :
------------------------------------------------

Firewall : Typically associated with the inbound and the outbound traffic for a vnet.

NSG: Typically associated with a subnet or individual network interface to manage the traffic inside a vnet or with in vnets.

What are the advantages of RG:
------------------------------

1 Logical organisation
2 Life cycle management
3 Resource group tagging 
4 Role based access control 
5 cost management 
6 resource group template
7 resource locks

What is diff between azure user data and custom data:
-----------------------------------------------------
custom data : while a vm is spined up with the help of custom data we can install the required app on the server during the build itself. lets say with VMscalset we are auto scaling the no of VMs. But these need to have some set of application too, so we can use the custom data and install the required app during the build itself.

user data: its a advanced version of custom data. Here a particular start up script will run every time the server is rebooted and during the spin up too. We can update the script anytime. but that can't be done in custom data as the custom data vanishes post the build.

Difference between azure app gateway and azure load balancer:
-------------------------------------------------------------

app gateway - L7 , which can perform https host based routing. it will intercept the url at the beginning e.g if its www.xyz.com/login it will go to particular webserver, if its www.xyz.com/logout it will go a different webserver.


LB - L4 if its a 3 tier app , 1st tier is web server , 2nd is app servers , 3rd is db. Generally the L4 layer is after L7 layer, it will be there for app server balancing.(backend application)

Scenario based, assume your comp is using idea azure networking setup and application is deployed in the web subnet. 
-------------------------------------------------------------------------------------------------------------------
explain the traffic flow to the application.
-------------------------------------------

so as per the interviewer , there is a Vnet and a web subnet. how a user will reach to the web server.

1. There will be a firewall setup where only the authorize users only are allowed rather than the hackers.
2. post firewall will set up a NAT rule. users will only be able to access
3. in application will set up a NSG rule. so even if the firewall is bypassed , it will be get blocked in firewall.
4. will set up an different Bastion too, if any other team need to access the server they can do ssh.

what is the purpose of azure bastion and what is the advantages of bastion:
---------------------------------------------------------------------------

1. secure remote access
2. elimination of public internet exposure.
3. reduced attack surface.
4. Bastion integration.
5. simplified connectivity
6. portal based access
7. RBAC - we can manage who will have the access
8. multi factor authentication
9. audit and monitoring


Day09:
------

Azure Storage service and used Cases:
-------------------------------------

There are four storage services

1. Blob
2. file
3. tables
4. queues

Before creating any of the above thing we need to first create a storage account , then we can create the services and manage those.

search for storage account and create it , give the subs and RG and give the account name and it should be unique.

Blob storage : we can store any unstructured data like video , music , code, file etc etc (like s3 in aws)

File storage : we can store here and provide the access to multiple pods or vms ( EFS in AWS )

Tables : NoSQL kind of DB where semistrictured data like data related to a employee or frequent purchases , commerce related

Queue : its for an application which serves multiple request, before processing it should be stored in queue. here its used. ( sqs in aws )

Creating Blob storage > go to your storage acc > container (blob) > create > and hurray you can upload 

Creating a file share > storage acc > file share > create > browse > upload > connect > windows/Linux > showscript > then we are onboarded.

creating a queue > storage acc > queues > create

same as tables too 

Storage Browser 
---------------

we can go to the storage browser and can access multiple storage services which we have created. We don't have to go to each and every browser.

Static website 
--------------

We can create a static website with this , we just need to upload a html file, it will provide the endpoint for access. 

Security : we can create a encryption for the data inside the storage , we can provide Azure AD for RBAC too.


Day10:
------

AZURE CLI :
-----------

Why should one use this instead of console:


We can automate repetitive task using azure CLI , ARM templates or Terraform

In CLI its easier to find the errors and store it.

search for azure cli documentation in google , click the first link.

As per your OS check the steps and install it.

for verification we can use #az version

then to login > #az login  & sign in 

go back to the az cli document 

check the learn azure cli > article list A-Z 

lets say we need to create azure vm then az vm we can search and copy the bash command.

e.g

# create Bash shell variable
vmName=TutorialVM1

az vm create \
  --resource-group $resourceGroup \			# we need the rg group, we can search in same way for the command to create the RG 
  --name $vmName \
  --image Ubuntu2204 \
  --vnet-name $vnetName \				#likewise create different parameters through the azure cli command & pass the parameter here.
  --subnet $subnetName \				# creating a subnet throughthe azure cli command
  --generate-ssh-keys \
  --output json \
  --verbose 						# to give the output in verbose format

az group delete 

az storage account 


Day 11:
--------

ARM Template:
--------------
Azure resource manager template:
--------------------------------

Lets say we build a vm by the below methods

cli
gui
terraform
bicep
sdk
etc 
etc

it will all go through a API layer called resource manager to build the vm. thats called ARM. and again there are some rule set to it , 

ARM templates are written in JSON 

for writing this we need the visual studio , then search for ARM tools and install it.

if you are writing something for creating a vm, we can select the auto pop up option and all the details will be populated, we can change it as per the requirement.

then open the terminal in the visual studio and copy the command to create with the template option , give the template details and run it , it will be created. (make sure to az login before issuing any command)





































