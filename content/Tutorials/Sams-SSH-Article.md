# SSHelling into the VPC through the Bastion  
Hopefully this will take some of the guess work out of using SSH to access resources on the VPC through the bastion. I will demonstrate how to add the bastion key with an additional key for some resource in our cloud. Any resource we would like to access will have an associated key and we will need that key to ssh into it. The instructions scale, so it is no different for 3 or 9000+ keys.  

## Sanity check  
First you will want to make sure that  
- You have ssh up and running on your machine. I believe a quick   
`sudo apt install ssh-server`  
will do the trick on Ubuntu.  
- You have the appropriate keys in a directory somewhere that you can access on your local machine (I keep them in the directory for COMP350).  
- Make sure your keys are appropriatly named. I will be using the Bastion.pem, and Webapp.pem keys.     

## _You got all that?_  
Let's get to it. (**TL;DR** Scroll to the bottom)  


1. You need to open a terminal and change directory to directory containg the `.pem` keys  
`$ cd <DirectoryWithPEMFiles>`  
2. We need to change the permissions of both keys. Since these are security keys meant to authenticate, it makes no sense to allow anyone else _any_ priveleged access (and ssh will not use a key if it doen't maintain the correct priveleges). In Linux there are 3 types of user for a file or directory- the file owner, the file's group, and then the rest of the world. We only want the owner (us) to access, so for this we will use 400. That is read access only for the owner.  
`$ chmod 400 Bastion.pem`
`$ chmod 400 WebApp.pem`  
or if you think you're slick try  
`$ chmod 400 *.pem`  
(hackerman!)
3. Now we need to add these keys to our agent. First take a look at what is already there  
`$ ssh-add -L`  
This will list the keys the agent is holding.  
4. Now add the keys we need  
`$ ssh-add -k Bastion.pem`  
`$ ssh-add -k WebApp.pem`  
5. Take a look so you know what it looks like to add a key  
`$ ssh-add -L`  

6. Now we SSHell in  
`$ ssh-add -A ec2-user@theIPOfTheBastionHost`
There's a couple things to note here - first, the `-A`. This tells the Bastion and SSH that we have other keys we want to use. The second, `ec2-user` is  not a filler. That is the default login on all instances.  
7. You may be prmopted with a warning, accept it. You see it every time you SSH into a machine for the first time.  
8. From the bastion host  
`$ ssh ec2-user@theIPOfTheWebApp`  
You will see the same warning from earlier, reply Y.  
You have experince the SSHell.

**TL;DR**  

`$ cd <DirectoryWithPEMFiles>`  
`$ chmod 400 Bastion.pem`  
`$ chmod 400 WebApp.pem`  
`$ ssh-add -L`  
`$ ssh-add -k Bastion.pem`    
`$ ssh-add -k WebApp.pem`  
`$ ssh-add -L`  
`$ ssh-add -A ec2-user@theIPOfTheBastionHost`  
`$ ssh ec2-user@theIPOfTheWebApp`  

