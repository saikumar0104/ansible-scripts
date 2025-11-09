Hii..
Ansible is an open-source automation tool used to manage, configure, and deploy systems, applications, and infrastructure — all through simple, human-readable YAML playbooks.
Kindly follow repository for best practises.
Now we have one important concept in Ansible which is vault : Ansible Vault is used to securely encrypt sensitive data (like passwords, keys, or secrets) so it can be safely stored and shared inside playbooks or repositories.
To Create: ansible-vault create secrest.yaml 
ansible_pass=1234jio
Use in command : ansible-playbook -i host -v  playbook.yaml --ask-vault-pass
