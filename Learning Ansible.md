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

`ansible myservers -m ping`

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