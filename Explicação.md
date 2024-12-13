## Contextualização

Nesse repositório estarei documentando todo o meu passo a passo do projeto AWS.

Nos primeiros momentos, estou utilizando o Oracle linux 8.9 no WSL e o docker-desktop para gerenciar containers 
## Rodando Wordpress localmente:

Para fazer isso, eu instalei o Docker-Desktop e o VS CODE:

https://www.docker.com/products/docker-desktop/

https://code.visualstudio.com/download

Depois da instalação, adicione a extensão Docker dentro do VS CODE:
<p float="left">

 <img src="https://github.com/yebisu22/Atividade-Docker-Compass/blob/dcaeb550af63c017c38e4cae1248cb67a76df62a/IMG/Docker%20VS" width="450" />
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
