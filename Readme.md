## Contextualização

Nesse repositório estarei documentando todo o meu passo a passo do projeto AWS.

Nos primeiros momentos, estou utilizando o Oracle linux 8.9 no WSL e o Docker-Desktop para gerenciar containers 

O objetivo final do projeto é baseado nessa arquitetura:
<p float="left">

 <img src="https://github.com/yebisu22/Atividade-Docker-Compass/blob/ab6b99970bca77b0771c70119862a338833b1c4e/IMG/Arquitetura.png" width="350" />
</p>


## Rodando Wordpress localmente:

Para fazer isso, eu instalei o Docker-Desktop e o VS CODE:

https://www.docker.com/products/docker-desktop/

https://code.visualstudio.com/download

Depois da instalação, adicionei a extensão Docker dentro do VS CODE:
<p float="left">

 <img src="https://github.com/yebisu22/Atividade-Docker-Compass/blob/dcaeb550af63c017c38e4cae1248cb67a76df62a/IMG/Docker%20VS" width="290" />
</p>

Após adicionar. criei um arquivo chamado 
```docker-compose.yml```

E peguei o script padrão indicado pelo Wordpress:
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

## Rodando wordpress em uma maquina EC2

Primeiro criei uma instancia EC2 na AWS

Antes de criar, configurei uma VPC com rede e sub rede publicas para poder acessar via SSH ou TTY e também uma chave de acesso

(em breve entro detalhes sobre como configurar uma VPC e Grupos de segurança).

Para acessar a EC2 por SSH digite o comando com as credenciais da chave de acesso

``ssh -i "meu-par-de-chaves.pem" ec2-user@xx.xx.xx.xx``

E voce vera uma tela como essa:

<p float="left">

 <img src="https://github.com/yebisu22/Atividade-Docker-Compass/blob/6cf1aeae5171aa6c5df1302a4b825afb633486e8/IMG/Pombo%20Ec2.png" width="450" />
</p>
