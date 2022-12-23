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

## Instalação do bind9
##![Captura de Tela (140)](https://user-images.githubusercontent.com/103418874/209364375-0da9f269-c0bb-4625-804a-540b7b1a6a1d.png)

#### Verificando status do serviço bind9

#### Diretórios do bind

#### Editando arquivos db:

✦ Zona Direta: db.grupo7.turma913.ifalara.local

✦ Zona reversa: db.10.9.13.rev

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
