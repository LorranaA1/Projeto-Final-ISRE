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

## Configuração de hardware utilizada em cada MV

## Hostnames com o nome de domínio no S.O. de cada MV

## Gateway server

## Compartilhamento de arquivos com SAMBA

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
#### Verifique status do serviço bind9
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
⚠️ Usado quando não se conhece o IP, mas sabe-se o host.

$ sudo cp /etc/bind/db.empty /etc/bind/zones/db.10.9.13.rev

Com isso, o arquivo db.10.9.13.rev conterá a zona reversa da rede 10.9.13.0.
```
![diretoriosBind](https://user-images.githubusercontent.com/103418874/209365240-38d546bb-b6f6-4c8a-b33a-8a1fb1848311.png)
#### Editando arquivos db:
✦ Zona Direta: db.grupo7.turma913.ifalara.local
##### Edite o arquivo db.grupo7.turma913.ifalara.local para adcionar as informações do seu domínio
```bash
    $ sudo nano db.grupo7.turma913.ifalara.local 
```
![zonaD](https://user-images.githubusercontent.com/103418874/209365372-f077e8f2-7555-4f6e-a88c-216278a2fd41.png)

✦ Zona reversa: db.10.9.13.rev
##### Edite o arquivo db.10.9.13.rev para adicionar as informações da zona reversa
```bash
    $ sudo nano db.10.9.13.rev
```
![zonaR](https://user-images.githubusercontent.com/103418874/209365397-e531bdfb-75b2-4539-bf3e-f29ac906c8b6.png)

#### Configuração do named.conf.local no arquivo do bind:
![configuracaoNamed_conf_local](https://user-images.githubusercontent.com/103418874/209365620-a61048b3-b4a0-4af2-ac60-486deefe25e0.png)
#### Verificação de sintaxe:

✦ Sintaxe dos arquivos db
![sintaxeArquivo](https://user-images.githubusercontent.com/103418874/209366209-1f4e8af5-5a58-4866-a95b-9c2e679c76d7.png)
![sintaxeArquivo2](https://user-images.githubusercontent.com/103418874/209366214-73eb64f4-e8f9-47e0-8111-643c7daf6f26.png)

#### Configurando para somente resolver endereços IPv4:
![enderecoIPV4](https://user-images.githubusercontent.com/103418874/209366328-9d73e09b-4d32-41ec-ab08-6ae75fda25e0.png)
#### Executando o bind:
![executandoOBind](https://user-images.githubusercontent.com/103418874/209366760-cdde525d-54d8-4824-bd64-a34c78cf7317.png)
#### Configuração dos clientes:
✦ Editando o arquivo de configuração do netplan:
![arquivoNetplan](https://user-images.githubusercontent.com/103418874/209366779-1d892404-7b27-4b73-ad90-be4cd42d2e8f.png)

#### ❖ nameserver2 (DNS slave)

## Implementando um servidor Web LAMP

## ``TESTES/VALIDAÇÃO``

❖ NAT

❖ DNS

✦ DNS MASTER

⇨ Testando a configuração como cliente:
![configComoCliente](https://user-images.githubusercontent.com/103418874/209367255-9fc4aa20-cbbd-46a6-ad50-5b0708498809.png)

⇨ Testando o serviço DNS para a máquina ns1:
![servicoDNSparaAmaquina1](https://user-images.githubusercontent.com/103418874/209367286-3b55f2c1-2e4b-4673-b921-302f782dd101.png)

⇨ Testando o serviço DNS reverso para a máquina ns1:
![DNSreversoMaquina1](https://user-images.githubusercontent.com/103418874/209367336-32808973-7fa9-45d3-8582-dd1f8200efaa.png)

⇨ Testando o serviço DNS reverso para a máquina ns2:
![DNSreversoMaquina2](https://user-images.githubusercontent.com/103418874/209367353-2a88aa60-d286-445b-8da0-c8bf6bcba6d2.png)

✦ DNS SLAVE

❖ SAMBA

❖ SERVIDOR WEB
