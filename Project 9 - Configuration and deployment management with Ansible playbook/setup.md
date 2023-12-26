Configuration and deployment management with Ansible playbook


set up 3 instances running Amazon linux OS
one will be Ansible_master and the other two will be Ansible_target 1 and 2

ssh to the master instance

install ansible on machine with command
```sudo yum install ansible```

verify it is installed with ```ansible --version```

Create a copy of private key used to ssh to the machine on the master instance
The aim of adding this key is so that ansible can use it to ssh into the other instances from the host when it needs to run a command remotely

To do this, copy the content of the key file on the local machine and paste in a similar file on the master instance

run this command 
```
vim key.pem
```
paste the content in the file then ```:wq``` to save the content and exit

set permissions for the key with this

```chmod 400 key.pem```

Next, we create the inventory file
```
vim inventory.txt
```
paste this content in the inventory to declare variables needed by ansible to access the target machines

```
ansible-target-1 ansible_host=51.20.114.103 ansible_connection=ssh ansible_user=ec2-user
ansible-target-2 ansible_host=16.171.240.216 ansible_connection=ssh ansible_user=ec2-user
```
Add the ssh key to the ssh-agent using this command
```
ssh-agent bash
cp key.pem ~/.ssh/
ssh-add ~/.ssh/key.pem
```


Try a ping from the inventory to a target machines
```
ansible ansible-target-1 -m ping -i inventory.txt
```


