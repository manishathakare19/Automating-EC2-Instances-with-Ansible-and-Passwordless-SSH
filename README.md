🔐 Step 2: Passwordless SSH Setup

Allows Ansible to run commands/playbooks without entering a password or PEM file every time.

ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa
cat ~/.ssh/id_rsa.pub | ssh -i ~/lll.pem ubuntu@<managed_node_ip> \
'mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys'
ssh ubuntu@<managed_node_ip>  # Test login


⚠️ Tips:

Don’t generate keys on managed nodes

Use the correct username (e.g., ubuntu)

Set proper permissions → chmod 600 ~/.ssh/authorized_keys

📁 Step 3: Create Ansible Inventory (hosts.ini)
[ubuntu_nodes]
manage-node-1 ansible_host=3.235.91.83 ansible_user=ubuntu
manage-node-2 ansible_host=44.201.22.7 ansible_user=ubuntu
manage-node-3 ansible_host=13.219.250.218 ansible_user=ubuntu

[all_nodes:children]
ubuntu_nodes

🧠 Step 4: Test Connectivity
ansible -i hosts.ini all -m ping


✅ Expected output: pong from all nodes.

🔌 Step 5: Shutdown Playbook (shutdown.yml)
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

SSH key management is critical — generate only on control node

Inventory grouping simplifies management

Ansible tasks require proper permissions

OS differences matter (Ubuntu vs CentOS)

Always test connectivity before running playbooks

Automation saves time and reduces errors

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

🧰 Tech Stack

Tools & Technologies Used:
AWS EC2 • Ansible • YAML • SSH • Linux (Ubuntu) • Automation
