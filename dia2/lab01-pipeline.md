## 1.1 Criando uma pipeline PHP

Partindo do princípio de que uma pipeline consiste em uma série de passos a serem executados para realizar uma tarefa, vamos tentar criar por escrito nossa primeira pipeline.



Ela deve ter passos definidos, condições de início e o que é necessário para executá-la, como quais ferramentas devem estar instaladas no ambiente.



Exemplo:

1. Instalar Composer
2. Instalar dependências do projeto com *composer install*
3. Executar testes



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