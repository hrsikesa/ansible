# Name

Create AWS EC2 Instance and configure HTTPD Server on it via Ansible

## About project
This is the example of Ansible project that shows how we can launch AWS EC2 Instance via Ansible & how we can use Dynamic Inventory to configure HTTP Server on some specific(having same Tags)AWS EC2 Instance

## Usage
1. Install ansible on your manager machine
2. Install boto python library, so that ansible can communicate to AWS
   ```bash
   pip3 install boto
   ```
3. For Dynamic Inventory for AWS use dynamic-inventory folder(present in same repo) .
In this ec2.py containes that code that will fetch the IP's of EC2 Instances, So to connect with AWS its requires AWS Access Key & Secret Access Key.
Export these as enviorment variables or add them in ec2.ini .

   If we want to use Dynamic Inventory for some other service then refer to this 
[link](https://github.com/ansible/ansible/tree/stable-2.9/contrib/inventory)

4. Use ansible.cfg((present in same repo)file as Ansible configuration file.
   In it in **inventory** specify the path of inventory folder which contains both ec2.py & ec2.ini file.
   
   Run below command to verify whether our Dynamic Inventory is working or not.
   This will list all EC2 Instances in our AWS account.
     ```bash
   ansible all --list-hosts
   ```
5. Now to launch Ec2 instance via Ansible use ec2-launch role(present in same repo under roles).
   To launch Ec2 Instance we need AWS Access Keys & Secret Access Keys with sufficient premissions to create EC2 Instance.
 
   Use below command to encrypt AWS Access Keys & Secret Access Keys via Ansible Vault . Specify values of AWS Access Keys & Secret Access Keys in vars/main.yaml file under ec2-launch role directory.
   
   ```bash
   ansible-vault encrypt --vault-id vault@prompt  /etc/ansible/roles/ec2-launch/vars/main.yml 
   ```
   We are storing all our roles in /etc/ansible/roles.

   In ec2-launch role we have also added task  for refreshing the Inventory to ensure new instances exist in inventory

6. Use webserver role(present in same repo under roles) to configure HTTPD  Server on all EC2 Instances that have tag **webserver** attached.

7. Create a playbook that uses both the roles ec2-launch & webserver . 
   
   Playbook(launch_ec2-webserver.yaml) should look like this .
   ```bash
   - hosts: localhost
     roles:
       - role: ec2-launch

   - hosts: tag_name_webserver
     roles:
       - role: webserver
   ```
   
   To run playbook use below command as we also have to provide vault password . 
   Also Create .vault_pass.txt file in users home directory & in it specify password of our Vault.
     ```bash
   ansible-playbook launch_ec2-webserver.yaml --vault-id vault@~/.vault_pass.txt
   ```
   
## Contributing
Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

Please make sure to update tests as appropriate.
