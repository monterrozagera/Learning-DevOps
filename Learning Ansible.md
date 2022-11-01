# Learning Ansible

Automation, infrastructure as code, software provisioning, configuration management, and application deployment.

- The system running Ansible is referred to as the **'Control Node'**
- Clientless - secure shell (SSH)
- Fleet management - AWX (graphical UI, open-source)
- Written in python, uses YAML for playbooks

**Playbook:** records and executes Ansible's configuration, deployment and orchestration functions.

**Plugin:** extends the functionality of what Ansible can do

**Role:** something developed by the community to execute within the Ansible environment

Playbook example:

```yaml
---
- name: Update web servers
  hosts: webservers
  remote_user: root

  tasks:
  - name: Ensure apache is at the latest version
    ansible.builtin.yum:
      name: httpd
      state: latest
  - name: Write the apache config file
    ansible.builtin.template:
      src: /srv/httpd.j2
      dest: /etc/httpd.conf

- name: Update db servers
  hosts: databases
  remote_user: root

  tasks:
  - name: Ensure postgresql is at the latest version
    ansible.builtin.yum:
      name: postgresql
      state: latest
  - name: Ensure that postgresql is started
    ansible.builtin.service:
      name: postgresql
      state: started
```

### Ad-hoc commands:

`ansible all -m ping`

## Hosts and Variables

```yaml
[southeast]
localhost			ansible_connection=local
other1.example.com	 ansible_connection=ssh		ansible_user=ubuntu	
```

Define `other1.example.com` to use ssh for remote control functionality with user ubuntu.

Within the inventory file, we can use IP addresses (0.0.0.0) or domain names (google.com).

### Subgrouping

```yaml
[east:children]
southeast
northeast
```

Creates a group named `east` and includes `southeast & northeast` in order to refer to them easily.

### Simulate servers for testing with local loopback addresses

```yaml
[logicservers]
server1		ansible_host=127.0.0.1		ansible_connection=local	deprecation_warnings=false
server2		ansible_host=127.0.0.2		ansible_connection=local	INTERPRETER_PYTHON=auto_silent
server3		ansible_host=127.0.0.3		ansible_connection=local
server4		ansible_host=127.0.0.4		ansible_connection=local
```





## Modules

eg. Module:

```yaml
- name: Find /var/log all directories, exclude nginx and mysql
  find:
    paths: /var/log
    recurse: no
    file_type: directory
    excludes: 'nginx,mysql'
```

Running in terminal (look for files in the Downloads directory):

`ansible localhost -m find -a "paths=Downloads file_type=file"`

Output:

```json
localhost | SUCCESS => {
    "changed": false,
    "examined": 1,
    "files": [
        {
            "atime": 1666904674.4950817,
            "ctime": 1666904674.4950817,
            "dev": 64768,
            "gid": 1000,
            "gr_name": "user",
            "inode": 1315940,
            "isblk": false,
            "ischr": false,
            "isdir": false,
            "isfifo": false,
            "isgid": false,
            "islnk": false,
            "isreg": true,
            "issock": false,
            "isuid": false,
            "mode": "0664",
            "mtime": 1666904674.4950817,
            "nlink": 1,
            "path": "/home/user/Downloads/randomfile.txt",
            "pw_name": "user",
            "rgrp": true,
            "roth": true,
            "rusr": true,
            "size": 0,
            "uid": 1000,
            "wgrp": true,
            "woth": false,
            "wusr": true,
            "xgrp": false,
            "xoth": false,
            "xusr": false
        }
    ],
    "matched": 1,
    "msg": "All paths examined",
    "skipped_paths": {}
}
```

## Remote Management

Ansible uses SSH to remotely control servers. In the server, copy the contents of `id_rsa.pub`, and append it to the `authorized_keys` file.

## Orchestration vs Automation

- Automation refers to a single task
- Orchestration refers to the management of many automated tasks
- Often a complicated ordering with dependencies

## Configuration Management

- Agentless
- SSH
- State Driven
- Idempotent  

```yaml
---
 - hosts: all
   become: yes
   name: "NTP Configuration"
   
   tasks:
   			- name: "Ensure NTP is installed"
   			  apt:
   			   name:
   			    - ntp
   			   state: present
   			   
   			- name: "Ensure NTP is started now and at boot"
   			  service:
   			   name: ntp
   			   state: started
   			   enabled: yes
 
```



## React to change with Ansible

- Scripts can be easily modified
- Structures like **handlers** exist for flexibility

```yaml
---
  - name: "Reac with Change example"
    hosts: webservers
    serial: 1
    
    tasks:
    
    - name: "Install nginx"
      debug:
        msg: "Install nginx on: {{ inventory_hostname }}"
        
    - name: "Upgrade nginx"
      debug:
        msg: "Upgrade nginx on: {{ inventory_hostname }}"
        
    - name: "Configure nginx"
      debug:
      	msg: "Start {{ inventory_hostname }}"
      notify: restart nginx
      changed_when: True
      
    - name: "Verify nginx"
      debug:
        msg: "Verify: {{ inventory_hostname }}"
        
    handlers:
     - name: restart nginx
       debug:
       	 msg: "CALLED HANDLER FOR RESTART" 
      	
```



If no change is made, the handler wont activate. `changed_when` will trigger a change. The `notify` variable will trigger the handler named `restart nginx` 

## Infrastructure Management

- Build Infrastructure from the ground up
- Manage physical Inf
- Automate virtualization
- Wide OS support
