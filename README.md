<p align="center">
<img src="https://cwmkt.com.br/wp-content/uploads/2023/08/logo-github-cwmkt.svg" alt="DispZap Whats Marketing" width="240" />
<p align="center">Seja bem-vindo ao Guia de Instalação Grafana 🚀</p>
</p>
  
<p align="center">
<img src="https://whatsapp.com/favicon.ico" alt="WhatsAPP-logo" width="32" />
<span>Grupo WhatsaAPP: </span>
<a href="https://chat.whatsapp.com/LgWN47cUa5AFFmywAhfaAg" target="_blank">Grupo</a>
</p>

<hr />
<hr />

### Passo 01: Instalar o Docker mais recente
Atualizar a lista de pacotes e fazer upgrade para versões mais recentes com o comando:

```bash
apt-get -y update && apt-get -y upgrade
```

### Remover versões existentes do docker caso estejam pré-instaladas com o comando:

```bash
sudo apt-get remove -y docker docker-engine docker.io containerd runc
```

### Atualizar novamente a lista de pacotes:

```bash
sudo apt-get -y update
```

### Instalar os pré-requisitos para o docker com o comando (copiar todas as linhas e executar de uma vez):

```bash
sudo apt-get -y install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
```

### Baixar e importar a chave gpg para validar o repositório oficial do docker:

```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```

### Adicionar o repositório oficial do docker na lista de repositórios que o ubuntu irá procurar pacotes com o comando (copiar tudo e executar de uma vez):

```bash
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

### Atualizar novamente a lista de pacotes para saber que o docker está disponível a partir do repositório oficial:

```bash
sudo apt-get -y update
```

### Instalar o docker e o containerd runtime:

```bash
sudo apt-get -y install docker-ce docker-ce-cli containerd.io
```

### Baixar o docker-compose para a versão correta do ubuntu com o comando abaixo:

```bash
sudo curl -s -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

### Dar permissão de execução no arquivo docker-compose baixado anteriormente com o comando:

```bash
sudo chmod +x /usr/local/bin/docker-compose
```

### Criar um link simbólico do docker-compose na pasta padrão de executáveis (para onde a variável PATH busca) com o comando:

```bash
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
```

### Testar a execução do docker rodando o container hello-world com o comando:

```bash
docker run --rm hello-world
```

#### deverá mostrar a seguinte saída na tela:

```bash
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
``` 


### Passo 02: Configuração Proxy reverso (NGINX)

```bash
apt-get -y install nginx
```

### Criar a configuração para acesso web na porta 80 (ssl será visto depois...). Copie todo o comando abaixo até EOF e execute-o de uma vez.

cat << "EOF" > /etc/nginx/sites-enabled/grafana
server {
    listen 80;
    listen [::]:80;    server_name grafana.seudominio.com.br;

    location / {
       #try_files $uri $uri/ =404;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header host $host;
        proxy_set_header X-Forwarded-Server $host;
        proxy_pass http://127.0.0.1:3000/;
    }
}
EOF

### Valide a configuração e reinicie o nginx

```bash
nginx -t
```

```bash
systemctl restart nginx
```

### Passo 03: Deploy do Grafana com o docker-compose

```bash
cd ~
git clone https://github.com/cwmkt/grafana.git
cd grafana
```

### Incie o container do Grafana

```bash
docker-compose up -d
```

#### Pronto! Grafana instalado com sucesso ✅
Agora só acessar, server_name grafana.seudominio.com.br</br>
usuário padrão: admin com senha admin

