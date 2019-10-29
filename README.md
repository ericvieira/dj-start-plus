# dj-start-plus
Script para começar projeto com Django com as features do WTTD

Rode o script com o nome do seu projeto:
```
~$ ./dj-script projeto
```

Ele vai criar a pasta e configurar o ambiente e instalar as libs, e criar o projeto django e um app core.

Depois de rodar vem a parte manual:

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

O arquivo `.env` foi gerado pelo script.


No arquivo `wsgi.py` mudar a linha abaixo:
```git 
+ from dj_static import Cling

- application = get_wsgi_application()
+ application = Cling(get_wsgi_application())

```

Comando para gerar as pastas para arquivos estáticos
```
manage collectstatic --noinput
```

## Configurar Heroku

Criando o projeto no Heroku e configurando as variáveis de ambiente:
``` 
(.projeto) projeto$ heroku apps:create seu-projeto
(.projeto) projeto$ heroku config:set SECRET_KEY=‘<sua secret key>’
(.projeto) projeto$ heroku config:set DEBUG=False
(.projeto) projeto$ heroku config:set ALLOWED_HOSTS=.herokuapp.com
```

E para terminar é só adicionar no **git** fazer o **commit** e fazer o **push** pro **Heroku** e está no ar. :smiley: