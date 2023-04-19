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

Configurar id (que está no backup):

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


## Procedimento de remover um Domain Controller manualmente

Baseado em https://wiki.samba.org/index.php/Demoting_a_Samba_AD_DC

Para verificar todos Domain Controllers que fazem parte do grupo:

    ldbsearch -H /var/lib/samba/private/sam.ldb '(invocationId=*)' --cross-ncs objectguid

### Caso em que ainda temos acesso ao DC que vamos desmontar

O DC que vamos desmontar não pode ser owner. Para verificar em ambos DCs:

    samba-tool fsmo show

Exemplo de saída:

    SchemaMasterRole owner: CN=NTDS Settings,CN=VAGRANTFIRSTDC,CN=Servers,CN=Default-First-Site-Name,CN=Sites,CN=Configuration,DC=smbdomain,DC=local,DC=br

Como o DC que vamos desmontar é o VAGRANTFIRSTDC, vamos acessar o novo DC e defini-lo como owner:

    samba-tool fsmo transfer --role='all' -Uadministrator --password='SuperSenh@1'

Rodando novamente:

    samba-tool fsmo show

Verificamos que VAGRANTSAMBADCDEBIAN11 agora é o owner:

    SchemaMasterRole owner: CN=NTDS Settings,CN=VAGRANTSAMBADCDEBIAN11,CN=Servers,CN=Default-First-Site-Name,CN=Sites,CN=Configuration,DC=smbdomain,DC=local,DC=br

Voltando ao Domain Controller que queremos remover:

    samba-tool domain demote -Uadministrator --password='SuperSenh@1'

Verificar se o domain controller removido não faz mais parte dos Domains Controllers:

    ldbsearch -H /var/lib/samba/private/sam.ldb '(invocationId=*)' --cross-ncs objectguid
    
Listando entradas DNS no samba firstdc por cli:
    
    samba-tool dns query 192.168.8.48 smbdomain.local.br @ ALL -UAdministrator%SuperSenh@1

Deletando a entrada de ip 192.168.121.237 do host printers.smbdomain.local.br por cli:

    samba-tool dns delete 192.168.8.48 smbdomain.local.br printers.smbdomain.local.br A 192.168.121.237 -UAdministrator%SuperSenh@1
