Terraform
============

Desired and contineous state.

It will check the actual state and compare with desired state. This make sure actual state and desired state always matches.

Infrasturcutre as a code
========================

IAC

The above desired state is achieved through the code and a tool. That why its called as Infra as a code.

This desired state is stored in a file in Gitlab. We are go back to the past desired state if reqd. So its versionable ad repeatable and testatble.

Testable - We can test it in different state and push it  to the next enviroment

Can be used in both cloud and onprime , other such tools in market are , Cloudformation, Azure resource manager , Pulumi

Terraform
___________

To identify which cloud resource , it uses a provider.

Installation:

We can install terraform in 2 ways , one we can install through a linux pkg , if we need any specific version of terraform , we can download the specific zip file and extarct it
and install it.

Terraform need access key and secret key to access to any cloud platform.

1st way of access is : we can set the env variable with the linux command 
 
#export AWS_ACCESS_KEY_ID=XXXXXXXXXXXXXXXXXXXXXXXXXXXX
#export AWS_SECRET_ACCESS_KEY=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX

problem with this is once the session is terminated , terraform will loose access to the server.

2nd way is as below.

we need to install the AWS CLI pkg on the linux server and configure and store the terraform credentials on this.

Install the AWS cli on the linux machine
#curl <url> to download the zip 
#apt install unzip && unzip awscliv2.zip
#./aws/install --bin-dir /usr/bin --install-dir /usr/bin/aws-cli --update
#aws --version

Now configure AWS and give the credentials (access id, secret key)

#aws configure

HCL 

-All terraform file should be saved in *.tf
-blocks {}
- every line is called as statement
-key = value

Provider Block
----------------- >> to tell terraform where is our cloud

syntax:
provider "providername" {
 key= value
}

Resource Block
--------------- >> what is our desired state e.g ec2 , sql or as such
syntax:
resource ""provider_resourcetype" "name" {
 key= value
}

Example:

#mkdir -p terraform/basics
#cd terraform/basics
#vi main.tf

provider "aws" {
 region = "ap-south-1"
}

resource "aws_instance" "example" {
 ami = "ami-xxxxxxxxxxxxx"		>> get this from aws ec2 instance
 instance_type = "te.micro"
}

Terraform Workflow
-------------------
-terraform init		>> read the .tf file & download the plugins we need  >> plugin downloaded in .terraform/providers/plugin
-terraform validate	>> if syntax are correct or not
-terraform plan		>> it will read .tf file , check the desired state and compare with actual state
	+ indicates resource creation
	- indicates resource deletion
	+/- resource recreation
	~ resource modification
-terraform apply 	>> it will apply the .tf file 
	#terraform apply -auto-approve	>> it will not ask us again wheather to proceed with apply or not
-terraform show
	#terraform show  >> it will show all the details of the instance created by terraform
-terraform destroy	>> it will destroy the instance created by terraform , but it will ask you which one to delete among all
        #terraforn destroy -target "aws_s3_bucket.example"	>> it will delete the exact resource

Interpolation: PROVIDER_RESOURCETYPE.RESOURCENAME.ATTRIBUTES


TAINT:
------

If its marked as taint means , it will forcibly recreate the resouce , in next immideate execution of plan or apply

#terraform taint <resourcename>		>> it will destroy and recreate the same resource forcibly

#terraform untaint <resourcename>	>> to untaint any resource

in newer version of terraform we can use below for taint

#terraform apply -replace=<resourcename> -auto-approve 		>> it will do the same work as taint


Terraform Dependecies
-----------------------

first parent should get created , then the child resource.

While destroying we need to destroy the child first then the parent.

we need to interlink the dependency tothe parent , like first we need to create the VM then the elastic IP (constantIP)

Example: this is implicit dependecy example

provider "aws" {
	region = "ap-south-1"
}

resource "aws_eip" "ip" {
	instance = aws_instance.example.id		>> here the id will be fetched once the VM is created
}

resource "aws_instance" "example" {
	ami = "ami-xxxxxxx"
	instance_type = "t2.micro"
}


Explicit dependency , we will create the dependency , although terraform don't have.
e.g - lets say we will create s3 bucket first then write something to it then create the vm and attach it . for this we use "depends_on = <resourcename>"

Example: explicit 

provider "aws" {
	region = "ap-south-1"
}

resource "aws_instance" "example" {
	ami = "ami-xxxx"
	instance_type = "t2.micro"
	depends_on = [aws_s3_bucket.example]
}

#create s3 bucket

resource "aws_s3_bucket" "example" {
	bucket = "wezvaxxxxxxxxxxx"
}


TFSTATE File :
----------------	>> *.tfstate will be created current directory

1.Once the terraform apply is done , then it will create a state file to store what are the configuration changes its has done.

2.If we want to know the actual state , we can get that from this statefile.

3.state file will be automatically created on this current directory. So logically project should have its own statefile in different locations.

4.for actual state , Terraform show will read from tfstate file and show it on terminal.

5.If we change anything in a current state , tfstate will be backed up as tfstate.backup and a new tfstate will be created , same happenes again if we change anything again.

6. We need to store the statefile somewhere in common location , or else some other associate may create something , with out knowing the actual state. 

  creating a remote tfstate 
we need to create a "backend block". practically storing a statefile we can have a s3 bucket.

example:

vi backend.tf
terraform{
	backend "s3" {
	 bucket = "wezva-xxxxxxxx"
	 key = "pathwhere the provider cloud key is stored"
	 region = "ap-south-1"
	}
}

N:B- So logically with main.tf we need to create a backend.tf for remote location storing of the tfstate file


Terraform Import:
--------------------


1.If any resource is created in AWS manually , Terraform will not be able to know it. So in this case we need to import the same thing to terraform.

2. Import details of resources , which are managed outside the terraform.

3. First add respective resource blocks to your desired state.

4. Import the existing details into the state file.

#terraform import aws_instance.example2 i-xxxxxxxxxxxxx		>> machine ID for the newly created AWS instances

5. We need to import only one outside resource at a time , we can't do multiple ones in one line command.


Terraform Module:
--------------------

1.Collection of files and programme togather is called as a module.

2. In terraform module is a folder where diffrent tf file will be present, alongside with resource block, data block etc. SO rather than copy pesting the code each time 
we can create a new infra with this Module and can change it wherever its reqd.

so practically we can segrigate the tf file , such as main.tf for provider block and resource block 
variable.tf for variable related thing 
output.tf for output related.

3. If we need to create a input ask for any variable . we need to put the value of the variable as blank in the variable.tf file

example:

module "instance" {
  source = "../modules/instance"	>> in this case child module is avaible in two folder back in modules/instance
}



If we need to put one module output as an input to other moduke, we can define as below , but both the module should be in same .tf file

module "instance" {
  source = "../modules/instance"
}

module "eip" {

  source = "../modules/eip"
  instanceid = module.instance.id		>> module+instance+id here module is default , instance is the module name , id is the output.tf attribute, so it will take the out of first module as the second one.
}


Built in Functions:
--------------------

1. Its there for latest version of terraform , version 12 and above.

2. we can write the function through the below commands.

#terraform console 
> max(1,31,2)
31			>> it will give maximum value

>split("a","tomato")
tolist([
	"tom"
	"to" ,
])			>> it will split tomato from "a" 
>substr("hello world", 1, 4)
ello			>> it will cut the first letter , from there 4 letter

>index (["a", "b", "c"], "b")
1			>> here index number of a is 0 , b is 1 , c is 3, apart from abc any othe thing it will give error 


+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++


Terraform certificate Preparation :


Terraform ref is the activity whioch will be performed once the terraform plan or apply is issues. it will check the difference between the actual and desired state and provide the input for the 
the given command.

However manually giving the terraform refresh will change the tfstate file it will be disatrerious, so better not give it explicitly.

N:B- Tfstate file has always a bcakup tfstate.backup which will be a rescuse if tfstate is changed or deleted.

Terraform ref is deprecated in the newer version of terraform i.e 0.15.4 , we can use refresh-only command for that.

if we don't need to give the aws credentials in the bprovider, we can mention the same thing in below , bkz if the credentials are not mentioned , it will search the same 
in below 

$HOME/.aws/config and $HOME/.aws/credentials

We can also save the credentilas using AWS CLI, which will store the credentials on the above location it slef , its easy approach if we are using the AWS platform

install AWS CLI the 
#aws config 
AWS ID:
Secret Key:

then restart the terminal and we can give the terraform commands with out giveing the credentials, it should work 












