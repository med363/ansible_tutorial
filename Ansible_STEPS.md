 ### Prerequis
 generation key ssh

```bash
ssh-keygen -t ecdsa -b 512 -f /tmp/keys-ssh
````

show pair keys
```bash
ls -la /tmp/keys-ssh
```

1ere methode
```bash
ll /tmp/keys-ssh
```
 IN MACHINE DISTANT
```bash
whami
```

### output 
amine
```bash
ssh-copy-id -i /tmp/keys-ssh amine@MACHINE_DISTANT(IP)
```

#2eme methode
#```bash
#ssh -i /tmp/keys-ssh MACHINE_DISTANT(IP)
#```

Establish connection
```bash
ssh  MACHINE_DISTANT(IP)
```

for no repeat create passphrase
```bash
eval $(ssh-agent)
ssh-add
```
Or we make alias
```bash
alias ssha='eval $(ssh-agent) && ssh-add'
```

To be persistant alias go to edit .bashrc and add
```bash
alias ssha='eval $(ssh-agent) && ssh-add'
```

Now we can connecte to remote machines
```bash
ssh MACHINE_DISTANT(IP)
```
------install ansible in machine of share keys

```bash
sudo apt update
sudo apt install -y ansible
```
create inventory (grp of MACHINE to manage)
@ ip MACHINES
```bash
nano inventory 
```
1ere methode
Make sure everything worked
```bash
ansible all --key-file <path_of_public_key> -i inventory -m ping
```
2eme methode
#### edit ansible.cfg----
```bash
[defaults]
inventory = inventory
private_key_file = ~/.ssh/ansible
```
And put
```bash
ansible all -m ping 
```

info of all MACHINES
```bash
ansible all -m grather_facts
```
Of one machine
```bash
ansible all -m grather_facts --limit MACHINE_DISTANT(IP)
```

Pass command with privellege
```bash
ansible all -m apt -a update_cache=true --become --ask-become-pass
```
Install some package with apt
```bash
ansible all -m apt -a "name=vim-nox state=latest" --become --ask-become-pass
```
---cree our playbook --
```bash
nano install_apache.yml
```

```bash
---
- hosts: all
  become: true
  tasks:
  - name: install apache2 package
    apt:
      name: apache2
 ```
Apply inventor file
```
ansible-playbook --ask-become-pass install_apache.yml`
```

---
remove apache2
```bash
---
- hosts: all
  become: true
  tasks:
  - name: install apache2 package
    apt:
      name: apache2
      state: absent
```


Other exemple :
IN /etc/ansible/hosts
```bash
[grps]
 @IPs
[misc:children]
 grps
 ```
 PING ALL
 ```bash
 ansible misc -m "ping"
 ```
 HOSTNAME FOR ALL
 ```bash
 ansible misc -a hostname
 ```
 SHOW MEMOIRE FOR ALL 
 ```bash
 ansible misc -a "df -h"
 ```
 
 SHOW FREE MEMOIRE FOR ALL 
 ```bash
 ansible misc -a "free -h"
 ```
 IN /etc/ansible/playbook/playbook.yml
 ```bash
 ---
- name: check connectivity
- hosts: master
  tasks:
  - ping: 

- name: Update Debian Linux packages
- hosts: misc
  become: yes
  gather_facts: yes
  tasks:
  - name: Update Debian Linux packages
    apt:
      upgrade: dist
      upgrade_cache: yes
    when: ansible_os_family == "Debian"

  - name: Clean up Debian Linux from cache 
    apt:
      autoremove: yes
      autoclean: yes
    when: ansible_os_family == "Debian"


  - name: Update Red Hat Linux packages
    yum:
      name: "*"
      state:latest
      upgrade_cache: yes
    when: ansible_os_family == "RedHat"


- hosts: misc
  become: yes
  tasks:
  - name: Install Some Package on Red Hat 
    yum:
      name: '{{item}}'
      state: latest
    with_items:
      - vim
      - git
      - zip
      - gzip
      - apache2
    when: ansible_os_family == "RedHat"

- hosts: misc
  become: yes
  tasks:
  - name: Install Some Package on Debian Linux 
    apt:
      name: '{{item}}'
      state: latest
    with_items:
      - vim
      - git
      - zip
      - gzip
      - apache2
    when: ansible_os_family == "Debian"

- hosts: misc
  become: yes
  tasks:
   - name: zip multiple files
     archive:
       path:
        - /home/vagrant/file1.txt
        - /home/vagrant/file2.txt
       dest: /home/vagrant/files.zip
     format: zip





- hosts: grp2
  become: true
  tasks:
  - name: Create a file
    file: path=/home/vagrant/f.txt state=touch

- hosts: grp2
  become: true
  tasks:
  - name: Create multiple file
    file: path={{item}} state=touch
    with_items:
    - '/home/vagrant/file1'
    - '/home/vagrant/file2'

- hosts: grp2
  become: true
  tasks:
  - name: Copy content to a file
    copy: content="<h1>some important info<h1>" dest=/home/vagrant/index.html


- hosts: grp3
  become: true
  tasks:
  - name: Create a group
    group: name=amine

- hosts: grp3
  become: true
  tasks:
  - name: Create a directory
    file: path=/home/vagrant/amine state=directory mode=755 owner=amine group=amine /*create grp first*/

- hosts: grp3
  become: true
  tasks:
  - name: Create multiple directory
    file: path={{item}} state=directory
    with_items:
    - '/home/vagrant/amine1'
    - '/home/vagrant/amine2'


- hosts: grp2
  become: true
  tasks:
  - name: Delete a file
    file: path=/home/vagrant/f.txt state=absent

- hosts: grp3
  become: true
  tasks:
  - name: Delete a directory
    file: path=/home/vagrant/amine state=absent 


- hosts: grp3
  become: true
  tasks:
  - name: Create User
    user: name=mohamed password=p@ssword groups=mohamed shell=/bin/bash



- hosts: grp3
  become: true
  tasks:
  - name: Remove User
    user: name=mohamed state=absent  remove=yes force=yes 
```

check syntaxe err:
```bash
ansible-playbook ping.yml --syntaxe-check
```

list target machine :
```bash
ansible-playbook ping.yml --list-hosts
```

list tasks:
```bash
ansible-playbook ping.yml --list-tasks
```


 
 





