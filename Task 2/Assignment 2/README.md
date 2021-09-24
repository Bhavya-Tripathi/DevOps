# ANSIBLE GIT EXAMPLE

### Referred article: https://www.middlewareinventory.com/blog/ansible-git-example/

## STEP 1
Follow the instructions given in the documentation to install ansible in your system.  
Installation guide:
https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html

## STEP 2
Create a personal access token in Github.   
See this:  
[How to create a Personal Access Token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token)
This token will be used to login to Github.

## STEP 3
Create a simple nodejs app and upload it in a private repository on Github.  
[How to create a simple nodejs application](https://expressjs.com/en/starter/hello-world.html)

## STEP 4
Store the github username and token in a file using `ansible-vault`
1. `$ ansible-vault create secrets.yml` this command will prompt you to create a Vault Password. After creating and confirming the password, the secrets.yml file will open.
Insert the following in that file:
> gituser: [INSERT GITHUB USERNAME]  
> gitpass: [INSERT PERSONAL ACCESS TOKEN]
(Note: these instructions are also present in the article)

## STEP 5
Create an EC2 instance on AWS.   
[How to create and EC2 instance](https://docs.aws.amazon.com/efs/latest/ug/gs-step-one-create-ec2-resources.html)
Use the ssh client to connect to the instance.

## STEP 6
In your project directory create an inventory called ansible_hosts.  
Add the following code in the file:  
> [nodeserver] <br>
> PUBLIC IP ADDRESS HERE ansible_user=ubuntu ansible_port=22 ansible_ssh_private_key_file=<GIVE Path to private key file here>

> [nodeserver:vars]<br>
> ansible_ssh_common_args="-o StrictHostKeyChecking=no"

Here [nodeserver] is the name of your hostgroup, you can give it any other name also.

## STEP 7
Create an ansible playbook `gitexample.yml`. The playbook is in the article as well as in my repository.
Change the following variables in the playbook:.
  
In the "Change the ownership of the directory part" change the owner to the name of your ec2 instance. For me it was `ec2-user`, for you it can be something else if you chose a platform other than Redhat.<br> 
[Here is a list of default usernames](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/connection-prereqs.html)
