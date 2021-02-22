# Use Ansible to add permissions to the sudoers file

Use Ansible to modify the sudoers file to add permission ofr a user to run a specific systemctl command to start/stop your custom service.
This could be a jar or any other kind of service

```yaml
- name: Add local user to sudoers to start/stop its service
   become: yes
   blockinfile:
     path: /etc/sudoers
     marker: "### {mark} ANSIBLE MANAGED BLOCK ###"
     block: |
       %my_user ALL= NOPASSWD: /bin/systemctl start my_application
       %my_user ALL= NOPASSWD: /bin/systemctl stop my_application
       %my_user ALL= NOPASSWD: /bin/systemctl restart my_application
       %my_user ALL= NOPASSWD: /bin/systemctl status my_application
     validate: 'bash -c "cat /etc/sudoers %s | visudo -cf-"'
```

Even better, by adding a file into sudoers.d folder, which is automatically imported in the sudoers file

```yaml
- name: Add local user to sudoers to start/stop its service
  become: yes
  copy:
    content: |
      %my_user ALL= NOPASSWD: /bin/systemctl start my_application
      %my_user ALL= NOPASSWD: /bin/systemctl stop my_application
      %my_user ALL= NOPASSWD: /bin/systemctl restart my_application
      %my_user ALL= NOPASSWD: /bin/systemctl status my_application
    dest: /etc/sudoers.d/my_application
    mode: u=r,g=r,o=
    validate: 'bash -c "cat /etc/sudoers.d/my_application %s | visudo -cf-"'
```
