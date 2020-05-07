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

