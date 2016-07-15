# Django Template

Esse projeto segue alguns principios do [The Twelve-Factor App](http://12factor.net/pt_br/). 

# Requisitos

- [Vagrant](https://www.vagrantup.com/)

- [Virtualenv](https://virtualenv.pypa.io/en/stable/)

#Configurando  Webfaction

1. [Crie uma aplicação Django 1.9.7 (mod_wsgi 4.5.2/Python 2.7)](https://docs.webfaction.com/software/django/getting-started.html) 

2. [Abra uma seção ssh](https://docs.webfaction.com/user-guide/access.html#ssh).

3. Entre `cd $HOME/webapps/myproject` onde `myproject` é o nome da aplicação django que aparece no painel de controle.

4. Crie e entre no diretório que será nosso repositório `mkdir myproject.git && cd  myproject.git` e inicialize um repositório bare `git init --bare`.

5. Clone o projeto em sua máquina

```
git clone ssh://deway@web<SERVER>.webfaction.com/home/deway/webapps/myproject/myproject.git
```

6. Caso não tenha instalado, [Instale virtualenv](https://virtualenv.pypa.io/en/stable/installation/).

7. Crie e ative uma virtualenv

```
virtualenv venv && source venv/bin/activate
```

8. Entre `cd myproject`.

9. Instale o django

```
pip install django==1.9.7
```

10. Crie um projeto usando este template

```
django-admin startproject --template=https://github.com/dewayinc/django1.9-template/archive/master.zip --name=Vagrantfile,post-receive myproject .
```

11. Copie o `hooks/post-receive` para o servidor

```
scp hooks/post-receive deway@web<SERVER>.webfaction.com:/home/deway/webapps/myproject/myproject.git/hooks
```

12. No servidor execute

```
chmod +x $HOME/webapps/myproject/myproject.git/hooks/post-receive
```

13. Na sua máquina adicione os arquivos ao git `git add .` e `git commit -m "Start Project"`

14. Atualize o repositório remoto `git push origin master`

15. No servidor instale os pacotes da aplicação

```
python2.7 easy_install.py -r requirements.txt
```

16. Reinicie o servidor 

```
source  $HOME/webapps/myproject/apache2/bin/restart
```


