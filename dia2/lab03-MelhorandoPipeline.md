## Melhorando Pipeline

```yaml
# Official framework image. Look for the different tagged releases at:
# https://hub.docker.com/r/library/php
image: tutagomes/larabed:latest

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
  - pecl install xdebug
  - docker-php-ext-enable xdebug
  # Install Composer and project dependencies.
  - curl -sS https://getcomposer.org/installer | php
  - php composer.phar install
  # Install Node dependencies.
  # comment this out if you don't have a node dependency
  # - npm install
  # Copy over testing configuration.
  # Don't forget to set the database config in .env.testing correctly
  # DB_HOST=mysql
  # DB_DATABASE=project_name
  # DB_USERNAME=root
  # DB_PASSWORD=secret
  - cp .env.testing .env
  # Run npm build
  # comment this out if you don't have a frontend build
  # you can change this to to your frontend building script like
  # npm run build
  # npm run test
  # Generate an application key. Re-cache.
  - php artisan key:generate
  - php artisan config:cache
  # Run database migrations.
  - php artisan migrate
  # Run database seed
  - php artisan db:seed

test:
  script:
    # run laravel tests
    - php vendor/bin/phpunit --coverage-text --colors=never
    # run frontend tests
    # if you have any task for testing frontend
    # set it in your package.json script
    # comment this out if you don't have a frontend test
    # - npm test


```



E claro, podemos também colocar a própria badge e analisar se estamos no caminho certo:

![Exemplo Pipeline](https://gitlab.com/arthurgomesfaria/workflow/badges/exemplo-pipeline/pipeline.svg)



[Mais informações sobre badges](https://docs.gitlab.com/ee/ci/pipelines/settings.html)



### Pipelines Com Jobs Separados

```yaml
# Official framework image. Look for the different tagged releases at:
# https://hub.docker.com/r/library/php
image: php:latest

# Pick zero or more services to be used on all builds.
# Only needed when using a docker container to run your tests in.
# Check out: http://docs.gitlab.com/ce/ci/docker/using_docker_images.html#what-is-a-service
services:
  - mysql:latest

include:
  - template: "./Code-Quality.gitlab-ci.yml"

variables:
  MYSQL_DATABASE: workflow
  MYSQL_ROOT_PASSWORD: secret

# This folder is cached between builds
# http://docs.gitlab.com/ce/ci/yaml/README.html#cache
cache:
  paths:
    - vendor/
    - node_modules/

default:
  # This is a basic example for a gem or script which doesn't use
  # services such as redis or postgres
  before_script:
    # navigate to project folder
    - cd website

stages:
  - code_quality
  - test

test:
  stage: test
  before_script:
    - cd website
    # Update packages
    - apt-get update -yqq
    # Prep for Node
    - apt-get install gnupg -yqq
    # Upgrade to Node 8
    - curl -sL https://deb.nodesource.com/setup_8.x | bash -
    # Install dependencies
    - apt-get install git nodejs libcurl4-gnutls-dev libonig-dev libzip-dev libicu-dev libmcrypt-dev libvpx-dev libjpeg-dev libpng-dev libxpm-dev zlib1g-dev libfreetype6-dev libxml2-dev libexpat1-dev libbz2-dev libgmp3-dev libldap2-dev unixodbc-dev libpq-dev libsqlite3-dev libaspell-dev libsnmp-dev libpcre3-dev libtidy-dev -yqq
    # Install php extensions
    - docker-php-ext-install mbstring pdo_mysql curl json intl gd xml zip bz2 opcache
    # Install & enable Xdebug for code coverage reports
    - pecl install xdebug
    - docker-php-ext-enable xdebug
    # Install Composer and project dependencies.
    - curl -sS https://getcomposer.org/installer | php
    - php composer.phar install
    # Install Node dependencies.
    # comment this out if you don't have a node dependency
    # - npm install
    # Copy over testing configuration.
    # Don't forget to set the database config in .env.testing correctly
    # DB_HOST=mysql
    # DB_DATABASE=project_name
    # DB_USERNAME=root
    # DB_PASSWORD=secret
    - cp .env.testing .env
    # Run npm build
    # comment this out if you don't have a frontend build
    # you can change this to to your frontend building script like
    # npm run build
    # npm run test
    # Generate an application key. Re-cache.
    - php artisan key:generate
    - php artisan config:cache
    # Run database migrations.
    - php artisan migrate
    # Run database seed
    - php artisan db:seed
  script:
    # run laravel tests
    - php vendor/bin/phpunit --coverage-text --colors=never
    # run frontend tests
    # if you have any task for testing frontend
    # set it in your package.json script
    # comment this out if you don't have a frontend test
    # - npm test
```



Arquivo `Code-Quality.gitlab-ci.yml`:

```yaml
code_quality:
  stage: test
  image: docker:19.03.8
  allow_failure: true
  services:
    - docker:19.03.8-dind
  variables:
    DOCKER_DRIVER: overlay2
    DOCKER_TLS_CERTDIR: ""
    CODE_QUALITY_IMAGE: "registry.gitlab.com/gitlab-org/ci-cd/codequality:0.85.9"
  script:
    - |
      if ! docker info &>/dev/null; then
        if [ -z "$DOCKER_HOST" -a "$KUBERNETES_PORT" ]; then
          export DOCKER_HOST='tcp://localhost:2375'
        fi
      fi
    - docker pull --quiet "$CODE_QUALITY_IMAGE"
    - cd website
    - docker run
        --env SOURCE_CODE="$PWD"
        --volume "$PWD":/code
        --volume /var/run/docker.sock:/var/run/docker.sock
        "$CODE_QUALITY_IMAGE" /code
  artifacts:
    reports:
      codequality: gl-code-quality-report.json
    expire_in: 1 week
  dependencies: []
  only:
    refs:
      - branches
      - tags
  except:
    variables:
      - $CODE_QUALITY_DISABLED

```

