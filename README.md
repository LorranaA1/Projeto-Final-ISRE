# Projeto Final da Disciplina de Serviço de Redes
### Grupo 7

Disciplina: Infraestrutura e Serviços de Redes

Docente: Alaelson Jatobá

Turma: 913

Discentes:

* Içami Costa Silva
* Marcos Felype Silva
* Maria Lorrana Alves Barbosa
* Vitor Daniel de Oliveira Magalhães

## ``DEFINIÇÕES``

## Tabela de definições de nomes e IPS para todas as VMs e Hostnames com o nome de domínio no S.O. de cada MV
![Tabela de definições](https://user-images.githubusercontent.com/103418874/210073911-5d55451f-2fc3-4709-a72e-c39687bf1ba0.jpg)

## Gateway server

#### Habilite o firewall e permita o acesso ssh:
```bash
     $ sudo ufw enable
     $ sudo ufw allow ssh
```
![unnamed](https://user-images.githubusercontent.com/103428491/210074123-a32f6777-48e8-4e70-bc89-176b004fb8b2.png)

#### Habilite o encaminhamento de pacotes das interfaces WAN para LAN:
✦ No arquivo /etc/ufw/sysctl.conf descomente a seguinte linha:
```bash
     net/ipv4/ip_forwarding=1
```
![unnamed (1)](https://user-images.githubusercontent.com/103428491/210074878-c8ad2781-823f-431c-9a8c-617117785e07.png)
![unnamed (4)](https://user-images.githubusercontent.com/103428491/210076018-08010daf-8778-4d0c-8bf4-ae10056a0303.png)
```bash
     Interface WAN: ens160
     Interface LAN: ens192
     Interface LoopBack: lo
```

#### Configure as interfaces de rede (netplan)
```bash
     $ sudo nano /etc/netplan/00-installer-config.yaml
```
✦ Edite do seguinte modo:
```bash
     network:
    ethernets:
        enp0s3:
            dhcp4: true
        enp0s8:
            addresses: [10.0.0.1/24]
            dhcp4: false              
    version: 2
```

#### Recrie o arquivo /etc/rc.local adicionando:
```bash
     #!/bin/bash

# /etc/rc.local

# Default policy to drop all incoming packets.
# Politica padrão para bloquear (drop) todos os pacotes de entrada
iptables -P INPUT DROP
iptables -P FORWARD DROP

# Accept incoming packets from localhost and the LAN interface.
# Aceita pacotes de entrada a partir das interfaces localhost e the LAN.
iptables -A INPUT -i lo -j ACCEPT
iptables -A INPUT -i ens192 -j ACCEPT

# Accept incoming packets from the WAN if the router initiated the connection.
# Aceita pacotes de entrada a partir da WAN se o roteador iniciou a conexao
iptables -A INPUT -i ens160 -m conntrack \
--ctstate ESTABLISHED,RELATED -j ACCEPT

# Forward LAN packets to the WAN.
# Encaminha os pacotes da LAN para a WAN
iptables -A FORWARD -i ens192 -o ens160 -j ACCEPT

# Forward WAN packets to the LAN if the LAN initiated the connection.
# Encaminha os pacotes WAN para a LAN se a LAN inicar a conexao.
iptables -A FORWARD -i ens160 -o ens192 -m conntrack \
--ctstate ESTABLISHED,RELATED -j ACCEPT

# NAT traffic going out the WAN interface.
# Trafego NAT sai pela interface WAN
iptables -t nat -A POSTROUTING -o ens160 -j MASQUERADE

# rc.local needs to exit with 0
# rc.local precisa sair com 0

exit 0
```
![unnamed (2)](https://user-images.githubusercontent.com/103428491/210074984-ec11cd11-cf2d-4665-9e98-17f4c5ceec54.png)


#### Convertendo o arquivo em executável e tornando-o inicializável no boot

```bash
     $ sudo chmod 755 /etc/rc.local
```

#### Verifique se o firewall está funcionando:

```bash
     $ sudo ufw status
```
```bash
     $ systemctl status ufw.service
```

![unnamed (3)](https://user-images.githubusercontent.com/103428491/210075323-31801afa-1518-45fc-85d2-4b6d8455c16d.png)

#### Reinicie a máquina para aplicação do script:
```bash
     $ sudo reboot
```

## Compartilhamento de arquivos com SAMBA

### ❖ Instalando o samba 

1. Atualizar repositórios locais

```bash
    $ sudo apt update 
```

2. Comando para instalar

```bash
    $ sudo apt-get install samba 
```

3. Verificando se o servidor está rodando

```bash
    $ sudo systemctl status smbd 
```

### Adicionando arquivo de backup

```bash
    $ sudo cp /etc/samba/smb.conf{,.backup} 
```

1. Removendo comentários do arquivo acima


```bash
   $ sudo bash -c 'grep -v -E "^#|^;" /etc/samba/smb.conf.backup | grep . > /etc/samba/smb.conf'
```


### Acessando o servidor samba


1. Editar o arquivo de configuração

```bash
    # Adicionar o que segue:

[global]
   workgroup = WORKGROUP
   netbios name = samba-srv
   security = user
   server string = %h server (Samba, Ubuntu)
   interfaces = 127.0.0.1/8 ens160 ens192
   bind interfaces only = yes
   log file = /var/log/samba/log.%m
   max log size = 1000
   logging = file
   panic action = /usr/share/samba/panic-action %d
   server role = standalone server
   obey pam restrictions = yes
   unix password sync = yes
   passwd program = /usr/bin/passwd %u
   passwd chat = *Enter\snew\s*\spassword:* %n\n *Retype\snew\s*\spassword:* %n\n *password\supdated\ssuccessfully* .
   pam password change = yes
   map to guest = bad user
   usershare allow guests = yes
[printers]
   comment = All Printers
   browseable = no
   path = /var/spool/samba
   printable = yes
   guest ok = no
   read only = yes
   create mask = 0700
[print$]
   comment = Printer Drivers
   path = /var/lib/samba/printers
   browseable = yes
   read only = yes
   guest ok = no
[homes]
   comment = Home Directories
   browseable = yes
   read only = no
   create mask = 0700
   directory mask = 0700
   valid users = %S
[public]
   comment = public anonymous access
   path = /samba/public
   browsable =yes
   create mask = 0660
   directory mask = 0771
   writable = yes
   guest ok = no
   valid users = @sambashare
   #guest only = yes
   #force user = nobody
   #force create mode = 0777
   #force directory mode = 0777
```

2. Adicionando usuário para utilizar o serviço samba

```bash
    User: aluno
    Password: alunoifal
```

3. Mudando permissões 

```bash
   sudo chown -R nobody:nogroup /samba/public
   sudo chmod -R 0775 /samba/public
   sudo chgrp sambashare /samba/public
```


    

## Interfaces para a rede interna (LAN) configurada com o endereçamento ip do grupo

## ``CONFIGURAÇÃO DO BIND``
## Configuração dos servidores DNS Master e Slave

### ❖ nameserver1 (DNS master)

#### Instalação do bind9
✦ Instale o bind9 via apt-get
```bash
    $ sudo apt-get install bind9 dnsutils bind9-doc 
```
![instalaçaobind9](https://user-images.githubusercontent.com/103418874/209365034-9b8aec93-fb20-4459-a4fc-1af42b1a7021.png)
#### Verifique status do serviço bind9:
```bash
    $ sudo systemctl status bind9
```
![bind9](https://user-images.githubusercontent.com/103418874/209365149-e3e88741-11e4-48c2-8988-56a8b37e3798.png)
#### Diretórios do bind
✦ Verifique se os arquivos do bind estão no diretório /etc/bind.
```bash
    $ ls /etc/bind
```
✦ Em seguida, crie um diretório para armazenar os arquivos de zonas (será o diretório /etc/bind/zones)
```bash
    $ sudo mkdir /etc/bind/zones
```
✦ Após isso, crie o arquivo db no diretório /etc/bind/zones
```bash
⚠️ O domínio será: grupo7.turma913.ifalara.local. 
Para isso, copie o arquivo /etc/bind/db.empty:

    $ sudo cp /etc/bind/db.empty /etc/bind/zones/db.labredes.ifalarapiraca.local 
    
```
✦ Crie também a zona reversa a partir do arquivo /etc/bind/db.empty
```bash
⚠ Usado quando não se conhece o IP, mas sabe-se o host.

    $ sudo cp /etc/bind/db.empty /etc/bind/zones/db.10.9.13.rev

Com isso, o arquivo db.10.9.13.rev conterá a zona reversa da rede 10.9.13.0.
```
![diretoriosBind](https://user-images.githubusercontent.com/103418874/209365240-38d546bb-b6f6-4c8a-b33a-8a1fb1848311.png)
#### Editando arquivos db:
#### ❖ Zona Direta: db.grupo7.turma913.ifalara.local
✦ Edite o arquivo db.grupo7.turma913.ifalara.local para adcionar as informações do seu domínio
```bash
    $ sudo nano db.grupo7.turma913.ifalara.local 
```
![zonaD](https://user-images.githubusercontent.com/103418874/209365372-f077e8f2-7555-4f6e-a88c-216278a2fd41.png)

#### ❖ Zona reversa: db.10.9.13.rev
✦ Edite o arquivo db.10.9.13.rev para adicionar as informações da zona reversa
```bash
    $ sudo nano db.10.9.13.rev
```
![zonaR](https://user-images.githubusercontent.com/103418874/209365397-e531bdfb-75b2-4539-bf3e-f29ac906c8b6.png)

#### Configuração do named.conf.local no arquivo do bind:
✦ Edite o arquivo de configuracão do bind para informar onde eles foram salvos. As zonas são adicionadas em /etc/bind/named.conf.local.
```bash
    $ sudo nano /etc/bind/named.conf.local
```
![configuracaoNamed_conf_local](https://user-images.githubusercontent.com/103418874/209365620-a61048b3-b4a0-4af2-ac60-486deefe25e0.png)
#### Verificação de sintaxe:
✦ Execute o comando named-checkconf
```bash
    $sudo named-checkconf
```
✦ Verifique a sintaxe dos arquivos db
```bash
    $ sudo named-checkzone grupo7.turma913.ifalara.local grupo7.turma913.ifalara.local
```
![sintaxeArquivo](https://user-images.githubusercontent.com/103418874/209366209-1f4e8af5-5a58-4866-a95b-9c2e679c76d7.png)
```bash
    $ sudo named-checkzone 13.9.10.in-addr.arpa db.10.9.13.rev
```
![sintaxeArquivo2](https://user-images.githubusercontent.com/103418874/209366214-73eb64f4-e8f9-47e0-8111-643c7daf6f26.png)

#### Configure para somente resolver endereços IPv4:
```bash
    $sudo nano /etc/default/named
```
✦ Adicione a linha OPTIONS="-4 -u bind"
![enderecoIPV4](https://user-images.githubusercontent.com/103418874/209366328-9d73e09b-4d32-41ec-ab08-6ae75fda25e0.png)
#### Execute o bind:
```bash
    $ sudo systemctl enable bind9
    $ sudo systemctl restart bind9
```
![executandoOBind](https://user-images.githubusercontent.com/103418874/209366760-cdde525d-54d8-4824-bd64-a34c78cf7317.png)
#### Configuração dos clientes:
✦ Edite o arquivo de configuração do netplan:
```bash
⚠️ Adicione os campos abaixo na interface de rede:
            nameservers: 
                addresses:
                - 10.9.13.126
                - 10.9.14.113
                search: [grupo7.turma913.ifalara.local]
```
![arquivoNetplan](https://user-images.githubusercontent.com/103418874/209366779-1d892404-7b27-4b73-ad90-be4cd42d2e8f.png)

#### ❖ nameserver2 (DNS slave)

#### Configure o DNS master na interface de rede:
✦ Configure a interface de rede com o netplan usando o DNS Master para fazer o ns2 acessar a Internet
```bash
    $ sudo nano /etc/netplan/00-instaler-config.yaml  
```
![ns2](https://user-images.githubusercontent.com/103418874/210074208-5e0f6197-9767-42d0-b501-3aebe643eb76.png)
#### Aplique as configurações:
```bash
    $ sudo netplan apply
```

#### Verifique se funcionou:
```bash
    $ ifconfig
```
![ifconfig](https://user-images.githubusercontent.com/103418874/210074714-bfd5f642-fef8-4a10-bbfc-5bed533d5e13.png)

#### ⇨ Configurando e instalando um servidor DNS secundário (slave)
```bash
    $ sudo apt-get install bind9 dnsutils bind9-doc -y
```
![get-install](https://user-images.githubusercontent.com/103418874/210074880-66397697-6706-4fc8-88bc-de97e518e631.png)

#### Verifique o status do serviço:
```bash
    $ sudo systemctl status bind9
```
![sudosystem](https://user-images.githubusercontent.com/103418874/210074966-82933d18-a6fb-473b-9dae-d3a24ea1b370.png)

#### Configure as zonas:
```bash
    $ sudo nano /etc/bind/named.conf.local
```
![zonacerta](https://user-images.githubusercontent.com/103418874/210076403-c0564e10-0b93-4f66-8adb-edfcaf768da6.png)

#### Chegando a sintaxe
✦ Execute o comando named-checkconf
```bash
    $sudo named-checkconf
```

## ``TESTES/VALIDAÇÃO``

❖ GATEWAY

❖ DNS

✦ DNS MASTER

⇨ Testando a configuração como cliente:
```bash
$ systemd-resolve --status enp0s3
```
![configComoCliente](https://user-images.githubusercontent.com/103418874/209367255-9fc4aa20-cbbd-46a6-ad50-5b0708498809.png)

⇨ Testando o serviço DNS para a máquina ns1:
```bash
$ dig ns1.grupo7.turma913.ifalara.local
```
![servicoDNSparaAmaquina1](https://user-images.githubusercontent.com/103418874/209367286-3b55f2c1-2e4b-4673-b921-302f782dd101.png)

⇨ Testando o serviço DNS reverso para a máquina ns1:
```bash
$ dig -x 10.9.13.126
```
![DNSreversoMaquina1](https://user-images.githubusercontent.com/103418874/209367336-32808973-7fa9-45d3-8582-dd1f8200efaa.png)

⇨ Testando o serviço DNS reverso para a máquina ns2:
```bash
$ dig -x 10.9.13.113
```
![DNSreversoMaquina2](https://user-images.githubusercontent.com/103418874/209367353-2a88aa60-d286-445b-8da0-c8bf6bcba6d2.png)

✦ DNS SLAVE

⇨ Resolvendo um hostname em um server específico com o comando **dig @server hostname**
```bash
$ dig @10.9.13.126 ns1.grupo7.turma913.ifalara.local
```
![dig-ns1](https://user-images.githubusercontent.com/103418874/210076503-9b29eb35-4af2-4518-8cf3-b671314afd55.png)
```bash
$ dig @10.9.13.131 gw.grupo7.turma913.ifalara.local
```
```bash
$ dig @10.9.13.123 smb.grupo7.turma913.ifalara.local
```

❖ SAMBA


## ``EXERCÍCIOS``

### 2. Faça login no ns1 e ping para as máquinas ns2, gw, e dh1.

 🔹ping para ns2
 
![pingns2](https://user-images.githubusercontent.com/103418874/210026024-53924886-d19c-4ba9-9262-205365d788ac.png)

 🔹ping para gw
 
![pinggw](https://user-images.githubusercontent.com/103418874/210026038-0c09df57-0185-48b3-980b-ca3598c92bab.png)

🔹ping para dh1

![pingsmb](https://user-images.githubusercontent.com/103418874/210068233-a850cfc3-0490-4bea-96ed-09af4378a8b2.png)

### 6. Faça login no ns1 e nslookup para as máquinas ns2, gw, e dh1.

🔹nslookup para ns2

🔹nslookup para gw

🔹nslookup para dh1

### 10. Faça login no ns1 e dig para as máquinas ns2, gw, e dh1.

🔹dig para ns2

![digns2](https://user-images.githubusercontent.com/103418874/210026118-831b5166-7b2a-4de5-8895-069ba7088ecc.png)

🔹dig para gw

![pinggw](https://user-images.githubusercontent.com/103418874/210026131-4a5b4b61-7786-4058-b99c-4482a24a55cd.png)

🔹dig para dh1

![digsmb](https://user-images.githubusercontent.com/103418874/210068213-0ddbffd1-27f9-4906-9ecd-2b2ab75b59c4.png)

### 14. Faça login no ns1 e dig -x para os IPs de ns2, gw, e dh1.

🔹dig -x para o IP de ns2

![dig-xns2](https://user-images.githubusercontent.com/103418874/210026186-b13c1eae-158e-40d3-9d34-d6cd3343d869.png)

🔹dig -x para o IP de gw

![dig-xgw](https://user-images.githubusercontent.com/103418874/210026197-c595b47b-de18-4310-9e9b-40e2951a218e.png)

🔹dig -x para o IP de dh1

![dig-x-smb](https://user-images.githubusercontent.com/103418874/210068180-370f2bfc-d9c8-4adc-9078-c38316ade9c7.png)
