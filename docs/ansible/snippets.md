
### Disable pipelining for hosts where requiretty is enabled
```ansible
- lineinfile:
    dest: /etc/sudoers
    line: 'Defaults requiretty'
    state: absent
  sudo_user: root
  vars:
      ansible_ssh_pipelining: no
```