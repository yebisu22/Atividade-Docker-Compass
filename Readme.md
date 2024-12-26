# Introdução
Nesse repositorio está a documentação do trabalho de dezembro Wordpress da Compass Uol.

as tecnologias utilizadas foram:

• Vs code

• Docker-Desktop

• Amazon Linux 8.9

• AWS

• Github 

• Wordpress 

# Contextualização

Nesse repositório está sendo documentado todo o passo a passo do projeto AWS.

Nos primeiros momentos, foi utilizado o Oracle linux 8.9 no WSL e o Docker-Desktop para gerenciar containers 

O objetivo final do projeto é baseado nessa arquitetura:
<p float="left">

 <img src="https://github.com/yebisu22/Atividade-Docker-Compass/blob/ab6b99970bca77b0771c70119862a338833b1c4e/IMG/Arquitetura.png" width="350" />
</p>


# Rodando Wordpress localmente:

Para fazer isso, é necessário instalar o Docker-Desktop e o VS CODE:

https://www.docker.com/products/docker-desktop/

https://code.visualstudio.com/download

Depois da instalação, adicione a extensão Docker dentro do VS CODE:
<p float="left">

 <img src="https://github.com/yebisu22/Atividade-Docker-Compass/blob/dcaeb550af63c017c38e4cae1248cb67a76df62a/IMG/Docker%20VS" width="290" />
</p>

Após adicionar. crie um arquivo chamado 
```docker-compose.yml```

E de recomendação, pegue o script padrão indicado pelo Wordpress:
```yml

version: '3.1'

services:

  wordpress:
    image: wordpress
    restart: always
    ports:
      - 8080:80
    environment:
      WORDPRESS_DB_HOST: db
      WORDPRESS_DB_USER: wordpress
      WORDPRESS_DB_PASSWORD: wordpress
      WORDPRESS_DB_NAME: wordpress
    volumes:
      - wordpress:/var/www/html

  db:
    image: mysql:8.0
    restart: always
    environment:
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: wordpress
      MYSQL_RANDOM_ROOT_PASSWORD: '1'
    volumes:
      - db:/var/lib/mysql

volumes:
  wordpress:
  db:


```

Automaticamente no Docker-Desktop será criado um container como esse:
<p float="left">

 <img src="https://github.com/yebisu22/Atividade-Docker-Compass/blob/d74b5c5da291fdd9d7de2c0ff5c9074b2c7e4132/IMG/Exemplo%20Container" width="250" />
</p>

E está pronto, Seu wordpress já esta ativo localmente!🙂 (Para acessar basta colcoar "localhost" e a porta 8080 ou 80 no navegador)

<p float="left">

 <img src="https://github.com/yebisu22/Atividade-Docker-Compass/blob/5815bec98b66db91ecc3c6c50c0546360b9d281c/IMG/Wordpress%20Exemplo" width="450" />
</p>

# Caminho para o Wordpress na EC2 AWS:

## VPC

Primeira parte fundamental no nosso projeto, é a criação de uma VPC.

Crie uma VPC com 4 Sub-redes 3 tabelas de rotas e 2 conexões de rede

Sendo elas 2 sub redes privadas e 2 publicas, dessa forma:


<p float="left">

 <img src="https://github.com/yebisu22/Atividade-Docker-Compass/blob/c1e0b2108058add65cf9877eee62b18159bae9c5/IMG/VPC%20cf.png" width="750" />
</p>

# Configurando Grupos de Segurança

Configurar bem um Grupo de segurança, vai ser o que sustenta grande parte do nosso projeto, e pra isso será criado 2 grupos, um privado e um publico

•Grupo publico com 4 regras de entrada: 
```
Porta 80 HTTP

Porta 2049 NFS

Porta 80 HTTP Ipv6

Porta 22 SSH
```

Regras de saída 
```
Todo tráfego 0.0.0.0/0
```

•Grupo privado com 3 regras

```
Porta 22 SSH

Porta 80 HTTP

Porta 3306 MYSQL
```

Regras de saída 

```
Todo tráfego 0.0.0.0/0
```
Dessa forma todas as portas necessárias ja foram liberadas

# Criando um EFS 

O EFS (Elastic File System) será uma ferramenta de volume e a criação é bem simples

Faça uma criação padrão e coloque em 2 redes com Grupos de seguranças publicos

E pronto seu EFS esta rodando 


<p float="left">

 <img src="https://github.com/yebisu22/Atividade-Docker-Compass/blob/bba8c4c2b036a59d9fda6e40f0286a7fb56a0ae6/IMG/EFS.png" width="750" />
</p>

# Criando um RDS

O RDS será a ferramenta de banco de dados utilizado no projeto com o MYSQL 

Nele será guardada as informações do Wordpress.

Primeira parte é selecionar o banco de dados utilizado e será o MYSQL

Logo após configure o nome do banco, senha e o nome do usuario (nos exemplos usaremos os padrões)

Utilize nossa VPC criada e deixe com o grupo de segurança privado

Em opções avançadas existe a opção de criar uma Database ao subir o RDS e nesse projeto será utilizado

verifique se está na porta 3306 e pronto, seu RDS esta pronto para uso

<p float="left">

 <img src="https://github.com/yebisu22/Atividade-Docker-Compass/blob/56462bb0875463f219ddca1eb17d8809484b36da/IMG/RDS.png" width="750" />
</p>

# Criando uma Bastion Host

A Bastion host será utlizada para acessar maquinas EC2 privadas

A criação pode variar bastante aos gostos pessoais a respeito do sistema operacional, nesse projeto foi usado o Amazon Linux 2.

Crie e coloque um par de chaves para acesso da maquina por SSH

Coloque sua instancia no grupo de segurança publico e atribua um ip publico e crie a instancia.

<p float="left">

 <img src="https://github.com/yebisu22/Atividade-Docker-Compass/blob/1086e348d8d29b10bddd9bd52384594d882b1fd1/IMG/Bastion%20Host.png" width="650" />
</p>

## Entrando na instancia EC2

Primeiro pegue as chaves credenciais e coloque na pasta que voce deseja utilizar para entrar na maquina no projeto foi usado /root/.ssh

Dentro do diretorio da chave de acesso, use o comando 

``ssh -i "meu-par-de-chaves.pem" ec2-user@xx.xx.xx.xx``

E voce vera uma tela como essa:

<p float="left">

 <img src="https://github.com/yebisu22/Atividade-Docker-Compass/blob/6cf1aeae5171aa6c5df1302a4b825afb633486e8/IMG/Pombo%20Ec2.png" width="450" />
</p>

Se ela essa tela apareceu sinal que está tudo certo.

# Wordpress na EC2

Para instalar o Wordpress na EC2 não é algo tão diferente de instalar na maquina local.

Usaremos as ferramentas:

•Docker-Compose

•Docker

## Instalando Docker e Docker-compose

Primeiro verifique se tem alguma atualização do sistema com o comando:

```
sudo yum update -y
```

Para instalar o Docker use 

```
sudo yum install docker -y
```

Adicione um usuario Docker a um grupo

```
sudo usermod -a -G docker ec2-user

id ec2-user

newgrp Docker

```

Instalando Docker-Compose

```
wget https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)
```
Docker-compose com executavel global e permissões de uso

```
sudo mv docker-compose-$(uname -s)-$(uname -m) /usr/local/bin/docker-compose


sudo chmod -v +x /usr/local/bin/docker-compose
```

Agora ative eles com o comando

```
sudo systemctl enable docker.service


sudo systemctl start docker.service
```

Para verificar basta usar 

```
Sudo systemctl status docker
```

E algo assim deve aparecer 

<p float="left">

 <img src="https://github.com/yebisu22/Atividade-Docker-Compass/blob/e6f5817770592b8489733bfd17477d214f52d07b/IMG/Docker%20status.png" width="750" />
</p>

# Instalando o Wordpress

Crie um diretorio 

```
mkdir wordpress
```
Entre no diretorio e crie um arquivo .yml com o codigo padrão do wordpress

```
vi docker-compose.yml
```
E coloque o codigo

```yml

version: '3.1'

services:

  wordpress:
    image: wordpress
    restart: always
    ports:
      - 8080:80
    environment:
      WORDPRESS_DB_HOST: db
      WORDPRESS_DB_USER: wordpress
      WORDPRESS_DB_PASSWORD: wordpress
      WORDPRESS_DB_NAME: wordpress
    volumes:
      - wordpress:/var/www/html

  db:
    image: mysql:8.0
    restart: always
    environment:
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: wordpress
      MYSQL_RANDOM_ROOT_PASSWORD: '1'
    volumes:
      - db:/var/lib/mysql

volumes:
  wordpress:
  db:


```
Agora para subir o container use o comando 

```
docker-compose up -d
```

## Acessando o site 

Use o endereço IPv4 publico da EC2 

e coloque no navegador com http:// antes do ip

E voce verá algo assim se tudo der certo

<p float="left">

 <img src="https://github.com/yebisu22/Atividade-Docker-Compass/blob/6ad63de674be86c7e8cacb4854117a716dd864e8/IMG/Wp%20ec2.png" width="750" />
</p>

# Conectando com o RDS no Wordpress

Basta pegar o Endpoint e os dados criados na nossa RDS anteriormente e colocar no codigo do Wordpress 

Exemplo:
```yml
 
services:
  wordpress:
    image: wordpress:latest
    container_name: wordpress
    restart: always
    ports:
      - "80:80"
    environment:
      WORDPRESS_DB_HOST: Endpoint
      WORDPRESS_DB_NAME: Nome do Banco
      WORDPRESS_DB_USER: Seu usuario
      WORDPRESS_DB_PASSWORD: Sua senha

```
Após isso basta reinicar o container e a já vai estar conectado com o RDS

# Conectando com o EFS na EC2

é algo bem simples, vá até o diretorio raiz e crie um diretorio dentro de mnt

```
cd / 

cd mnt

sudo mkdir efs
```
Depois anexe o EFS com o comando 

```
sudo mount -t efs fs-xxxx:/ /mnt/efs

```
E no wordpress coloque em serviços

```yml
 volumes:
      - /mnt/efs:/var/www/html
```
Verifique com df -h

E pronto ja esta tudo vinculado ao seu wordpress 😄

# Criando um script de automatização Wordpress

O script usado foi

```sh
#!/bin/bash

sudo yum update -y


sudo yum install docker -y


sudo usermod -a -G docker ec2-user

id ec2-user

newgrp Docker


wget https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m) 


sudo systemctl enable docker.service


sudo systemctl start docker.service


sudo mv docker-compose-$(uname -s)-$(uname -m) /usr/local/bin/docker-compose


sudo chmod -v +x /usr/local/bin/docker-compose


mkdir -p /home/ec2-user/wordpress


cd /home/ec2-user/wordpress

cat > docker-compose.yml <<EOF

services:
  wordpress:
    image: wordpress:latest
    container_name: wordpress
    restart: always
    ports:
      - "80:80"
    environment:
      WORDPRESS_DB_HOST: 
      WORDPRESS_DB_NAME: 
      WORDPRESS_DB_USER: 
      WORDPRESS_DB_PASSWORD: 
    volumes:
      - /mnt/efs:/var/www/html


EOF

cd wordpress

docker-compose up -d

cd / 

cd mnt

sudo mkdir efs

sudo mount -t efs fs-xxxx:/ /mnt/efs

```
# Crie um Gateway NAT 

O gateway será usado para passar internet para as instancias privadas

Pra isso criaremos um Gat na sub rede publica com o tipo de conectividade publica

e vincular com as sub rede privada pela tabela de rotas

<p float="left">

 <img src="https://github.com/yebisu22/Atividade-Docker-Compass/blob/a9a483e459d0bdabf17af96458980b30103c995f/IMG/GAT.png" width="750" />
</p>



# Criando uma EC2 privada 

Os passos são os mesmos do publico porem a hora da criação da instancia tem umas mudanças.

Use a sub rede privada (com Gateway NAT) e não atribua ip publico 

use o grupo de segurança privado

e coloque o script acima para poder instalar e rodar o wordpress.

## Conectar com EC2 privada 

Voce vai utilizar uma Bastian Host na mesma VPC da instancia privada e acessar via SSH como se fosse uma instancia publica.

e verá a tela padrão de conexão 

<p float="left">

 <img src="https://github.com/yebisu22/Atividade-Docker-Compass/blob/6cf1aeae5171aa6c5df1302a4b825afb633486e8/IMG/Pombo%20Ec2.png" width="450" />
</p>

# Criando um Load Balancer

Escolha o Load balancer classico

Na hora da criação escolha a VPC de sempre e deixe os mapeamentos de rede (sub redes) publico e o grupo de segurança tambem publico

em Verificações de integridade, coloque a seguinte configuração e crie 

```
HTTP porta 80 caminho de ping = /wp-admin/install.php
```
## Atribuindo instancias 

Depois de criado, vá até "Atribuir instancias" e registre sua instancia privada 

Se tudo der certo ira passar pelo teste de integridade

E sera possivel acessar o wordpress pelo DNS do Load balancer 

# Auto Scaling Group

Crie um grupo de Auto Scaling. Defina o tamanho mínimo, máximo e desejado das instâncias EC2.

 
## Associar o Load Balancer ao Auto Scaling Group

Adicione o Load Balancer ao Auto Scaling Group para que ele distribua tráfego para as instâncias EC2 automaticamente.

E pronto seu projeto AWS foi completo 😄
