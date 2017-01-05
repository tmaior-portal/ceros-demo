# Ceros Demo Bootstrap Quick Guide

### Creating the Devices 

Log-in to Soflayer at http://control.softlayer.com on th etop menu bar click on Devices then Device List. On the top right corner click on Order Devices to create the machines.

Choose **Virtual Server (private node)** and **Hourly**. Choose desired device configuration and finish the order.

When the device is ready in the device list, click on the created device to get its Public IP Address on the current device dashboard. Click on the Passwords to get the root user's password.

### Access (* This step is required on all devices)

On the root folder of this repository find the files **key.pub** and **key.pem** and install it on the devices that will run the Ansible Playbooks with the following commands:

```shell-script
$ ssh-copy-id -i /path/to/key.pem root@DEVICE_IP
```
Log on to the server using the root's password or the installed ssh key and type the following to create required user to run the playbooks 

```shell-script
$ useradd ansible;
$ passwd ansible "DESIRED PASSWORD"  * **Optional step**
```
Next, we need to add this users to sudoers, type:

```shell-script
$ sudo visudo 
```
Find the following line on the file:

Add the following line to allow ansible to run sudo without asking for a password:

```shell-script
ALL     ALL=(ALL) NOPASSWD: ALL
```
Save and quit.

Add the key to the ansible user by typing:

```shell-script
$ ssh-copy-id -i /path/to/key.pem ansible@DEVICE_IP
```

### Bootstrap 

Checkout the code to the /home/ansible directory of the machine that will be used to run the Ansible playbooks.

Install Ansible with the following commands:

```
$ sudo yum update
$ sudo yum install ansible
```

Add the servers IP Addresses to the **hosts** file (all servers must have the a common key file installed) as shown bellow:

```
[consul_server]
CONSUL_SERVER_IP

[swarm_manager]
SWARM_MANAGER_IP

[swarm_node]
SWARM_WORKER_NODE_IP

[jenkins]
JENKINS_SERVER_IP
```

Now, run the playbook using the following command:

```shell-script
$ ansible-playbook bootstrap-cluster-yml 
```

Wait for the output and check the if the services where installed:

Consul:
```shell-script
http://CONSUL_SERVER_IP:8500
```
Jenkins:
```shell-script
http://JENKINS_SERVER_IP
```
This playbook creates two services in the swarm cluster: nginx-consul-template and gliderlabs/registrator. Microservices will be installed using Jenkins. The playbook also automatically configure the following jobs on Jenkins:

- hello-world
