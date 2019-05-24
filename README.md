sambadc role
============

This role only does two jobs:

 - Configure a pre-installed samba server to be a new Domain Controler (DC)
 - Or configure a pre-installed samba server to join to existent DC group
 
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
         - uspdev.install_samba
         - uspdev.sambadc

Tips
----

Show domain level password options.

    samba-tool domain passwordsettings show

## Procedimento de restauração de backup em caso de pane.
Baseado em https://wiki.samba.org/index.php/Using_the_samba_backup_script

Criar nova máquina com:

 - mesmo hostname
 - mesmo ip

Instalar samba e o configure como DC. Use a esta role, sambadc, para esta função.
Parar o serviço do samba:

/usr/sbin/service samba-ad-dc stop

Configurar id:

    net setdomainsid S-1-5-21-1948074455-2901749274-3793093824

Remove as pastas:

    rm -rf /etc/samba/ /var/lib/samba/

Descompactar backups:

    tar -jxf etc_samba.tar.bz2 -C /etc
    tar -jxf var_lib_samba.tar.bz2 -C /var/lib/
    tar -jxf var_lib_samba_private.tar.bz2 -C /var/lib/samba/
    tar -jxf var_lib_samba_sysvol.tar.bz2 -C /var/lib/samba/

Criar os arquivos para idepotência da role sambadc:

    touch /var/.samba_ad_created
    touch /var/.samba_ad_joined

[VERIFICAR] Não entendi se precisamos rodar ou não:

    samba-tool ntacl sysvolreset

Subir o serviço:

    /usr/sbin/service samba-ad-dc start
