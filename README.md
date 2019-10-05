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
# Este é o caminho do storage configurado nas variáveis de ambiente.

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
cp fairplay-api/.env.example fairplay-api/.env
```

```
APP_URL=http://localhost:8081
APP_URL_FRONTEND=http://localhost


DB_DATABASE=fairplaydb_dev
DB_USERNAME=root
DB_PASSWORD=<senha>

SOCIAL_FACEBOOK_REDIRECT=http://localhost:8081/api/auth/redirect/facebook
SOCIAL_FACEBOOK_CLIENT_ID=
SOCIAL_FACEBOOK_CLIENT_SECRET=

SOCIAL_GOOGLE_REDIRECT=http://localhost:8081/api/auth/redirect/google
SOCIAL_GOOGLE_CLIENT_ID=
SOCIAL_GOOGLE_CLIENT_SECRET=
```

## 🐬 fairplay-web

Altere as variáveis de ambiente

```
cp fairplay-web/.env.example fairplay-web/.env
```
```
API_URL = http://localhost:8081/api
API_URL_SOCKET = http://localhost:8081:6001
API_URL_STORAGE = http://localhost:8081/storage
```

## 💻 Docker

Altere as variáveis de ambiente
```
cp .env.example .env

MYSQL_ROOT_PASSWORD=<mesma senha anterior>
```

Ajuste a configuração do Laravel Echo Server (websockets)

```
cp docker/laravel-echo-server/laravel-echo-server.example.json docker/laravel-echo-server/laravel-echo-server.json
```

Suba os serviços pela primeira vez

```
docker-compose up --build
```
> **NOTA**: `--build` força que as alterações dos arquivos de configurações sejam sempre reconstruídas quando os serviços sobem.

## ✏️ Notas

- Ao alterar arquivos `.env` (backend, frontend ou docker) os serviços precisam ser reiniciados.

- No caso do frontend a aplicação deve ser reconstruída com `yarn dev` ou `yarn build` (produção).


## ▶ Start

Use a ferramenta de preferência para criar o banco de dados.

```
# o nome do banco deve ser o mesmo utilizado nas variáveis de ambiente

create database fairplaydb_dev;
```

Em outro terminal, no repositório principal, acesse o bash do container `php-fpm` e instale as dependências do backend.

```
# bash
docker-compose exec php-fpm bash  

# install
composer install && 
php artisan migrate --seed && 
php artisan key:generate && 
php artisan passport:install && 
php artisan storage:link && 

chmod -R 777 storage bootstrap/cache && 
rm -rf storage/logs/*.log
```

Saia do terminal anterior e agora acesse o bash do container `node` para executar o frontend em modo desenvolvimento com *hot reload*. 

> **NOTA**: Este procedimento é apenas em desenvolvimento. Em produção a aplicação de frontend é construída através do entrypoint (`yarn build`).
```
docker-compose exec node bash
yarn && 
yarn dev
```


- frontend: http://localhost
- backend: http://localhost:8081




# PRODUÇÃO

Os mesmos procedimentos anteriores devem ser executados, com as seguintes alterações.

- Em produção todas urls devem ter o prefixo https://
- Ajuste as variáveis de ambiente do Docker

```
# .env
NODE_ENTRYPOINT=/bin/bash -c "yarn && yarn build"
```

- Ajuste as variáveis de ambiente do frontend

- Ajuste as variáveis de ambiente do backend

- Laravel Echo Server

```
# /docker/laravel-echo-server/laravel-echo-server.json
devMode: false
```

- Caddy

  - Os endereços de frontend e backend devem começar com "https://" e sem a porta.
  - Descomentar/comentar configurações para produção em `docker/caddy/Caddyfile`.


- Suba os serviços a primeira vez e observe nos logs se tudo ocorreu bem. Todas as imagens setão baixadas e instaladas (+- 10min). 
```
docker-compose up --build
```


- Como não existe um opção para um segundo terminal, pare os serviços e suba em modo background. E execute o processo de "Start".

```
docker-compose up -d --build
```

> **NOTA**: -d roda os serviços em background.