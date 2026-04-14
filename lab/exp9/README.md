# Experiment 9: Ansible with Docker

## Overview
This project demonstrates how to use Ansible for automating server configuration using Docker containers as managed nodes. It showcases how multiple servers can be configured simultaneously using Ansible playbooks and SSH-based communication.

## Objective
To automate configuration and management of multiple servers using Ansible, reducing manual effort and ensuring consistency across systems.

## Problem Statement
Managing infrastructure manually across multiple servers leads to:
- Configuration drift  
- Inconsistent environments  
- Time-consuming repetitive tasks  
- Poor scalability  

This experiment addresses these issues using Ansible automation.

## Technologies Used
- Ansible (Automation Tool)
- Docker (Containerization)
- WSL / Linux Environment
- SSH (Secure Shell)

## Project Structure
.
├── Dockerfile
├── inventory.ini
├── playbook.yml
├── id_rsa
├── id_rsa.pub
└── README.md

## Prerequisites
- Docker installed and running  
- Ansible installed  
- WSL or Linux environment  

## Installation and Setup

### 1. Install Ansible
```bash
sudo apt update -y
sudo apt install ansible -y
ansible --version
```

### 2. Generate SSH Keys
```bash
ssh-keygen -t rsa -b 4096
```
Press ENTER for default settings (no passphrase).

### 3. Copy Keys to Project Folder
```bash
cp ~/.ssh/id_rsa .
cp ~/.ssh/id_rsa.pub .
```

### 4. Docker Setup (Dockerfile)
```dockerfile
FROM ubuntu

RUN apt update -y && \
    apt install -y python3 python3-pip openssh-server && \
    mkdir -p /var/run/sshd

RUN mkdir -p /run/sshd && \
    echo 'root:password' | chpasswd && \
    sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config && \
    sed -i 's/#PasswordAuthentication yes/PasswordAuthentication no/' /etc/ssh/sshd_config && \
    sed -i 's/#PubkeyAuthentication yes/PubkeyAuthentication yes/' /etc/ssh/sshd_config

RUN mkdir -p /root/.ssh && chmod 700 /root/.ssh
COPY id_rsa /root/.ssh/id_rsa
COPY id_rsa.pub /root/.ssh/authorized_keys
RUN chmod 600 /root/.ssh/id_rsa && chmod 644 /root/.ssh/authorized_keys

EXPOSE 22
CMD ["/usr/sbin/sshd", "-D"]
```

### 5. Build Docker Image
```bash
docker build -t ubuntu-server .
```

### 6. Run Multiple Containers
```bash
for i in {1..4}; do
  docker run -d -p 220${i}:22 --name server${i} ubuntu-server
done
```

### 7. Ansible Configuration (inventory.ini)
```ini
[servers]
server1 ansible_host=localhost ansible_port=2201
server2 ansible_host=localhost ansible_port=2202
server3 ansible_host=localhost ansible_port=2203
server4 ansible_host=localhost ansible_port=2204

[servers:vars]
ansible_user=root
ansible_ssh_private_key_file=~/.ssh/id_rsa
ansible_python_interpreter=/usr/bin/python3
```

### 8. Add SSH Hosts
```bash
ssh-keyscan -p 2201 localhost >> ~/.ssh/known_hosts
ssh-keyscan -p 2202 localhost >> ~/.ssh/known_hosts
ssh-keyscan -p 2203 localhost >> ~/.ssh/known_hosts
ssh-keyscan -p 2204 localhost >> ~/.ssh/known_hosts
```

### 9. Test Connectivity
```bash
ansible all -i inventory.ini -m ping
```
Expected Output:
server1 | SUCCESS => pong
server2 | SUCCESS => pong
server3 | SUCCESS => pong
server4 | SUCCESS => pong


### 10. Playbook (playbook.yml)
```yaml
***
- name: Configure servers
  hosts: all
  become: yes
  tasks:
    - name: Update packages
      apt:
        update_cache: yes

    - name: Install packages
      apt:
        name: ["vim", "htop", "wget"]
        state: present

    - name: Create test file
      copy:
        dest: /root/ansible_test.txt
        content: "Configured by Ansible"
```

### 11. Run Playbook
```bash
ansible-playbook -i inventory.ini playbook.yml
```

### 12. Verify Results
```bash
ansible all -i inventory.ini -m command -a "cat /root/ansible_test.txt"
```

### 13. Cleanup
```bash
for i in {1..4}; do docker rm -f server${i}; done
```

## Problems Faced and Solutions
1. **SSH Keys Not Found During Docker Build**
   - Error: `COPY id_rsa.pub not found`
   - Cause: Keys were not present in project directory
   - Solution: `cp ~/.ssh/id_rsa . && cp ~/.ssh/id_rsa.pub .`

2. **Wrong Key Name**
   - Cause: Keys generated with custom name
   - Solution: Recreate with default name `id_rsa`

3. **Inventory File Parsing Error**
   - Error: `Failed to parse inventory.ini`
   - Cause: Incorrect format
   - Solution: Correct inventory structure

4. **SSH Authenticity Prompt**
   - Error: `The authenticity of host can't be established`
   - Solution: `ssh-keyscan >> ~/.ssh/known_hosts`

5. **Only One Server Responding**
   - Cause: SSH trust not established for all servers
   - Solution: Add all ports using `ssh-keyscan`

6. **Working in Windows Directory (/mnt/c/)**
   - Issue: Permission problems
   - Solution: Use Linux home directory

## Result
- Successfully created multiple Docker containers as servers
- Established SSH connectivity
- Configured servers using Ansible
- Automated tasks across all servers simultaneously

## Conclusion
Ansible provides a powerful and efficient way to automate infrastructure management. It ensures consistency, reduces manual effort, and scales easily across multiple systems using a simple, agentless architecture.
