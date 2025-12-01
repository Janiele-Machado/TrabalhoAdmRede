Documentação: pfSense
1. Visão Geral

Este documento descreve a configuração do pfSense utilizada no projeto: DHCP, DNS.

2. Interfaces

WAN: modo bridge com a internet/roteador.
- Recebe IP automaticamente via DHCP do roteador físico.

LAN: rede interna.

<img width="759" height="409" alt="Tela do PFsense" src="https://github.com/user-attachments/assets/0a6a74ee-1326-45ee-be81-d63e17ab8afe" />

3. Configuração da LAN no pfSense

Acesse via navegador:

http://192.168.24.1


Login padrão:

      Usuário: admin
      Senha: pfsense

Navegação:

Interfaces → LAN

Configure:

- IPv4 Configuration Type: Static IPv4

- IPv4 Address: 192.168.24.1

- Mask: /24

Salvar e aplicar.

  4. Configuração do Servidor DHCP

O DHCP será responsável por distribuir endereços IP automaticamente para os dispositivos da rede.

 4.1. Acessando o DHCP

Caminho:

Services → DHCP Server → LAN

Ative:

Enable DHCP Server on LAN Interface

 4.2. Configurando o Range

Configure:

Range:
Start: 192.168.24.50
End:   192.168.24.150

<img width="948" height="314" alt="Captura de tela 2025-12-01 150905" src="https://github.com/user-attachments/assets/3261c613-7827-4adc-90cf-f27b4d565b0a" />

Gateway automático = IP da LAN do pfSense.

4.3. Exemplo de registro DHCP (Lease)

Após iniciar as máquinas Linux:

Status → DHCP Leases

<img width="897" height="577" alt="Captura de tela 2025-12-01 150159" src="https://github.com/user-attachments/assets/24522e5f-00fb-4cea-ab7b-81dd25b790db" />

 4.4. Testando DHCP no Mint

No terminal:

    ip a

<img width="959" height="410" alt="Captura de tela 2025-12-01 151723" src="https://github.com/user-attachments/assets/411cd810-48c6-472d-9ff8-3c783fbefb59" />

6. Regras de Firewall

Padrão da LAN: permitir saída para internet.

7. Testes

Ping entre máquinas

Verificar IP recebido via DHCP

Testar resolução DNS, se configurado
