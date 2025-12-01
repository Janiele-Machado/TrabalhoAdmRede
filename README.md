**Projeto Final de Administração de Redes de Computadores**
1. Introdução

Este documento apresenta a configuração completa de um ambiente de rede virtualizada contendo:

Um pfSense atuando como roteador, firewall, servidor DHCP e servidor DNS.

Uma máquina Linux Mint Servidor, responsável pelos serviços:

- Apache (servidor web)

- FTP

- NFS

Uma máquina Linux Mint Cliente, que consome os serviços configurados.

O objetivo é demonstrar a construção de uma infraestrutura de rede funcional e estável utilizando tecnologias amplamente aplicadas em ambientes corporativos.

2. Topologia da Rede

A topologia utilizada neste projeto segue o seguinte modelo:

pfSense (roteador/firewall)

- Interface WAN: modo bridge

- Interface LAN: 192.168.24.1/24

Linux Mint Servidor

- IP: 192.168.24.10/24 (DHCP reservado ou estático)

- Funções: Apache, FTP, NFS

Linux Mint Cliente

- IP via DHCP: 192.168.24.X

- Funciona como consumidor dos serviços

A topologia lógica utilizada no ambiente virtual é a seguinte:

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
     Apache / FTP / NFS                            Dispositivo de Testes


Cada serviço possui um arquivo próprio contendo todos os detalhes do processo de configuração, incluindo scripts utilizados, comandos executados e anotações sobre cada etapa.

A seguir, disponibilizamos o link para o Drive contendo as máquinas virtuais utilizadas no projeto:

