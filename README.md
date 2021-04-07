# Conteúdo da Live de Container Images

## O que são imagems e camadas

- Explicar o que são imagens (docker image ls)
- Explicar o que são camadas (docker history)


##Definição de Dockerfile.

- Explicar o que é o Dockerfile
- Apresentar os comandos do Dockerfile e a relação com camadas (FROM, RUN, COPY e CMD)
- Outros commandos: ENV, ARG, WORKDIR, ENTRYPOINT

##Boas práticas para criação de imagens.

- Imagem efemera
- Excluir arquivos com .dockerignore
- Utilizar multi-stage builds
- Reduzir o número de camadas (RUN, COPY, ADD)
- Utilizar argumentos em multiplas linhas (RUN apk add)

**DEMO**: Aplicar as boas práticas na criação de imagem em uma aplicação em Lua.

##Build de imagens com Buildah

Script para build da imagem lua-ms utilizando buldah

```bash
#!/bin/bash 

container=$(buildah from alpine)
buildah run $container  wget 'http://openresty.org/package/admin@openresty.com-5ea678a6.rsa.pub' -O /etc/apk/keys/admin@openresty.com-5ea678a6.rsa.pub
buildah run $container sh -c "echo http://openresty.org/package/alpine/v3.12/main >> /etc/apk/repositories"
buildah run $container apk add --no-cache luarocks5.1 lua-inspect lua5.1-sql-mysql openresty gcc musl-dev openssl-dev lua5.1-dev
buildah run $container luarocks-5.1 install lapis
buildah copy $container ./nginx.conf /usr/local/openresty/nginx/conf/nginx.conf
buildah config --workingdir /opt/app $container
buildah config --entrypoint "/usr/local/openresty/nginx/sbin/nginx -g 'daemon off; master_process on;'" $container
buildah umount $container
buildah commit $container lua-ms
```

# Desafio

Criar um Dockerfile para aplicação https://github.com/hector-vido/python-ms.

Esta aplicação está escrita em python
Utilizar uma imagem de alpine
Colocar os arquivos da aplicação em /opt/app com um "bind mount"
Esta aplicação utiliza duas variáveis de ambiente
APP_HOST - precisar ser 0.0.0.0
APP_PORT - fica a sua escolha
Será preciso instalar o pacote py3-pip
Rodar pip3 install -r requirements.txt para instalar dependências
Iniciar a aplicação com flask run ou python3 app.py

**BONUS**: Assim que criar esse Dockerfile tente customiza e aplicar as boas práticas vistas, por exemplo, o multi-stage building.
