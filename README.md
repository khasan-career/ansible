📌 Ansible 3-Tier Architecture Automation Project

📖 Overview
This project provisions a 3-Tier Web Application Architecture using Ansible from scratch.

It automates the configuration of:
✅ Web Layer – Apache/Nginx servers.
✅ Application Layer – App servers with business logic.
✅ Database Layer – MySQL with secured root password (via ansible-vault).

The setup demonstrates Infrastructure as Code (IaC) principles with a secure, scalable design.

📌 Architecture Diagram
- (Control Node + 3 Managed Nodes: Web, App, DB)

📌 Project Structure
ansible-lab/
├── inventory.ini         # Static inventory with 3 managed nodes
├── site.yml              # Main playbook
├── roles/
│   ├── webserver/
│   │   ├── tasks/main.yml
│   ├── appserver/
│   │   ├── tasks/main.yml
│   ├── database/
│   │   ├── tasks/main.yml
├── group_vars/
│   ├── all.yml           # Common variables
├── vault.yml             # Encrypted MySQL root password

📌 Prerequisites
- VMWare VM's + Ubuntu OR AWS EC2 Instances.
- 1 Control Node, 3 Managed Nodes (Web, App, DB).
- SSH Key-Based Authentication enabled.

Ansible installed on Control Node:
# sudo apt update && sudo apt install ansible -y

📌 Setup Steps
🔑 1. Generate SSH Key & Copy to Managed Nodes
ssh-keygen -t rsa
# ssh-copy-id zsysadmin@192.168.110.63   # Webserver
# ssh-copy-id zsysadmin@192.168.110.64   # Appserver
# ssh-copy-id zsysadmin@192.168.110.65   # DBserver

📂 2. Create Inventory
inventory.ini
[webservers]
192.168.110.63

[appservers]
192.168.110.64

[dbservers]
192.168.110.65

🔐 3. Secure Variables with Vault
ansible-vault create vault.yml
Inside vault.yml:
vault_mysql_root_password: "MySecurePass123"

📜 4. Main Playbook
site.yml
- hosts: webservers
  roles:
    - webserver

- hosts: appservers
  roles:
    - appserver

- hosts: dbservers
  roles:
    - database
      
🗄️ 5. Database Role Example
roles/database/tasks/main.yml
- name: Install MySQL Server
  apt:
    name: mysql-server
    state: present
  become: yes

- name: Change MySQL root authentication plugin to mysql_native_password
  mysql_user:
    name: root
    host: localhost
    password: "{{ vault_mysql_root_password }}"
    login_unix_socket: /var/run/mysqld/mysqld.sock
    plugin: mysql_native_password
    state: present
  become: yes

📌 Execution
Run the playbook:
# ansible-playbook -i inventory.ini site.yml -u zsysadmin --ask-vault-pass

📌 Verification
✅ Web Layer:
# curl http://192.168.110.63

✅ App Layer:
Check service running via SSH to appserver.

✅ Database Layer:
# mysql -u root -p

📌 Security Best Practices
- No hardcoded passwords, used Ansible Vault.
- become: yes used only where needed.
- Separate roles for modularity.

📌 Future Enhancements
- Integrate CI/CD with GitHub Actions or Jenkins.
- Deploy on AWS EC2 and RDS.
- Add load balancer and auto-scaling.

📌 Author
👤 Kamrul Shuhel
