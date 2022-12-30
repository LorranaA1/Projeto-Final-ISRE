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

#### Verifique o status do serviço:
```bash
    $ sudo systemctl status bind9
```

#### Configure as zonas:
```bash
    $ sudo nano /etc/bind/named.conf.local
```
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
$ dig @10.9.13.126 ns1.grupo7.turma913.ifalara.local ou
$ dig @10.9.13.131 gw.grupo7.turma913.ifalara.local ou
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
