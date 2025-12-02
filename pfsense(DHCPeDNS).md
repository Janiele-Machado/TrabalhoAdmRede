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

5. Configuração do DNS (DNS Resolver)

O pfSense usará o DNS Resolver para resolver nomes dentro da rede e pode funcionar como DNS local.

 5.1. Ativar o DNS Resolver

Caminho:

Services → DNS Resolver

Certifique-se de:

Habilitar o Enable DNS Resolver

Network Interfaces: LAN

Outgoing Network Interfaces: WAN

Marcar o Register DHCP leases

Marcar o Register DHCP static mappings

<img width="733" height="658" alt="Captura de tela 2025-12-02 101623" src="https://github.com/user-attachments/assets/c9352149-f041-4680-a9f5-96659e6ce25d" />

 5.2. Criando Registros DNS Internos (Host Overrides)

Services → DNS Resolver → Host Overrides → Add

Exemplo:

- Host: site-rede
- Domain: lan
- IP Address: 192.168.24.10    (IP estático do servidor Mint)
- Description: Servidor Linux

Salvar e aplicar.

<img width="927" height="666" alt="Captura de tela 2025-12-02 101650" src="https://github.com/user-attachments/assets/e7b36065-a2a7-4347-b884-2a773946bea2" />



3. Implementação e Testes dos Serviços
3.1. Servidor DHCP (pfSense)

Objetivo: Atribuir endereços IP automaticamente aos dispositivos na rede (Faixa 192.168.24.x).
Validação e Testes

Teste 1: Verificação de IP no Cliente Comando executado no Linux Mint Cliente para confirmar o recebimento do IP:


      ip addr show | grep inet

<img width="734" height="127" alt="Captura de tela 2025-12-02 102356" src="https://github.com/user-attachments/assets/95b02246-7178-42ca-b38b-596d0a20a1bc" />


Teste 2: Log de Transação DHCP (DORA) Comando para forçar renovação e visualizar a negociação com o servidor:


      sudo dhclient -r && sudo dhclient -v

<img width="716" height="313" alt="Captura de tela 2025-12-02 102858" src="https://github.com/user-attachments/assets/43008151-5007-44e0-8329-01f8fc020d42" />

Teste 3: Tabela de Leases no Servidor Verificação visual na interface web do pfSense:

<img width="897" height="577" alt="Captura de tela 2025-12-01 150159" src="https://github.com/user-attachments/assets/d9c6033f-9bc8-44bf-b40b-512525942924" />



Validação e Testes

Teste 1: Resolução de Nomes (Nslookup) Verificando se o servidor DNS preferencial é o pfSense (192.168.24.1):
Bash

nslookup google.com

<img width="417" height="215" alt="Captura de tela 2025-12-02 103247" src="https://github.com/user-attachments/assets/d587e34c-52d4-48c5-9870-80e517158b57" />

Teste 2: Detalhamento da Consulta (Dig) Teste de tempo de resposta e autoridade:

dig google.com

<img width="563" height="353" alt="Captura de tela 2025-12-02 103427" src="https://github.com/user-attachments/assets/62b5d1cc-44e1-41f0-a091-88aae404bf10" />

Teste 3:Ping entre máquinas

Na imagem abaixo, apresentamos o teste de ping utilizando o IP do servidor Mint e, em seguida, outro teste de ping utilizando o DNS para verificar a conexão com o servidor DHCP.

<img width="646" height="505" alt="Captura de tela 2025-12-02 104006" src="https://github.com/user-attachments/assets/69ceffff-3065-4d10-8bbb-ce70628dfd0a" />
