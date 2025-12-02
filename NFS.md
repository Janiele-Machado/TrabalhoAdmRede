# Documentação Completa do Servidor NFS

Esta documentação descreve **todo o processo de instalação, configuração, testes e resolução de problemas** do serviço **NFS (Network File System)** no ambiente virtualizado utilizado no projeto. 

---

#  1. Visão Geral

O NFS permite que máquinas Linux compartilhem diretórios como se fossem pastas locais. No projeto, temos:

* **pfSense**: fornecendo DHCP e DNS
* **Linux Mint Servidor**: executando NFS
* **Linux Mint Cliente**: montando e acessando o compartilhamento

---

#  2. Requisitos

Antes de iniciar, verifique:

* O cliente e o servidor devem estar **na mesma rede** via pfSense
* Ambos devem estar com **DNS funcional**
* O servidor deve responder ao cliente via ping:

  ```
  ping 192.168.24.10   # Exemplo de IP do servidor NFS
  ```

---

#  3. Instalação do Servidor NFS

No **Linux Mint Servidor**:

```
sudo apt update
sudo apt install nfs-kernel-server -y
```

Para verificar se instalou corretamente:

```
systemctl status nfs-kernel-server
```

* Saída do comando `systemctl status` mostrando o serviço ativo
  
<img width="880" height="252" alt="Captura de tela 2025-12-02 120906" src="https://github.com/user-attachments/assets/5a4bd94b-084e-49dc-889c-d734f28c1fa1" />

---

#  4. Criar Diretório a Ser Compartilhado

Utilizamos `/srv/nfs` (padrão Linux Filesystem Hierarchy):

```
sudo mkdir -p /srv/nfs/compartilhado
sudo chown -R nobody:nogroup /srv/nfs/compartilhado
sudo chmod -R 777 /srv/nfs/compartilhado
```

* Terminal mostrando o diretório criado e permissões via
* `ls -ld /srv/nfs/compartilhado`
<img width="574" height="56" alt="Captura de tela 2025-12-02 121049" src="https://github.com/user-attachments/assets/c6c0692a-c170-4483-abc2-d7e623991546" />

---

# 📝 5. Configuração do Arquivo /etc/exports

Edite o arquivo:

```bash
sudo nano /etc/exports
```

Adicione:

```bash
/srv/nfs/compartilhado 192.168.24.0/24(rw,sync,no_subtree_check)
```

### Explicação das opções

* **rw** — leitura e escrita
* **sync** — gravações síncronas (mais seguro)
* **no_subtree_check** — evita erros ao mover pastas
* **no_root_squash** — Permite que o usuário root do cliente mantenha privilégios no diretório compartilhado (menos seguro, usar apenas em ambientes controlados)

Aplicar as exportações:

```bash
sudo exportfs -rav
```

<img width="956" height="277" alt="Captura de tela 2025-12-02 122152" src="https://github.com/user-attachments/assets/63362f1b-96ff-4283-9cc6-213687739f95" />

---

# 6. Reiniciar Serviço

```bash
sudo systemctl restart nfs-kernel-server
```

---

#  7. Configuração no Cliente NFS

No **Linux Mint Cliente**, instalar o pacote:

```bash
sudo apt install nfs-common -y
```

Criar ponto de montagem:

```bash
sudo mkdir -p /mnt/nfs
```

Montar manualmente:

```bash
sudo mount 192.168.24.10:/srv/nfs/compartilhado /mnt/nfs
```

<img width="975" height="286" alt="nfs" src="https://github.com/user-attachments/assets/b22227e8-9563-481b-bb10-82d6d3b7e119" />

---

# 🔁 8. Montagem Automática (Opcional)

Para montar automaticamente ao iniciar o sistema, editar o `/etc/fstab`:

```bash
sudo nano /etc/fstab
```

Adicionar a linha:

```bash
192.168.24.10:/srv/nfs/compartilhado   /mnt/nfs   nfs   defaults   0   0
```

Testar:

```bash
sudo mount -a
```

---

#  9. Testes Importantes

###  Teste 1 — Ver as exportações do servidor

No cliente:

```bash
showmount -e 192.168.24.10
```

Deve aparecer algo como:

```
Export list for 192.168.24.10:
/srv/nfs/compartilhado 192.168.24.0/24
```

---

###  Teste 2 — Criar arquivo no cliente e ver no servidor

Cliente:

```bash
echo "teste NFS" | sudo tee /mnt/nfs/arquivo.txt
```

Servidor:

```bash
ls -l /srv/nfs/compartilhado
```

O arquivo deve aparecer.

---

###  Teste 3 — Permissões

No servidor:

```bash
ls -ld /srv/nfs/compartilhado
```

---

#  10. Solução de Problemas (Troubleshooting)

###  Erro: "Permission denied"

* Verifique permissões com:

  ```bash
  sudo chmod -R 777 /srv/nfs/compartilhado
  ```
* Verifique se o IP do cliente está dentro do range configurado no `/etc/exports`.

###  Erro: "mount: wrong fs type, bad option..."

* Cliente pode não ter o `nfs-common` instalado.

###  Cliente não monta

* Testar conectividade:

  ```bash
  ping 192.168.24.10
  ```
* Testar disponibilidade do servidor:

  ```bash
  showmount -e 192.168.24.10
  ```

###  Serviço não inicia

* Verificar logs:

  ```bash
  sudo journalctl -xe
  ```

---

