# Projeto Final de Administração de Redes de Computadores

## 1. Introdução
Este documento apresenta a configuração completa de um ambiente de rede virtualizada contendo:
* Um **pfSense** atuando como roteador, firewall, servidor DHCP e servidor DNS.
* Uma máquina **Linux Mint Servidor**, responsável pelos serviços:
    * Apache (servidor web)
    * FTP
    * NFS
* Uma máquina **Linux Mint Cliente**, que consome os serviços configurados.

O objetivo é demonstrar a construção de uma infraestrutura de rede funcional e estável utilizando tecnologias amplamente aplicadas em ambientes corporativos.

---

## 2. Topologia da Rede
A topologia utilizada neste projeto segue o seguinte modelo:

**pfSense (roteador/firewall)**
* Interface WAN: modo bridge
* Interface LAN: `192.168.24.1/24`

**Linux Mint Servidor**
* IP: `192.168.24.10/24` (DHCP reservado ou estático)
* Funções: Apache, FTP, NFS

**Linux Mint Cliente**
* IP via DHCP: `192.168.24.X`
* Funciona como consumidor dos serviços

### Diagrama Lógico
A topologia lógica utilizada no ambiente virtual é a seguinte:

`
                  INTERNET
                     |
                  [WAN - pfSense] (Bridge)
                     |
                [LAN - 192.168.24.1/24]
                     |
    -------------------------------------------------
    |                                               |
[MINT SERVIDOR - 192.168.24.10]       [MINT CLIENTE - 192.168.24.xxx]
    |                                               |
 Apache / FTP / NFS                            Dispositivo de Testes`

Cada serviço possui um arquivo próprio contendo todos os detalhes do processo de configuração, incluindo scripts utilizados, comandos executados e anotações sobre cada etapa.


3. Implementação e Testes dos Serviços
3.1. Servidor DHCP (pfSense)

Objetivo: Atribuir endereços IP automaticamente aos dispositivos na rede (Faixa 192.168.24.x).
Validação e Testes

Teste 1: Verificação de IP no Cliente Comando executado no Linux Mint Cliente para confirmar o recebimento do IP:
Bash

ip addr show | grep inet

    [INSIRA O PRINT AQUI: Terminal do cliente mostrando o IP 192.168.24.x]

Teste 2: Log de Transação DHCP (DORA) Comando para forçar renovação e visualizar a negociação com o servidor:
Bash

sudo dhclient -r && sudo dhclient -v

    [INSIRA O PRINT AQUI: Terminal mostrando os pacotes Discover, Offer, Request, Ack]

Teste 3: Tabela de Leases no Servidor Verificação visual na interface web do pfSense:

    [INSIRA O PRINT AQUI: Tela 'Status > DHCP Leases' do pfSense mostrando o cliente conectado]

3.2. Servidor DNS (pfSense)

Objetivo: Resolver nomes de domínio externos e internos.
Configuração

O serviço de DNS Resolver foi habilitado no pfSense para escutar na interface LAN e registrar as concessões DHCP e Hosts estáticos.

    [INSIRA O PRINT AQUI: Tela 'Services > DNS Resolver' do pfSense mostrando o serviço habilitado]

Validação e Testes

Teste 1: Resolução de Nomes (Nslookup) Verificando se o servidor DNS preferencial é o pfSense (192.168.24.1):
Bash

nslookup google.com

    [INSIRA O PRINT AQUI: Terminal mostrando Server: 192.168.24.1 e a resposta do Google]

Teste 2: Detalhamento da Consulta (Dig) Teste de tempo de resposta e autoridade:
Bash

dig google.com

    [INSIRA O PRINT AQUI: Saída do comando DIG mostrando a query time]

3.3. Servidor Web (Apache)

Objetivo: Hospedar site interno no Linux Mint Servidor (192.168.24.10).
Configuração

Comando de instalação e verificação do serviço no servidor:
Bash

sudo apt update && sudo apt install apache2 -y
sudo systemctl status apache2

    [INSIRA O PRINT AQUI: Terminal do servidor mostrando o status 'Active (running)']

Validação e Testes

Teste 1: Acesso via Terminal (HTTP Header) Executado no Cliente para validar resposta do servidor:
Bash

curl -I http://192.168.24.10

    [INSIRA O PRINT AQUI: Resultado mostrando HTTP/1.1 200 OK]

Teste 2: Acesso via Navegador Acesso visual realizado pelo Linux Mint Cliente:

    [INSIRA O PRINT AQUI: Print do navegador do Cliente acessando o IP 192.168.24.10]

Teste 3: Log de Acesso em Tempo Real Monitoramento no servidor enquanto o cliente acessa:
Bash

tail -f /var/log/apache2/access.log

    [INSIRA O PRINT AQUI: Terminal do servidor mostrando o IP do cliente acessando o site]

3.4. Servidor FTP (Vsftpd)

Objetivo: Permitir transferência de arquivos entre Cliente e Servidor.
Configuração

Arquivo de configuração /etc/vsftpd.conf (Trecho das configurações ativas):
Bash

listen=NO
listen_ipv6=YES
anonymous_enable=NO
local_enable=YES
write_enable=YES

Validação e Testes

Teste 1: Verificação de Porta (Banner Grabbing) Verificando se a porta 21 está ouvindo no servidor:
Bash

nc -v 192.168.24.10 21

    [INSIRA O PRINT AQUI: Terminal mostrando conexão sucedida com vsftpd]

Teste 2: Upload de Arquivo via Linha de Comando Transferência de um arquivo de teste do Cliente para o Servidor:
Bash

echo "Arquivo de teste FTP" > teste.txt
ftp 192.168.24.10
# Comandos executados dentro do FTP:
# > put teste.txt
# > ls

    [INSIRA O PRINT AQUI: Terminal mostrando o login e a transferência do arquivo]

3.5. Servidor NFS (Network File System)

Objetivo: Compartilhamento de diretórios em rede.
Configuração

Arquivo /etc/exports no Servidor:
Bash

/var/nfs/compartilhamento 192.168.24.0/24(rw,sync,no_subtree_check)

Comando para aplicar a exportação:
Bash

sudo exportfs -a

Validação e Testes

Teste 1: Discovery (Showmount) Cliente verificando quais pastas o servidor está compartilhando:
Bash

showmount -e 192.168.24.10

    [INSIRA O PRINT AQUI: Resultado mostrando /var/nfs/compartilhamento]

Teste 2: Montagem e Escrita Remota O Cliente monta a pasta e cria um arquivo dentro dela para provar permissão de escrita:
Bash

sudo mount 192.168.24.10:/var/nfs/compartilhamento /mnt
sudo touch /mnt/arquivo_remoto_cliente.txt
ls -l /mnt

    [INSIRA O PRINT AQUI: Terminal mostrando o comando de mount e o arquivo criado]

4. Conclusão

Os testes realizados confirmam que a topologia proposta foi implementada com sucesso. Todos os serviços (DHCP, DNS, Web, FTP e NFS) estão operacionais e integrados, permitindo comunicação fluida entre o roteador pfSense, o Servidor Linux e o Cliente Linux.

A seguir, disponibilizamos o link para o Drive contendo as máquinas virtuais utilizadas no projeto:

    [INSIRA O LINK AQUI]
