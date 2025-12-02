**Servidor Apache no Linux Mint**

1. Contexto do Ambiente Virtualizado

O ambiente utilizado contém:

- pfSense como servidor DHCP + DNS

- Linux Mint como servidor onde o Apache será instalado

- O Mint recebe o IP automaticamente pelo pfSense (reserva configurada)

Toda comunicação ocorre dentro da rede interna virtualizada

<img width="735" height="91" alt="Captura de tela 2025-12-02 105650" src="https://github.com/user-attachments/assets/be430880-9a1f-485c-b7dd-e19b473e5ef1" />

<img width="712" height="228" alt="Captura de tela 2025-12-02 105730" src="https://github.com/user-attachments/assets/8973b858-80cd-4476-a2fe-0c83fd321fc4" />


Objetivo: Hospedar site interno no Linux Mint Servidor (192.168.24.10).


Configuração

Comando de instalação e verificação do serviço no servidor:


    sudo apt update && sudo apt install apache2 -y
    sudo systemctl status apache2

<img width="964" height="330" alt="Captura de tela 2025-12-02 110204" src="https://github.com/user-attachments/assets/aa1f6013-b6ef-42a7-8914-060eebee139b" />

Pressione Q para sair.

Teste no navegador acessando:

http://192.168.24.10

<img width="984" height="1003" alt="Captura de tela 2025-12-02 110425" src="https://github.com/user-attachments/assets/28661abf-d770-4461-8f48-4960513f4b08" />


2. Criando o Diretório do Site

Crie o diretório onde os arquivos do seu site ficarão:

    sudo mkdir /var/www/site-rede

Ajuste as permissões para o seu usuário poder editar:

    sudo chown -R $USER:www-data /var/www/site-rede


5. Criando a Página Inicial do Site

Abra o arquivo index.html:

    nano /var/www/site-rede/index.html

Coloque esse HTML:

Salve com:

CTRL + O (Enter)

CTRL + X

<img width="985" height="238" alt="Captura de tela 2025-12-02 111233" src="https://github.com/user-attachments/assets/648e6a13-8d93-4574-a03f-c0545079a984" />


 6. Criando o VirtualHost (arquivo .conf)

Crie o arquivo de configuração:S

sudo nano /etc/apache2/sites-available/site-rede.conf

Insira:

    <VirtualHost *:80>
      ServerName site-rede.local
      DocumentRoot /var/www/site-rede

      <Directory /var/www/site-rede>
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/site-rede-error.log
    CustomLog ${APACHE_LOG_DIR}/site-rede-access.log combined
    </VirtualHost>

<img width="975" height="320" alt="Captura de tela 2025-12-02 111506" src="https://github.com/user-attachments/assets/e620e811-4aae-4e66-9a80-3ab01622c27d" />


Recarregue o Apache:

sudo systemctl reload apache2

 8. Testando o Servidor

Acesso pelo IP

- No navegador:

http://192.168.24.10

<img width="984" height="1003" alt="Captura de tela 2025-12-02 110425" src="https://github.com/user-attachments/assets/68d570e2-6900-4641-9a05-5078a68e2c32" />

- Teste via terminal do Mint

curl http://192.168.24.10

<img width="520" height="204" alt="Captura de tela 2025-12-02 111726" src="https://github.com/user-attachments/assets/2332538c-1a86-4bd7-9f34-8a7ab4e83287" />

- Teste via outras máquinas da rede

<img width="983" height="1007" alt="Captura de tela 2025-12-02 111933" src="https://github.com/user-attachments/assets/1a9d8e5d-0a59-40ff-ac6f-c7bb41744718" />

- ping 192.168.24.10

<img width="514" height="238" alt="Captura de tela 2025-12-02 112001" src="https://github.com/user-attachments/assets/6c629a73-a9a0-480b-a292-d06f662baeb9" />

curl retornando o HTML.

<img width="546" height="168" alt="Captura de tela 2025-12-02 112145" src="https://github.com/user-attachments/assets/051b2bfc-0b7f-4880-9c0b-5b4bfe5aebda" />


 9. Teste via Nome DNS

No terminal:

    nslookup site-rede.local

Resultado esperado:

<img width="440" height="120" alt="Captura de tela 2025-12-02 112402" src="https://github.com/user-attachments/assets/a75cbc27-643b-4c0f-abd3-bfeeb4009f27" />


 10. Logs do Servidor

Logs úteis para debug:

Acessos:

/var/log/apache2/site-rede-access.log

Erros:

/var/log/apache2/site-rede-error.log

Visualizar logs em tempo real:

sudo tail -f /var/log/apache2/*.log


