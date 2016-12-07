# Django Template

Esse projeto segue alguns princípios do [The Twelve-Factor App][12factor-link].

### Objetivo

Padronizar a criação de aplicações _Django_ na _WebFaction_ facilitando tanto o _deploy_ quanto a instalação no ambiente de desenvolvimento. 

### Composição

* _Template_ de projeto _Django_, alterado para obedecer as regras citadas em [12factor/config][12factor-config-link];
* _Hook_ `post-receive` do _Git_ para facilitar o _deploy_;
* Modificação do `easy_install` para prover a instalação de aplicações de terceiros individualmente por projeto;
* Configuração do ambiente de desenvolvimento com `Vagrantfile`.

### Requisitos

- [Vagrant][vagrant-link]

- [Virtualenv][virtualenv-link]

### Configurando ambiente de produção (Webfaction)

1. [Crie uma aplicação Django 1.9.8 (mod_wsgi 4.5.2/Python 2.7)][webfaction-django-link].

2. [Abra uma sessão ssh][webfaction-ssh-link].

3. Execute `cd $HOME/webapps/myapp`, onde `myapp` é o nome da aplicação _Django_ que aparece no painel de controle.

4. Remova o projeto criado automaticamente: `rm -rf myproject`.

5. Crie os diretórios do projeto e do repositório:

    ```bash
    mkdir myapp myapp.git
    ```

6. Execute `cd myapp.git` e inicie um repositório _bare_ com `git init --bare`.

7. Clone o projeto em sua máquina:

    ```bash
    git clone ssh://deway@web<SERVER>.webfaction.com/home/deway/webapps/myapp/myapp.git    
    ```

8. Crie e ative uma _virtualenv_:

    ```bash
    virtualenv venv && source venv/bin/activate    
    ```

9. Execute `cd myapp`.

10. Instale o _Django_:

    ```bash
    pip install django==1.9.8
    ```

11. Crie um projeto usando este _Template_:

    ```bash
    django-admin startproject --template=https://github.com/dewayinc/django1.9-template/archive/master.zip --name=Vagrantfile,post-receive myapp .    
    ```

12. Copie o `hooks/post-receive` para o servidor:

    ```bash
    scp hooks/post-receive deway@web<SERVER>.webfaction.com:/home/deway/webapps/myapp/myapp.git/hooks
    ```

13. No servidor, dê permissão de execução ao _hook_:

    ```bash
    chmod +x $HOME/webapps/myapp/myapp.git/hooks/post-receive
    ```

14. Na sua máquina, adicione o projeto ao _Git_:

    ```bash
    git add . && git commit -m "Start Project"
    ```

15. Atualize o repositório remoto:

    ```bash
    git push origin master
    ```

16. No servidor, instale os pacotes da aplicação:

    ```bash
    python2.7 easy_install.py -r requirements.txt
    ```
    > Após a intalação atualize o arquivo `requirements.txt com a versão dos pacotes que está usando. Ex: django==1.9.8

17. Copie o arquivo `.env-example` como `.env` e modifique os valores de acordo com o exemplo abaixo ([mais detalhes sobre o formato do arquivo][django-environ-link]).

    ```
    SECRET_KEY=K89Zz9H0pj2e8xCXEH1ac7PSqx2s4JXy
    DEBUG=True
    ALLOWED_HOSTS=*
    DATABASE_URL=postgres://user:password@host:port/db_name
    ```

18. No servidor entre `cd $HOME/webapps/myapp/myapp/` e execute:

    ```bash
    python2.7 manage.py migrate && python2.7 manage.py collectstatic --noinput
    ```

19. Reinicie o servidor.

    ```bash
    source  $HOME/webapps/myapp/apache2/bin/restart    
    ```

### Configurando o ambiente de desenvolvimento com Vagrant

1. Instale o [Vagrant][vagrant-link];

2. Clone o projeto de um dos repositórios indicados;

    > Atualmente a Deway usa o Bitbukcket.

3. Edite o arquivo `.git/config` do repositório e adicione a linha abaixo na instrução `[remote "origin"]`:

    ```bash
    url = ssh://deway@web<SERVER>.webfaction.com/home/deway/webapps/<NOME_DA_APLICACAO>/<NOME_DA_APLICACAO>.git    
    ```

4. Copie o arquivo `.env-example` como `.env` e altere o valores de acordo com o _app_ como no exemplo abaixo:
 
    ```
    SECRET_KEY=K89Zz9H0pj2e8xCXEH1ac7PSqx2s4JXy
    DEBUG=True
    ALLOWED_HOSTS=*
    DATABASE_URL=postgres://myapp:myapp@127.0.0.1:5432/myapp
    ```
    > No Vagrantfile existe a instrução para criação de um banco de dados postgres. O usuário, senha e nome do banco é o nome da aplicação.

5. Entre no diretorio onde fica o `Vagrantfile` e inicie o vagrant.

    ```bash
    vagrant up
    ```

[12factor-link]: http://12factor.net/pt_br/
[12factor-config-link]: http://12factor.net/pt_br/config
[vagrant-link]: https://www.vagrantup.com/
[virtualenv-link]: https://virtualenv.pypa.io/en/stable/
[webfaction-django-link]: https://docs.webfaction.com/software/django/getting-started.html
[webfaction-ssh-link]: https://docs.webfaction.com/user-guide/access.html#ssh
[django-environ-link]: http://django-environ.readthedocs.io/en/latest/