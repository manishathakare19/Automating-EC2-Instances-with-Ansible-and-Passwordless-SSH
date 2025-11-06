# 🚀 AWS EC2 Automation Using Ansible

## 📋 Project Overview
This project demonstrates how to **create and manage AWS EC2 instances** using **Ansible**.  
It automates instance creation, sets up **passwordless SSH**, manages **inventories**, and runs **conditional operations** like shutdown via playbooks.

---

## 🧩 Project Goals
- Launch multiple EC2 instances (Ubuntu)
- Configure passwordless SSH from control node to all managed nodes
- Build and manage an Ansible inventory
- Execute operational playbooks (e.g., shutdown)
- Learn automation fundamentals for DevOps beginners

---

## ⚙️ Step 1: Launch EC2 Instances via Ansible
**Playbook: `ec2_create.yml`**

```yaml
- name: Create EC2 instances
  hosts: localhost
  connection: local
  gather_facts: false
  vars_files:
    - aws_credentials.yml

  tasks:
    - name: Launch EC2 instances
      amazon.aws.ec2_instance:
        name: "{{ item.name }}"
        key_name: "lll"
        instance_type: t2.micro
        security_group: default
        region: us-east-1
        aws_access_key: "{{ ec2_access_key }}"
        aws_secret_key: "{{ ec2_secret_key }}"
        network:
          assign_public_ip: true
        image_id: "{{ item.image }}"
        tags:
          environment: "{{ item.name }}"
      loop:
        - { image: "ami-0360c520857e3138f", name: "manage-node-1" }
        - { image: "ami-0360c520857e3138f", name: "manage-node-2" }
        - { image: "ami-0360c520857e3138f", name: "manage-node-3" }
✅ Key learning: Automating instance creation saves time and ensures consistency.

🔐 Step 2: Passwordless SSH Setup
Allows Ansible to run commands/playbooks without entering a password or PEM file every time.

bash
Copy code
ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa
cat ~/.ssh/id_rsa.pub | ssh -i ~/lll.pem ubuntu@<managed_node_ip> \
'mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys'
ssh ubuntu@<managed_node_ip>  # Test login
⚠️ Tips:

Don’t generate keys on managed nodes

Use the correct username (e.g., ubuntu)

Correct permissions → chmod 600 ~/.ssh/authorized_keys

📁 Step 3: Create Ansible Inventory (hosts.ini)
ini
Copy code
[ubuntu_nodes]
manage-node-1 ansible_host=3.235.91.83 ansible_user=ubuntu
manage-node-2 ansible_host=44.201.22.7 ansible_user=ubuntu
manage-node-3 ansible_host=13.219.250.218 ansible_user=ubuntu

[all_nodes:children]
ubuntu_nodes
🧠 Step 4: Test Connectivity
bash
Copy code
ansible -i hosts.ini all -m ping
✅ Expected output: pong from all nodes.

🔌 Step 5: Shutdown Playbook (shutdown.yml)
yaml
Copy code
- name: Shutdown all Ubuntu instances
  hosts: all_nodes
  become: true
  gather_facts: true

  tasks:
    - name: Shutdown nodes
      ansible.builtin.command: /sbin/shutdown -h now
      ignore_errors: yes
🔹 become: true → root access
🔹 ignore_errors: yes → avoids failure when SSH disconnects during shutdown

🧾 Step 6: Lessons Learned
SSH key management is critical — generate only on control node.

Inventory grouping simplifies management.

Ansible tasks require proper permissions.

OS differences matter (Ubuntu vs CentOS).

Always test connectivity before running playbooks.

Automation saves time and reduces errors.

🏁 Step 7: Project Outcome
✅ Successfully launched 3 EC2 instances
✅ Configured passwordless SSH
✅ Built Ansible inventory for grouped management
✅ Ran conditional shutdown playbooks
✅ Learned AWS EC2 + Ansible automation + SSH practices

💡 Tips for Beginners
Start with one node before scaling up

Keep SSH keys and PEM files organized

Always document IPs and hostnames

Use ignore_errors: yes for shutdown tasks

Match OS and username properly


