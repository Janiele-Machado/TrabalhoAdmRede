**Servidor FTP (Vsftpd)**

Documentação Completa do Servidor FTP (vsftpd) no Linux Mint

Este documento descreve **todo o processo de instalação, configuração e testes** de um servidor FTP utilizando **vsftpd** em um ambiente virtualizado com **pfSense**.


# 1. Cenário do Projeto

* pfSense distribuindo **DHCP e DNS**
* Máquina **Mint-Servidor** com os serviços:

  * Apache
  * FTP (vsftpd)
  * NFS
* Máquina **Mint-Cliente** conectada na mesma rede para realizar testes


# 2. Instalar o vsftpd

No Mint-Servidor:

```
sudo apt update
sudo apt install vsftpd -y
```

# 3. Fazer backup do arquivo de configuração (boa prática)

```
sudo cp /etc/vsftpd.conf /etc/vsftpd.conf.bak
```

---

#  4. Configuração do servidor FTP

Edite o arquivo principal:

```
sudo nano /etc/vsftpd.conf
```

Ative as opções abaixo (apenas manter, ativar ou adicionar caso não existam):

```
local_enable=YES
write_enable=YES
chroot_local_user=YES
allow_writeable_chroot=YES
```

Explicação:

* **local_enable** → permite login de usuários locais
* **write_enable** → permite upload
* **chroot_local_user** → coloca cada usuário preso na própria pasta
* **allow_writeable_chroot** → corrige bloqueio comum de permissões

Documentação completa:
 ```
listen=YES
listen_ipv6=NO

anonymous_enable=NO
local_enable=YES
write_enable=YES
local_umask=022

# Chroot para manter usuário preso à pasta dele
chroot_local_user=YES
allow_writeable_chroot=YES

# Logs
xferlog_enable=YES
log_ftp_protocol=YES
vsftpd_log_file=/var/log/vsftpd.log

# Modo passivo - necessário mesmo em rede local virtualizada
pasv_enable=YES
pasv_min_port=40000
pasv_max_port=41000

# O endereço passivo será o IP interno do servidor Mint
# (importante em ambiente virtual)
pasv_address=192.168.24.10

# Performance
connect_from_port_20=YES
````

#  5. Criar o usuário FTP

Criar um usuário sem shell (apenas para FTP):

```
sudo useradd -m ftpuser -s /usr/sbin/nologin
sudo passwd ftpuser
```


# 6. Criar diretório para o FTP com permissões corretas

Crie a pasta onde o usuário fará upload/download:

```
sudo mkdir -p /srv/ftp/ftpuser
sudo chown ftpuser:ftpuser /srv/ftp/ftpuser
sudo chmod 755 /srv/ftp/ftpuser
```

`ls -ld /srv/ftp/ftpuser`

<img width="532" height="66" alt="Captura de tela 2025-12-02 114522" src="https://github.com/user-attachments/assets/7715ee8e-fa8f-4df4-9382-995cec474ebe" />

# 7. Verificar shell permitido

Certifique-se de que o shell `/usr/sbin/nologin` está em `/etc/shells`:

```
cat /etc/shells
```
<img width="389" height="203" alt="Captura de tela 2025-12-02 114650" src="https://github.com/user-attachments/assets/355b133d-79ca-4358-bb1c-d029b08a5f79" />

Se não aparecer, adicione:

```
echo "/usr/sbin/nologin" | sudo tee -a /etc/shells
```

---

# 8. Reiniciar o serviço

```
sudo systemctl restart vsftpd
sudo systemctl status vsftpd
```


* `systemctl status vsftpd`
  
<img width="814" height="264" alt="Captura de tela 2025-12-02 114808" src="https://github.com/user-attachments/assets/b618aac3-aa90-41c6-9676-aa65528afa80" />

---

# 9. Testando o Login via FTP (no Mint-Cliente)

No cliente, conecte ao servidor:

```
ftp 192.168.24.10
```

Digite:

* usuário: `ftpuser`
* senha: 4444


# 10. Teste de Download (GET)

No servidor, crie um arquivo para baixar:

```
echo "Arquivo de teste do servidor" | sudo tee /srv/ftp/ftpuser/teste.txt
sudo chown ftpuser:ftpuser /srv/ftp/ftpuser/teste.txt
```

No cliente, dentro do FTP:

```
get teste.txt
```


<img width="993" height="985" alt="Captura de tela 2025-12-01 104313" src="https://github.com/user-attachments/assets/32de6936-5feb-422f-8be5-01f6c073f4ae" />


---

# 11. Teste de Upload (PUT)

No cliente:

```
echo "Enviado pelo cliente" > cliente.txt
```

No FTP:

```
put cliente.txt
```
<img width="975" height="1016" alt="Captura de tela 2025-12-01 104629" src="https://github.com/user-attachments/assets/0d7875cd-3ee5-4a7d-83b9-c5e55b0237d9" />

Depois, no servidor:

```
ls -l /srv/ftp/ftpuser
```

Você verá:

* teste.txt
* cliente.txt

<img width="516" height="138" alt="Captura de tela 2025-12-02 115459" src="https://github.com/user-attachments/assets/3477ac01-6154-439d-b78b-19b010022e0f" />

---

12. Comandos básicos do FTP

| Comando          | Função                     |
| ---------------- | -------------------------- |
| `ls`             | Lista arquivos do servidor |
| `cd`             | Muda diretório             |
| `get arquivo`    | Baixa um arquivo           |
| `put arquivo`    | Envia um arquivo           |
| `mget *`         | Baixa vários arquivos      |
| `delete arquivo` | Exclui arquivo no servidor |
| `bye` / `quit`   | Sai do FTP                 |

---

 13. Possíveis erros e soluções

### Erro 530 Login incorrect

* senha errada
* diretório do usuário não existe
* shell do usuário não está em `/etc/shells`
* permissões incorretas

### Permit denied ao enviar arquivo

* falta de permissão → resolver com:

```
sudo chown ftpuser:ftpuser /srv/ftp/ftpuser -R
```

---


