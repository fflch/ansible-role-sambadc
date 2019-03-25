sambadc role
============

This role only does two jobs:

 - Create a new Domain Controler (DC) in a samba server 
 - Or join a new DC to a existent DC group
 
 This role does not do the following, but depends them to work:
 
 - Install samba
 - Configure resolv.conf

Said that, this role contains a lot of codes and ideas copied from another roles and were
grouped here to achieve a different approach to setup samba dc:


 - https://github.com/yamb00/ansible-role-samba
 - https://github.com/bertvv/ansible-role-samba
 - https://github.com/mrlesmithjr/ansible-samba
 - https://github.com/criecm/ansible-role-samba
 - https://github.com/gentoo-ansible/role-samba-dc
 - https://github.com/tschifftner/ansible-role-samba
 - https://github.com/jtyr/ansible-samba
 - https://github.com/raasss/ansible-role-samba
 - https://github.com/HiTechRabbit/secondary_dc_samba_ansible
 - https://github.com/darrylweaver/ansible-samba


Example Playbook
----------------

You should install samba before run this role:

    - hosts: servers
      roles:
         - uspdev.install-samba
         - uspdev.sambadc

