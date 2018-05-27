Role Name
=========

This role is a collection of codes and ideas from another roles:

 - https://github.com/yamb00/ansible-role-samba/: This role implement a  

- https://github.com/bertvv/ansible-role-samba
 - https://github.com/mrlesmithjr/ansible-samba
 - https://github.com/criecm/ansible-role-samba
 - https://github.com/gentoo-ansible/role-samba-dc/
 - https://github.com/tschifftner/ansible-role-samba
 - https://github.com/jtyr/ansible-samba
 - https://github.com/raasss/ansible-role-samba
 - https://github.com/HiTechRabbit/secondary_dc_samba_ansible
 - https://github.com/darrylweaver/ansible-samba

Requirements
------------


Role Variables
--------------


Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: servers
      vars:
        sambadc_global: &def
          "vfs objects": acl_xattr
      roles:
         - { role: username.rolename, x: 42 }

License
-------

BSD

Author Information
------------------

An optional section for the role authors to include contact information, or a website (HTML is not allowed).
