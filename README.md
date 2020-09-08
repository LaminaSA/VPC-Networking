# VPC---Networking



# Creating a VPC

Check you region is Ireland

Go to the main AWS Services webpage, and type in VPC it should pop up

Click on Your VPCs

Click Create VPC 

    Enter a Name that stands out 
    
    Enter a CIDR Block " 000.000.000.000/0" = This is the format you want it in, depending on how many Octets you use the number after the slash will be: 8-16-24-32
    
    Enter No for IPv6 
    
    Tenancy is default
    
Click create and your VPC is created!



# Create a internet gateway

Navigate to the VPC area within AWS

On the left sidebar, scroll down till you find internet gateway and click it 

Once inside, click create internet gateway 
    
    Enter a Name that stands out 

Click next and you have now created a Internet gateway!

Click on your gateway, navigate to the actions and click attach to VPC 
    
    Attach to the VPC you created earlier - Just type the name in 

You have now attached your Internet gateway to your VPC!

# Creating Subnets - Private and Public 

Navigate to the VPC area within AWS

On the left sidebar, scroll down till you find Subnets and click it 

We will begin with a Private Subnet first 

Once in, click create Subnet 
    
    Enter a Name that stands out, as your Private Subnet
    
    Attach your VPC 
    
    Availability zone is left as - No Preference
    
    Ipv4 CIDR block - needs to be same as the VPC id with the 3rd octet being different - with the addition of the 3rd octet the / will be 24 as we are using 3 octets
    
You have now created a Private subnet! 


Now the Public one!

Once in, click create Subnet 
    
    Enter a Name that stands out, as your Public Subnet
    
    Attach your VPC 
    
    Availability zone is left as - No Preference
    
    Ipv4 CIDR block - needs to be same as the VPC id with the 3rd octet being different - with the addition of the 3rd octet the / will be 24 as we are using 3 octets - 
    add 1 to the Private one you made earlier as it will be easier for you to read and understand!
    
You have Now Created a Private and Public Subnet!


# Creating Route Tables

Navigate to the VPC area within AWS

On the left sidebar, scroll down till you find Route Tables and click it 

We will begin with a Public Route Table first 

Click on Create Route Table 

    Enter a Name that stands out
    
    Attach your VPC 
    
    Add a Tag - Key is Name - Value is the Name you created 
    
Click Create, You have now have a Public Route Table!

You will see that you now have a blank name Route table, this will be your Private Route Table
    
    Add a Name that stands out

You have now have a Private Route Table!   

Now to add routes to your Public Route Table
    
    Look at the splash box below
    
    Click on Routes 
    
    For Destination = 0.0.0.0/0 - Target = Add your Internet Gateway - Click Save Routes


# IMPORTANT 

Add a Internet Route for your Private Table, this is so you can download the packages for your Database Instance - 
but you need to remove afterwards otherwise the whole point of a VPC is Pointless

    Look at the splash box below
    
    Click on Routes 
    
    For Destination = 0.0.0.0/0 - Target = Add your Internet Gateway - Click Save Routes


Adding Subnet Associations for your Route Tables 

Click on your Public Route Table
    
    Click on Subnet Associations
    
    Click on Edit Subnet Associations
    
    CLick on your Public Subnet to add it 
    
    Click Save

You have added the Public Subnet to your Public Route Table 

# Network ACL 

Navigate to the VPC area within AWS

On the left sidebar, scroll down till you find Network ACLs and click it 

You will find already created for you , this is your Private Network ACL 

    Rename it to something easy to remember

We will create a Public Network ACL 

Click on Create Network ACL 

    Name - Enter a Name that stands out
    
    VPC - Add yours that you created

On your public Network ACL 

Look at the splash box below 
    
    Navigate to Subnet associations
    
    Click Edit Subnet associations
    
    Click on your public subnet and add it to the Network ACL 
    
    Click Edit
    
You have now added your public Subnet to your public Network ACL!

Your private Network ACL will update its Subnet associations accordingly


Editing Inbound/Outbound Rules for Private Network ACL
    
Look at the splash box below 
    
    Navigate to Inbound Rules
    
    Click Edit Inbound Rules
    
    Add rule - Rule = 100 - Type = Custom TCP - Protocol = TCP 6 - Port Range = 27017 - Source = Public Subnet IP - Allow
    
    Add rule - Rule = 110 - Type = SSH - Protocol = TCP 6 - Port Range = 22 - Source = Public Subnet IP - Allow
    
    Add rule - Rule = 120 - Type = Custom TCP - Protocol = TCP 6 - Port Range = 1024 - 65535 - Source = Public Subnet IP - Allow
    
    Click Save
    
    Navigate to Outbound Rules
    
    Click Edit Outbound Rules
    
    Add rule - Rule = 110 - Type = All Traffic - Protocol = TCP 6 - Port Range = All - Source = 0.0.0.0/0 - Allow
    
Editing Inbound/Outbound Rules for Public Network ACL
    
Look at the splash box below 
    
    Navigate to Inbound Rules
    
    Click Edit Inbound Rules
    
    Add rule - Rule = 110 - Type = HTTP - Protocol = TCP 6 - Port Range = 80 - Source = 0.0.0.0/0 - Allow
    
    Add rule - Rule = 120 - Type = HTTPS - Protocol = TCP 6 - Port Range = 443 - Source = 0.0.0.0/0 - Allow
    
    Add rule - Rule = 130 - Type = Custom TCP - Protocol = TCP 6 - Port Range = 1024 - 65535 - 0.0.0.0/0 - Allow
    
    Add rule - Rule = 140 - Type = SSH - Protocol = TCP 6 - Port Range = 22 - Source = Your IP - Allow
    
    Add rule - Rule = 150 - Type = Custom TCP - Protocol = TCP 6 - Port Range = 27017 - Source = Private Subnet IP - Allow
    
    Add rule - Rule = 160 - Type = Custom TCP - Protocol = TCP 6 - Port Range = 1024 - 65535 - Source = Private Subnet IP - Allow
    
    Click Save
    
    Navigate to Outbound Rules
    
    Click Edit Outbound Rules
    
    Add rule - Rule = 110 - Type = All Traffic - Protocol = TCP 6 - Port Range = All - Source = 0.0.0.0/0 - Allow


# Creating the EC2 Instances for your WebApp, Database and Bastion


# WebApp

Go to the main AWS Services webpage, and type in EC2 it should pop up

Click on running Instances 

We will configure the WebApp

Click Launch instance
    
    Choose AMI
    
        Scroll down to the Ubuntu 16.04 Image, select it 
    
    Choose Instance type
        
        Choose t2 micro
    
    Configure Instance
        
        Network - Choose your VPC 
        
        Subnet - Choose your Public Subnet 
        
        Auto assign Public IP - Enable it
        
    Add Storage
        
        No need to add Storage
        
    Add Tags 
    
        Key - Put Name, Value - Put your Instance name 
        
    Configure Security Group 
        
        Security Group Name - Add a easier to remember name 
        
        Add rules - SSH - Port Range = 22 - Source = 0.0.0.0/0
        
        Add rules - Custom TCP Rules - Port Range = 27017 - Source = Database Private IP goes here
        
        Add rules - Custom TCP Rules - Port Range = 0 - 65535 - Source = Your public IP 
        
        Add rules - HTTP - Port Range = 80 - Source = 0.0.0.0/0 
    
    Click review and launch 
    
    Click launch 
        
        For the Key Pair, add what you have created or been given, in my case it was KeyName.pem
        
        Click Launch Instance

# Bastion

Go to the main AWS Services webpage, and type in EC2 it should pop up

Click on running Instances 

We will configure the Bastion 

Click Launch instance
    
    Choose AMI
    
        Scroll down to the Ubuntu 16.04 Image, select it 
    
    Choose Instance type
        
        Choose t2 micro
    
    Configure Instance
        
        Network - Choose your VPC 
        
        Subnet - Choose your Public Subnet 
        
        Auto assign Public IP - Enable it
        
    Add Storage
        
        No need to add Storage
        
    Add Tags 
    
        Key - Put Name, Value - Put your Instance name 
        
    Configure Security Group 
        
        Security Group Name - Add a easier to remember name 
        
        Add rules - SSH - Port Range = 22 - Source = Your IP Here 
        
        Add rules - SSH - Port Range = 22 - Source = Public Subnet
        
        Add rules - Custom TCP Rules - Port Range = 27017 - Source = Public Subnet
        
        Add rules - HTTP - Port Range = 80 - Source = 0.0.0.0/0 
    
    Click review and launch 
    
    Click launch 
        
        For the Key Pair, add what you have created or been given, in my case it was KeyName.pem
        
        Click Launch Instance
        
        
# Database

Go to the main AWS Services webpage, and type in EC2 it should pop up

Click on running Instances 

We will configure the Database 

Click Launch instance
    
    Choose AMI
    
        Scroll down to the Ubuntu 16.04 Image, select it 
    
    Choose Instance type
        
        Choose t2 micro
    
    Configure Instance
        
        Network - Choose your VPC 
        
        Subnet - Choose your private Subnet 
        
        Auto assign Public IP - Disable it - This is for your Database so you done need a Public IP 
        
    Add Storage
        
        No need to add Storage
        
    Add Tags 
    
        Key - Put Name, Value - Put your Instance name 
        
    Configure Security Group 
        
        Security Group Name - Add a easier to remember name 
        
        Add rules - SSH - Source = Bastion Security group goes here 
        
        Add rules - SSH - Source = Your IP Address 
        
        Add rules - Custom TCP Rules - Port Range = 27017 - Source = Bastion Security group
        
        Add rules - Custom TCP Rules - Port Range = 27017 - Source = WebApp Security group
    
    Click review and launch 
    
    Click launch 
        
        For the Key Pair, add what you have created or been given, in my case it was 
        
        Click Launch Instance
        
        
# Provisioning the Web App Instance

Now we have created our network,, we need to populate the servers with data 

To copy files across to the WebApp 
    
    Scp moves the file into the virtual machine. Synching OS to app folder in VM
    
        scp -i ~/.ssh/ -r ~/Documents/aws/app/ ubuntu@WEBAPP IP HERE:/home/ubuntu/

    Syncing provision folder to vm
    
        scp -i ~/.ssh/KeyName.pem -r ~/Documents/aws/environment/app/provision.sh ubuntu@WEBAPP IP HERE:/home/ubuntu/

    Secure tunnel into the virtual machine on the EC2 instance
        
        ssh -i ~/.ssh/KeyName.pem ubuntu@WEBAPP IP HERE -i $ ./provision.sh
      
# Error When provisioning the WebApp  

The Error i encountered here 
    
    -bash: ./provision.sh: /bin/bash^M: bad interpreter: No such file or directory

To solve this you need to run the command 
    
    sudo apt-get install dos2unix
    
    dos2unix provision.sh  - Changes it to the correct format to allow it to be run 
    
The Error i encounter in installing dos2unix was:
    
    unable to resolve host ip-182-128-2-220

To solve this cd into the root using 
    
    cd ../..
    
    cd into etc 
    
    sudo nano hosts
    
    add below the localhost line - 127.0.0.1 ip-182-128-2-220 - space them out so that they are the same 
    
    cd back to root
    
    sudo apt-get update
    
    sudo apt install dos2unix
    
This solves the above problems 

# Back to provisioning the web app 

Now run the command
    
    ./provision.sh 
    
If a Pop-up box displays select
    
    keep local versions - click yes 
    
get the IPv4 address of your web app and put it into your browser 

Congrats, your web app on port 80 is working!


# Provisioning the Database 

To do this you must move your DevOps ssh into the bastion server 
    
     scp -i ~/.ssh/KeyName.pem KeyName.pem ubuntu@BASTION IP GOES HERE:/home/ubuntu/.ssh/

Once this is done, ssh into the Bastion server 
    
    ssh -i ~/.ssh/KeyName.pem ubuntu@BASTION IP GOES HERE

Once you are in the bastion server, check if you have the DevOpsStudent.pem file 
    
    ls - you should see the .ssh folder
    
    cd .ssh 

Now ssh to the Database from the bastion using
    
    ssh -i ~/.ssh/KeyName.pem ubuntu@DATABASE IP GOES HERE  

Once inside the Database create your provision file and mongod.conf file 
    
    touch provision.sh
    
    touch mongod.conf
    
nano into them and add the required data

chmod the provision.sh
    
    chmod +x provision.sh
    
Once this is done
    
    run ./provision.sh 
    
This will install mongodb 

# Error Provisioning the database

An Error I ran into 
    
    unable to resolve host ip-182-128-1-95

To solve this cd into the root using 
    
    cd ../..
    
    cd into etc 
    
    sudo nano hosts
    
    add below the localhost line - 127.0.0.1 ip-182-128-1-95 - space them out so that they are the same 
    
    cd back to root
    
    sudo apt-get update
    
    sudo apt install dos2unix

An Error I ran into 
    
    no connection from bastion to database 
    
To solve this - change Private NetworkACL subnet ip from private to public 
    

# All together now - Getting posts to run 

You have now provisioned the WebApp and the Database, now to get posts working you must do the following 

    sudo reboot 
On both the WebApp and Database server

Once this is done, ssh into your Bastion, then into your Database 

Once inside run 
    
    sudo systemctl start mongod

The green light should display - this indicates that the DB is running 

Now SSH into the WebApp and 
    
    cd app
    
    npm install
    
    node app.js  

This will link the webapp to the db and will seed the database 

Go to your WebApps IPv4 address /posts and it should be Running!


Congratulations, you have created a VPC! 