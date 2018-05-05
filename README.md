# FAIRPLAY

> ⚠️ **Projeto experimental.**

Este é o repositório principal que contém 2 sub-repositórios (git submodules): 

- **fairplay-api**: Laravel 5.x, Mysql 5.7, Laravel Echo Server (websockets)
- **fairplay-web**: Vue 2.x, Nuxt 1.x, Vuetify 1.x

A infraestrtura do projeto é baseada totalmente no Docker / Docker Compose:

- **/docker**: arquivos base de configuração dos serviços. Contém toda a infraestrutura necessária para o *frontend* e *backend*.
- **docker-compose.yml**: composição de serviços Docker.
- **.env**:  variáveis de ambiente utilizadas por *docker-compose.yml*.


# INSTALAÇÃO

✔️ Preparando

✔️ Clone os repositórios

✔️ Configure fairplay-api (backend)

✔️ Configure fairplay-web (frontend)

✔️ Configure o Docker

✔️ Inicie os serviços

## 👍 Preparando

1) Certifique-se que tenha Docker instalado na máquina hospedeira.

2) Para fazer uma instalação limpa, removendo dados de instalações anteriores (mysql, caddy, redis ...), execute:

```
# Este é o caminho do storage configurado nas variáveis.

rm -rf ~/.storage
```

3) Caso deseje remover todas as imagens e containers execute:

```
docker system prune -a && 
docker rmi $(docker images -a -q)
```

## 📖 Repositórios

Clone o repositório principal com seus sub-módulos (recursivamente):

```
git clone --recursive https://github.com/robsontenorio/fairplay
```

Em seguida, no VSCODE, altere os branches dos sub-repositórios para "MASTER".


## 🐉  fairplay-api

Altere as variáveis de ambiente

```
cd fairplay-api
cp .env.example .env

APP_URL=http://api.fairplay.test
APP_URL_FRONTEND=http://fairplay.test


DB_DATABASE=fairplaydb_dev
DB_USERNAME=root
DB_PASSWORD=<mesma senha anterior>

SOCIAL_FACEBOOK_REDIRECT=http://api.fairplay.test/api/auth/redirect/facebook
SOCIAL_FACEBOOK_CLIENT_ID=
SOCIAL_FACEBOOK_CLIENT_SECRET=

SOCIAL_GOOGLE_REDIRECT=http://api.fairplay.test/api/auth/redirect/google
SOCIAL_GOOGLE_CLIENT_ID=
SOCIAL_GOOGLE_CLIENT_SECRET=
```

## 🐬 fairplay-web

Altere as variáveis de ambiente

```
cd fairplay-web
cp .env.example .env

API_URL = http://api.fairplay.test/api
API_URL_SOCKET = http://api.fairplay.test:6001
API_URL_STORAGE = http://api.fairplay.test/storage
```

## 💻 Docker

Altere as variáveis de ambiente
```
cp .env-example .env

MYSQL_ROOT_PASSWORD=<senha>
```

Ajuste a configuração do Laravel Echo Server (websockets)

```
cd /docker/laravel-echo-server
cp laravel-echo-server.example.json laravel-echo-server.json
```

Adicione os endereços

```
# apenas para localhost

nano /etc/hosts

127.0.0.1       fairplay.test
127.0.0.1       api.fairplay.test
```

Suba os serviços pela primeira vez

```
docker-composer up --build
```
> **NOTA**: `--build` força que as alterações dos arquivos de configurações sejam sempre reconstruídas quando os serviços sobem.

> **NOTA**: No modo de desenvolvimento o comando `yarn dev` será executado no frontend, para execução em modo *hot reload*. Aguarda a compilação inicial finalizar antes de acessar o sistema.


Crie o banco de dados 

```
# com o mesmo nome utilizado em
 `fairplay-api/.env -> DB_DATABASE` .
``` 

Dependências do backend

```
# acesse o bash do container php-fpm
docker-compose exec php-fpm bash

# instalar
composer install && 
php artisan migrate --seed && 
php artisan key:generate && 
php artisan passport:install && 
php artisan storage:link && 

chmod -R 777 storage bootstrap/cache && 
rm -rf storage/logs/*.log
```

Dependências do frontend

```
As dependências do frontend são resolvidas automaticamente quando o container inicia, via:

# .env
NODE_ENTRYPOINT=/bin/bash -c "yarn && yarn build"
```

## URLs

- frontend: http://fairplay.test
- backend: http://api.fairplay.test

# PRODUÇÃO

Os mesmos procedimentos devem ser executados, com passos complementares.

## Repositórios

GIT CLONE --RECURSIVE

## Variáveis de Ambiente

Docker

```
# .env

NODE_ENTRYPOINT=/bin/bash -c "yarn && yarn build"
```

Frontend

Backend


### Laravel Echo Server

```
# /docker/laravel-echo-server/laravel-echo-server.json
devMode: true
```

### Caddy

- Domínio https://site  / https://api.site
- Descomentar / comentar confiuracoes

# NOTAS

- Ao alterar arquivos `.env` (backend, frontend ou docker) os serviçoes precisam ser reiniciados.