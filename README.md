Hii..
Ansible is an open-source automation tool used to manage, configure, and deploy systems, applications, and infrastructure — all through simple, human-readable YAML playbooks.
Kindly follow repository for best practises.
Now we have one important concept in Ansible which is vault : Ansible Vault is used to securely encrypt sensitive data (like passwords, keys, or secrets) so it can be safely stored and shared inside playbooks or repositories.
To Create: ansible-vault create secrest.yaml 
ansible_pass=1234jio
Use in command : ansible-playbook -i host -v  playbook.yaml --ask-vault-pass



All in one script:
-----------------------------------------------------------------------------------------------------
playbook.yml
-----------------------------------------------------------------------------------------------------
---
- name: creating diractories and files using vault
  hosts: IOT
  gather_facts: yes
  become: yes
  vars_files:
    - var.yml
  tasks:
    - name: check connectivity
      ping:

    - name: Printing some debuging facts
      debug:
        msg: " {{ ansible_default_ipv4.address }} : {{ ansible_hostname }} server host os family is {{ ansible_os_family }}"

    - name: check mem and disk space
      shell: |
        free -g | awk 'NR==2{print $4}'
        lsblk -b -o NAME,SIZE | awk 'NR>1 {sum += $2} END {print sum/1024/1024/1024}'
      register: sh_output

    - name: Print the output of shell commands
      debug:
        msg: "{{ sh_output.stdout_lines }} The free space is {{ sh_output.stdout_lines[0]}} GB and Total disk space: {{ sh_output.stdout_lines[1]}}"

    - name: Create Dir
      file:
        path: /tmp/files
        state: directory
        mode: 0770
      when: sh_output.stdout_lines[1] | float > 500
    - name: Create file
      file:
        path: /tmp/files/file.txt
        state: touch
        mode: 0770
      when: sh_output.stdout_lines[1] | float > 100

    - name: Install some packages use when condition and print some debug msg
      yum:
        name: "{{ apache_pkg }}"
        state: present
      when: ansible_os_family == "RedHat"

    - name: writing code in html file
      shell: echo "<h2>Hello from KSK</h2>" | tee /var/www/html/index.html
      notify: restart service

  handlers:
    - name: restart service
      service:
        name: "{{ apache_service }}"
        state: restarted
      tags: handler

--------------------------------------------------------------------
var.yml
-------------------------------------------------------------------
apache_pkg: httpd
apache_service: httpd

