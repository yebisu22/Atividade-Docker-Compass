## Contextualização

Nesse repositório está sendo documentado todo o passo a passo do projeto AWS.

Nos primeiros momentos, foi utilizado o Oracle linux 8.9 no WSL e o Docker-Desktop para gerenciar containers 

O objetivo final do projeto é baseado nessa arquitetura:
<p float="left">

 <img src="https://github.com/yebisu22/Atividade-Docker-Compass/blob/ab6b99970bca77b0771c70119862a338833b1c4e/IMG/Arquitetura.png" width="350" />
</p>


## Rodando Wordpress localmente:

Para fazer isso, é nescessário instalar o Docker-Desktop e o VS CODE:

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

## Caminho para o Wordpress na EC2 AWS:

## VPC

Primeira parte fundamental no nosso projeto, é a criação de uma VPC.

Crie uma VPC com 4 Sub-redes 3 tabelas de rotas e 2 conexões de rede

Sendo elas 2 sub redes privadas e 2 publicas, dessa forma:


<p float="left">

 <img src="https://github.com/yebisu22/Atividade-Docker-Compass/blob/c1e0b2108058add65cf9877eee62b18159bae9c5/IMG/VPC%20cf.png" width="750" />
</p>

## Configurando Grupos de Segurança

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

##Criando um EFS 

O EFS (Elastic File System) será uma ferramenta de volume e a criação é bem simples

Faça uma criação padrão e coloque em 2 redes com Grupos de seguranças publicos

E pronto seu EFS ta rodando 


<p float="left">

 <img src="https://github.com/yebisu22/Atividade-Docker-Compass/blob/c1e0b2108058add65cf9877eee62b18159bae9c5/IMG/VPC%20cf.png" width="750" />
</p>
