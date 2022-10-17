
### Disable pipelining when requiretty is enabled
```YAML
- lineinfile:
    dest: /etc/sudoers
    line: 'Defaults requiretty'
    state: absent
  sudo_user: root
  vars:
      ansible_ssh_pipelining: no
```

### Copy file to server
```YAML
---
- hosts: all
  become: yes

  tasks:
  - name: Copy file from host to all servers in inventory
    copy:
      src: "{{ item.src }}"
      dest: "{{ item.dest }}"
      owner: sudo
      group: sudo
      mode: 0777
      force: yes

    with_items:
      - src: /home/hello.txt
        dest: /home/hello.txt
```

### Append text to file
```YAML
---
- name: Append text to a file
  hosts: all
  become: yes

  tasks:
    - name: Append a line to text file
      lineinfile:
        path: /home/sdp/.bash_profile
        line: Text to be added
```

### Check running docker containers
```YAML
## I created this playbook to check all docker processes running in a list of IP's with a single username/password combination

---
- name: Check container names
  hosts: docker_containers
  remote_user: mahesh.rijal
  become: yes
  gather_facts: yes
  strategy: free

  pre_tasks:
    - name: Check if docker is installed
      command: docker --version
      register: docker_valid
      ignore_errors: yes

  tasks:
    - name: Check all running containers
      become: yes
      command: docker ps --format "{{ '{{' }} .Names {{ '}}' }}"
      register: dkr_ps
      when: docker_valid

    - name: Print all running container names
      debug:
        msg:
          - "{{ dkr_ps.stdout_lines }}"

    - name: Copy results to file
      lineinfile:
        line:
          - "{{ ansible_default_ipv4.address }}"
          - "{{ dkr_ps.stdout_lines }}"
        insertafter: EOF
        dest: /home/mhs/ansible/container-names.txt
      delegate_to: 127.0.0.1

#- local_action: copy content={{ dkr_ps.stdout_lines }} dest=/home/sdp/ansible/playbookoutput.txt
```

### Check service status
```YAML
---
- name: Check sshd status
  hosts: all
  remote_user: user
  become: yes
  gather_facts: no

  tasks:
    - name: ping all servers
      ansible.builtin.ping:
      register: ping_stat

    - name: check if sshd is running
      ansible.builtin.shell: ps cax | grep sshd
      register: sshd_status

    - debug:
        var: ping_stat

    - debug:
        msg: "{{sshd_status.stdout}}"
```