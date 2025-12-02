##  Documentação do Projeto: Configuração pfSense (DHCP e DNS)


## **1. Visão Geral**

Este documento descreve a configuração do **pfSense** utilizada no projeto, com foco na implementação dos serviços **DHCP** (Dynamic Host Configuration Protocol) e **DNS Resolver** (Domain Name System).


## **2. Interfaces**

As interfaces do pfSense foram configuradas para gerenciar o acesso à internet (WAN) e a rede interna (LAN).

* **WAN:** Modo **bridge** com a internet/roteador físico.
    * Recebe **IP automaticamente** via DHCP do roteador físico.
* **LAN:** Rede interna do projeto.

<img width="759" height="409" alt="Tela do PFsense" src="https://github.com/user-attachments/assets/0a6a74ee-1326-45ee-be81-d63e17ab8afe" />

### **3. Configuração da Interface LAN no pfSense**

A interface LAN foi configurada com um IP estático para servir como **Gateway Padrão** e **Servidor DNS** para a rede interna.

1.  Acesse a interface web do pfSense via navegador: **`http://192.168.24.1`**
2.  Login padrão:
    * **Usuário:** `admin`
    * **Senha:** `pfsense`
3.  Navegação: **Interfaces** $\to$ **LAN**
4.  **Configure:**
    * **IPv4 Configuration Type:** **Static IPv4**
    * **IPv4 Address:** **`192.168.24.1`**
    * **Mask:** **/24**
5.  **Salvar** e **aplicar** as alterações.

---

## **4. Configuração do Servidor DHCP**

O DHCP é responsável por distribuir endereços IP automaticamente para os dispositivos da rede ($192.168.24.x$).

### **4.1. Acessando o DHCP**

1.  Caminho: **Services** $\to$ **DHCP Server** $\to$ **LAN**
2.  **Ative:** **Enable DHCP Server on LAN Interface**

### **4.2. Configurando o Range**

A faixa de endereços IP para os clientes foi definida:

* **Range Start:** **`192.168.24.50`**
* **Range End:** **`192.168.24.150`**

<img width="948" height="314" alt="Captura de tela 2025-12-01 150905" src="https://github.com/user-attachments/assets/3261c613-7827-4adc-90cf-f27b4d565b0a" />

O **Gateway** é atribuído **automaticamente** como o IP da LAN do pfSense (**`192.168.24.1`**).

### **4.3. Exemplo de Registro DHCP (Lease)**

O *lease* (aluguel) do IP de uma máquina Linux cliente pode ser verificado em: **Status** $\to$ **DHCP Leases**.

<img width="897" height="577" alt="Captura de tela 2025-12-01 150159" src="https://github.com/user-attachments/assets/24522e5f-00fb-4cea-ab7b-81dd25b790db" />

### **4.4. Testando DHCP no Mint**

A verificação do IP recebido pelo cliente Linux Mint é feita no terminal:

```bash
ip a

```

<img width="959" height="410" alt="Captura de tela 2025-12-01 151723" src="https://github.com/user-attachments/assets/411cd810-48c6-472d-9ff8-3c783fbefb59" />





## 5. Configuração do DNS (DNS Resolver)

O pfSense usará o **DNS Resolver** para resolver nomes dentro da rede e pode funcionar como DNS local.

### 5.1. Ativar o DNS Resolver

Caminho: `Services` → `DNS Resolver`

Certifique-se de:
* Habilitar o **Enable DNS Resolver**.
* Network Interfaces: **LAN**.
* Outgoing Network Interfaces: **WAN**.
* Marcar o **Register DHCP leases**.
* Marcar o **Register DHCP static mappings**.

![Captura de tela: Configuração do DNS Resolver no pfSense](https://github.com/user-attachments/assets/c9352149-f041-4680-a9f5-96659e6ce25d)

### 5.2. Criando Registros DNS Internos (Host Overrides)

Caminho: `Services` → `DNS Resolver` → `Host Overrides` → `Add`

Exemplo de Host Override:
* **Host**: `site-rede`
* **Domain**: `lan`
* **IP Address**: `192.168.24.10` (IP estático do servidor Mint)
* **Description**: Servidor Linux

Salvar e aplicar.

<img width="927" height="666" alt="Captura de tela 2025-12-02 101650" src="https://github.com/user-attachments/assets/e7b36065-a2a7-4347-b884-2a773946bea2" />


## 6. Implementação e Testes dos Serviços

### 6.1. Servidor DHCP (pfSense)
**Objetivo:** Atribuir endereços IP automaticamente aos dispositivos na rede (Faixa 192.168.24.x).

#### Validação e Testes

**Teste 1: Verificação de IP no Cliente**
Comando executado no Linux Mint Cliente para confirmar o recebimento do IP:
```bash
ip addr show | grep inet

```

<img width="734" height="127" alt="Captura de tela 2025-12-02 102356" src="https://github.com/user-attachments/assets/95b02246-7178-42ca-b38b-596d0a20a1bc" />

**Teste 2: Log de Transação DHCP (DORA) Comando para forçar renovação e visualizar a negociação com o servidor:**
```bash

sudo dhclient -r && sudo dhclient -v
```

<img width="716" height="313" alt="Captura de tela 2025-12-02 102858" src="https://github.com/user-attachments/assets/43008151-5007-44e0-8329-01f8fc020d42" />

**Teste 3: Tabela de Leases no Servidor Verificação visual na interface web do pfSense:**
<img width="897" height="577" alt="Captura de tela 2025-12-01 150159" src="https://github.com/user-attachments/assets/d9c6033f-9bc8-44bf-b40b-512525942924" />

### 6.2. Servidor DNS (pfSense)
Validação e Testes

**Teste 1: Resolução de Nomes (Nslookup) Verificando se o servidor DNS preferencial é o pfSense (192.168.24.1):**
```bash
nslookup google.com
```
<img width="417" height="215" alt="Captura de tela 2025-12-02 103247" src="https://github.com/user-attachments/assets/d587e34c-52d4-48c5-9870-80e517158b57" />

**Teste 2: Detalhamento da Consulta (Dig) Teste de tempo de resposta e autoridade:**
```bash
dig google.com
```
<img width="563" height="353" alt="Captura de tela 2025-12-02 103427" src="https://github.com/user-attachments/assets/62b5d1cc-44e1-41f0-a091-88aae404bf10" />

**Teste 3: Ping entre máquinas** 

*Apresentamos o teste de ping utilizando o IP do servidor Mint e, em seguida, outro teste de ping utilizando o DNS para verificar a conexão com o servidor DHCP.*

<img width="646" height="505" alt="Captura de tela 2025-12-02 104006" src="https://github.com/user-attachments/assets/69ceffff-3065-4d10-8bbb-ce70628dfd0a" />
