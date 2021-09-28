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
[How to create and EC2 instance](https://docs.aws.amazon.com/efs/latest/ug/gs-step-one-create-ec2-resources.html) <br>
Remember while creating the instance, add a custom TCP rule with the port number you have given in your node app. You can also edit the rules afterwards if you haven't added it while creating the instance.
Use the ssh client to connect to the instance.

## STEP 6
In your project directory create an inventory called ansible_hosts.  
`$ cd project_folder` <br>
`$ touch ansible_hosts` <br>

Now open the ansible_hosts in a text editor and add the following code in the file:  
(change ansible user to your default user of the ec2 instance you created [Here is a list of default usernames](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/connection-prereqs.html))
> [nodeserver] <br>
> PUBLIC IP ADDRESS HERE ansible_user=ubuntu ansible_port=22 ansible_ssh_private_key_file=<GIVE Path to private key file here>

> [nodeserver:vars]<br>
> ansible_ssh_common_args="-o StrictHostKeyChecking=no"

Here [nodeserver] is the name of your hostgroup, you can give it any other name also.

## STEP 7
Create an ansible playbook `gitexample.yml`. The playbook is in the article as well as in my repository.
Change the following variables in the playbook:.
  
1. In the "Change the ownership of the directory part" change the owner to the name of your ec2 instance. For me it was `ec2-user`, for you it can be something else if you chose a platform other than Redhat.<br> 
[Here is a list of default usernames](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/connection-prereqs.html)<br>
2. In "validating the port is open" task change the port number to the port number you used in your node app.
3. In "Download the NodeJs..." task change the path name to the path of your own private repository.

## STEP 8
Check if the hostgroup is reachable or not: <br>
 `$ ansible nodeserver -m ping -i ansible_hosts` <br>
Now run the playbook using the following command: <br>
 `$ ansible-playbook gitexample.yml --ask-vault-pass` <br>

## STEP 9
After the playbook runs successfully, open your app in the browser by typing this:  
  `http://<Public ip address>:<portnumber>` <br>

  
