
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