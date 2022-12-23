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

## Configuração do bind9 com o nameserver1 (master) e o nameserver2 (slave)

❖ nameserver1 (DNS master)

#### Instalação do bind9
![instalaçaobind9](https://user-images.githubusercontent.com/103418874/209365034-9b8aec93-fb20-4459-a4fc-1af42b1a7021.png)
#### Verificando status do serviço bind9
![bind9](https://user-images.githubusercontent.com/103418874/209365149-e3e88741-11e4-48c2-8988-56a8b37e3798.png)
#### Diretórios do bind
![diretoriosBind](https://user-images.githubusercontent.com/103418874/209365240-38d546bb-b6f6-4c8a-b33a-8a1fb1848311.png)
#### Editando arquivos db:
✦ Zona Direta: db.grupo7.turma913.ifalara.local
![zonaD](https://user-images.githubusercontent.com/103418874/209365372-f077e8f2-7555-4f6e-a88c-216278a2fd41.png)

✦ Zona reversa: db.10.9.13.rev
![zonaR](https://user-images.githubusercontent.com/103418874/209365397-e531bdfb-75b2-4539-bf3e-f29ac906c8b6.png)

#### Configuração do named.conf.local no arquivo do bind:

#### Verificação de sintaxe:

✦ 

✦ 

#### Configurando para somente resolver endereços IPv4:

#### Executando o bind:

#### Configuração dos clientes:

✦ Editando o arquivo de configuração do netplan:

❖ nameserver2 (DNS slave)

## Implementando um servidor Web LAMP

## ``TESTES/VALIDAÇÃO``

❖ NAT

❖ DNS

✦ DNS MASTER

⇨ Testando a configuração como cliente:

⇨ Testando o serviço DNS para a máquina ns1:

⇨ Testando o serviço DNS reverso para a máquina ns1:

⇨ Testando o serviço DNS reverso para a máquina ns2:

✦ DNS SLAVE

❖ SAMBA

❖ SERVIDOR WEB
