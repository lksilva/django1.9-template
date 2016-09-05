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

# Configurando ambiente de produção  (Webfaction)

1. [Crie uma aplicação Django 1.9.8 (mod_wsgi 4.5.2/Python 2.7)](https://docs.webfaction.com/software/django/getting-started.html).

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
8. Em sua maquina caso não tenha instalado, [Instale virtualenv](https://virtualenv.pypa.io/en/stable/installation/).

9. Crie e ative uma virtualenv.

    ```bash
    virtualenv venv && source venv/bin/activate    
    ```
10. Entre `cd myapp`.

11. Instale o django.

    ```bash
    pip install django==1.9.8    
    ```
12. Crie um projeto usando este template.
    
    ```bash
    django-admin startproject --template=https://github.com/lksilva/django1.9-template/archive/patch-1.zip --name=Vagrantfile,post-receive myapp .    
    ```
13. Copie o `hooks/post-receive` para o servidor.
    
    ```bash
    scp hooks/post-receive deway@web<SERVER>.webfaction.com:/home/deway/webapps/myapp/myapp.git/hooks    
    ```
14. No servidor de permissão de execução ao hook.
    
    ```bash
    chmod +x $HOME/webapps/myapp/myapp.git/hooks/post-receive    
    ```
15. No seu ambiente local execute `pip install -r requirements.txt`, depois `pip freeze`, copie e atualize os requirements.txt.
 
16. Na sua máquina adicione o projeto ao git `git add .` e `git commit -m "Start Project"`.

16. Atualize o repositório remoto `git push origin master`.
    
17. Modifique o arquivo `.env` e `.env-dev`, crie uma nova SECRET_KEY e atualize o DATABASE_URL com a credencial de acesso ao banco "produção" e "desenvolvimento" respectivamente. Mais informações sobre o formato do arquivo pode ser encontrado [aqui](http://django-environ.readthedocs.io/en/latest/).
    
        SECRET_KEY=K89Zz9H0pj2e8xCXEH1ac7PSqx2s4JXy
        DEBUG=True
        ALLOWED_HOSTS=*
        DATABASE_URL=postgres://user:password@host:port/db_name

18. Atualize o repositório remoto novamente, passos 16 e 17.

19. No servidor entre `cd $HOME/webapps/myapp/myapp/` e execute.

    ```bash    
    python2.7 manage.py migrate 
    
    python2.7 manage.py collectstatic --noinput    
    ```
20. No servidor entre `cd $HOME/webapps/myapp/apache2/conf` e modifique o arquivo httpd.conf

21. Reinicie o servidor.

    ```bash
    source  $HOME/webapps/myapp/apache2/bin/restart    
    ```


# Configurando  Ambiente de desenvolvimento (Vagrant)


1. Instale o [Vagrant](https://www.vagrantup.com/).

2. Clone o projeto de um dos repositórios indicados. 

    > Atualmente a Deway usa o Bitbukcket.

3. Edite o arquivo `.git/config` do repositório e adicione a linha abaixo na instrução `[remote "origin"]`

    ```bash
    url = ssh://deway@web<SERVER>.webfaction.com/home/deway/webapps/<NOME_DA_APLICACAO>/<NOME_DA_APLICACAO>.git  
    url = https://<MEU_USUARIO>@bitbucket.org/deway/NOME_DA_APLICACAO.git
    fetch = +refs/heads/*:refs/remotes/origin/*
    fetch = +refs/heads/*:refs/remotes/bitbucket/*
    ```
    Exemplo:
    ```bash
        url = ssh://deway@web522.webfaction.com/home/deway/webapps/guiabase_django/guiabase_django.git
        url = https://LucasDeway@bitbucket.org/rafaelthemendes/deway-guia-base-django.git
        fetch = +refs/heads/*:refs/remotes/origin/*
        fetch = +refs/heads/*:refs/remotes/bitbucket/*
    ```

4. Entre no diretorio onde fica o `Vagrantfile` e inicie o vagrant.

    ```bash
    vagrant up    
    ```
        
   
