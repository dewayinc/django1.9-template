# Django Template

Esse projeto segue alguns principios do [The Twelve-Factor App](http://12factor.net/pt_br/). 

# Objetivo

Esse template tem como objetivo padronizar a criação de aplicações django na webfaction facilitando tanto o deploy como a instalação no ambiente de desenvolvimento. 

# Composição

Ele é composto de um template de projeto django, alterado para obedecer as regras citadas em [12factor/Config](http://12factor.net/pt_br/config), um hook `post-receive` do git para facilitar o deploy,
um `easy_install` customizado para prover a instalação de aplicações de terceiros individualmente por projeto e um `Vagrantfile` com configurações para seu ambiente de desenvolvimento. 

# Requisitos

- [Vagrant](https://www.vagrantup.com/)

- [Virtualenv](https://virtualenv.pypa.io/en/stable/)

# Configurando  Webfaction

1. [Crie uma aplicação Django 1.9.7 (mod_wsgi 4.5.2/Python 2.7)](https://docs.webfaction.com/software/django/getting-started.html).

2. [Abra uma seção ssh](https://docs.webfaction.com/user-guide/access.html#ssh).

3. Entre `cd $HOME/webapps/myapp` onde `myapp` é o nome da aplicação django que aparece no painel de controle.

4. Remova o projeto criado automaticamente `rm -rf myproject`.

5. Crie os direrorios onde ficarão o projeto e o repositório.

    ```bash
    mkdir myapp myapp.git 
       
    ```    
6. Entre `cd myapp.git` e inicie um repositório bare `git init --bare`.

7. Clone o projeto em sua máquina.

    ```bash
    git clone ssh://deway@web<SERVER>.webfaction.com/home/deway/webapps/myapp/myapp.git
    
    ```    
8. Caso não tenha instalado, [Instale virtualenv](https://virtualenv.pypa.io/en/stable/installation/).

9. Crie e ative uma virtualenv.

    ```bash
    virtualenv venv && source venv/bin/activate
    
    ```
10. Entre `cd myapp`.

11. Instale o django.

    ```bash
    pip install django==1.9.7
    
    ```
12. Crie um projeto usando este template.
    
    ```bash
    django-admin startproject --template=https://github.com/dewayinc/django1.9-template/archive/master.zip --name=Vagrantfile,post-receive myapp .
    
    ```
13. Copie o `hooks/post-receive` para o servidor.
    
    ```bash
    scp hooks/post-receive deway@web<SERVER>.webfaction.com:/home/deway/webapps/myapp/myapp.git/hooks
    
    ```
14. No servidor de permissão de execução ao hook.
    
    ```bash
    chmod +x $HOME/webapps/myapp/myapp.git/hooks/post-receive
    
    ```
15. Na sua máquina adicione o projeto ao git `git add .` e `git commit -m "Start Project"`.

16. Atualize o repositório remoto `git push origin master`.

17. No servidor instale os pacotes da aplicação.

    ```bash
    python2.7 easy_install.py -r requirements.txt
    
    ```
19. Crie um arquivo `.env` como no exemplo abaixo na pasta raiz do projeto, ele deve ficar no mesmo local em que o `manage.py`. Mais informações sobre o formato do arquivo pode ser encontrado [aqui](http://django-environ.readthedocs.io/en/latest/).
    
        SECRET_KEY=K89Zz9H0pj2e8xCXEH1ac7PSqx2s4JXy
        DEBUG=True
        ALLOWED_HOSTS=*
        DATABASE_URL=postgres://user:password@host:port/db_name
   
18. Reinicie o servidor.

    ```bash
    source  $HOME/webapps/myapp/apache2/bin/restart
    
    ```


