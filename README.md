**Projeto Final de Administração de Redes de Computadores**
1. Objetivo do Projeto

Este projeto tem como objetivo implementar um ambiente de rede completo utilizando máquinas virtuais, com foco em serviços essenciais de infraestrutura. Toda a configuração será documentada e versionada em GitHub, incluindo scripts, prints e logs de testes.

O trabalho prevê:

Configuração de um firewall/roteador pfSense.

Criação de um servidor Linux Mint responsável por hospedar serviços de Apache, FTP e NFS.

Criação de um cliente Mint para testes de conectividade.

Implantação de DHCP e DNS no pfSense.

Realização de testes para validar o funcionamento de toda a rede interna.

2. Topologia da Rede

A topologia lógica utilizada no ambiente virtual é a seguinte:

                INTERNET (Bridge da Máquina Real)
                         |
                      [WAN - pfSense] (Bridge)
                         |
                    [LAN - 192.168.24.1/24]
                         |
        -------------------------------------------------
        |                                               |
    [MINT SERVIDOR - 192.168.24.xxx]       [MINT CLIENTE - 192.168.24.xxx]
        |                                               |
     Apache / FTP / NFS                            Dispositivo de Testes

