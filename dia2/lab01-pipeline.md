## 1.1 Criando uma pipeline PHP

Partindo do princípio de que uma pipeline consiste em uma série de passos a serem executados para realizar uma tarefa, vamos tentar criar por escrito nossa primeira pipeline.



Ela deve ter passos definidos, condições de início e o que é necessário para executá-la, como quais ferramentas devem estar instaladas no ambiente.



Exemplo:

1. Instalar Composer
2. Instalar dependências do projeto com *composer install*
3. Executar testes



```yaml
# Official framework image. Look for the different tagged releases at:
# https://hub.docker.com/r/library/php
image: php:latest

# Pick zero or more services to be used on all builds.
# Only needed when using a docker container to run your tests in.
# Check out: http://docs.gitlab.com/ce/ci/docker/using_docker_images.html#what-is-a-service
services:
  - mysql:latest

variables:
  MYSQL_DATABASE: workflow
  MYSQL_ROOT_PASSWORD: secret

# This folder is cached between builds
# http://docs.gitlab.com/ce/ci/yaml/README.html#cache
cache:
  paths:
    - vendor/
    - node_modules/

# This is a basic example for a gem or script which doesn't use
# services such as redis or postgres
before_script:
  # navigate to project folder
  - cd website
  - curl -sS https://getcomposer.org/installer | php
  - php composer.phar install
  - cp .env.testing .env
  - php artisan key:generate
  - php artisan config:cache
  - php artisan migrate
  - php artisan db:seed

test:
  script:
    # run laravel tests
    - php vendor/bin/phpunit --coverage-text --colors=never
```



## 1.2 Criando uma pipeline NPM

Assim como o Composer, podemos utilizar o NPM para gerenciar as dependências do nosso projeto Node.

Para isso, vamos criar rapidamente um novo projeto no GitLab e chamá-lo de interface1.

Lá dentro, vamos iniciar um `npm` através do comando `npm init`.

Vamos instalar pelo menos uma dependência, como por exemplo o express, um servidor simples escrito em Node

```bash
npm i express
```

Agora, só commitar o código!



Novamente, vamos definir alguns passos para criar nossa pipeline.

Exemplo:

1. Instalar Node
2. Instalar dependências do projeto com *npm install*
3. Executar testes