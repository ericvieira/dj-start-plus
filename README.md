# dj-start-plus
HowTo + Script para começar projeto com Django com as features do WTTD

Como estou criando um novo projeto, resolvi documentar um passo-a-passo de acordo com o WTTD para fácil e rápido o acesso em um único lugar. 
Esse tutorial considera que já foi feito o primeiro projeto e está com tudo instalado. 

Pode ser que tenha algum erro, pois comecei a fazer e depois fui anotando.
Qualquer erro ou melhoria é só comentar.


# Inicio 

No terminal fazer os seguintes comandos:

```     
~$ mkdir projeto
~$ cd projeto
projeto$ python -m venv .projeto
projeto$ source .projeto/bin/activate
(.projeto) projeto$ pip install django
(.projeto) projeto$ django-admin startproject projeto .
(.projeto) projeto$ pip install python-decouple
(.projeto) projeto$ pip install dj-database-url
(.projeto) projeto$ pip install dj-static
(.projeto) projeto$ pip install django-test-without-migrations
(.projeto) projeto$ manage startapp core
(.projeto) projeto$ mv core projeto 
```
 
Dentro do **PyCharm** em **Preferences** marcar a pasta `.projeto` como excluded para na hora de fazer busca por arquivos não aparecer um monte de arquivos que não aparecer essas dependências.  
![05|690x475](upload://yqnwo47raOd4xQGE9jWLlYZhGyI.png) 

E para terminar é só adicionar no **git** fazer o **commit** e fazer o **push** pro **Heroku** e está no ar. :smiley:


Editar na `urls.py`:

```diff
+ from projeto.core import views

urlpatterns = [
+   path('', views.home),
    path('admin/', admin.site.urls),
]
```
Editar na `core/views.py`:
```diff
+ def home(request):
+    return render(request, 'index.html')
```

Criar a pasta 'templates' dentro da pasta `core` e dentro de `core/templates` criar o arquivo `index.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<h1>Welcome to Projeto</h1>
</body>
</html>
```

Mudanças no  `settings.py`:

```diff  
+ from decouple import config, Csv
+ from dj_database_url import parse as dburl


+ SECRET_KEY = config('SECRET_KEY')
- SECRET_KEY = <secret key gerada pelo projeto>

- DEBUG = True
+ DEBUG = config('DEBUG', default=False, cast=bool)
- ALLOWED_HOSTS = []
+ ALLOWED_HOSTS = config('ALLOWED_HOSTS', default=[], cast=Csv())


INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
+   'test_without_migrations',
+   'projeto.core', 
]



- DATABASES = {
-     'default': {
-        'ENGINE': 'django.db.backends.sqlite3',
-        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
-    }
- }
+ default_dburl = 'sqlite:///' + os.path.join(BASE_DIR, 'db.sqlite3')

+ DATABASES = {
+     'default': config('DATABASE_URL', default=default_dburl, cast=dburl),
+ }


STATIC_URL = '/static/'
+ STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
```

Criar um arquivo `.env` e copiar as variáveis do  `settings.py` sem **espaço** e **aspas**:

```
SECRET_KEY=<sua SECRET_KEY> 
DEBUG=True
ALLOWED_HOSTS=127.0.0.1,.localhost,.herokuapp.com
```

No arquivo `wsgi.py` mudar a linha abaixo:
```git 
+ from dj_static import Cling

- application = get_wsgi_application()
+ application = Cling(get_wsgi_application())

```

No terminal:

```
(.projeto) projeto$ pip freeze > requirements.txt
```

## Configurar Git

```
(.projeto) projeto$ git init .

```
Criar arquivo `.gitignore`, e **atenção** para colocar  o seu **.projeto**
```bash 
.projeto
.env
.idea
*.sqlite3
*pyc
__pycache__
staticfiles
````

## Configurar Heroku

Editar o `requirements.txt`

```diff 
dj-database-url==0.5.0
dj-static==0.0.6
Django==2.2.5
django-test-without-migrations==0.6
python-decouple==3.1
pytz==2019.2
sqlparse==0.3.0
static3==0.7.0
+ gunicorn==19.9.0
+ psycopg2==2.8.3
```

Criar arquivo `Procfile`, com apenas a linha abaixo e **atenção** para colocar **projeto**.wsgi: 
```
web: gunicorn projeto.wsgi --log-file - :
```

Criando o projeto no Heroku e configurando as variáveis de ambiente:
``` 
(.projeto) projeto$ heroku apps:create seu-projeto
(.projeto) projeto$ heroku config:set SECRET_KEY=‘<sua secret key>’
(.projeto) projeto$ heroku config:set DEBUG=False
(.projeto) projeto$ heroku config:set ALLOWED_HOSTS=.herokuapp.com
```

E para terminar é só adicionar no **git** fazer o **commit** e fazer o **push** pro **Heroku** e está no ar. :smiley: