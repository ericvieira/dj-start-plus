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
+   path('', views.home, name='home'),
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
+   'django_extensions',
+   'projeto.core', 
]


- DATABASES = {
-     'default': {
-        'ENGINE': 'django.db.backends.sqlite3',
-        'NAME': BASE_DIR / 'db.sqlite3',
-    }
- }
+ default_dburl = 'sqlite:///' + str(BASE_DIR / 'db.sqlite3')

+ DATABASES = {
+     'default': config('DATABASE_URL', default=default_dburl, cast=dburl),
+ }


- LANGUAGE_CODE = 'en-us'
+ LANGUAGE_CODE = 'pt-br'

- TIME_ZONE = 'UTC'
+ TIME_ZONE = 'America/Sao_Paulo'


+ MIDDLEWARE_CLASSES = (
+     'whitenoise.middleware.WhiteNoiseMiddleware',
+ )


STATIC_URL = '/static/'
+ STATIC_ROOT = 'staticfiles'


+ # Email Configuration
+ EMAIL_BACKEND = config('EMAIL_BACKEND')
+ EMAIL_HOST = config('EMAIL_HOST')
+ EMAIL_PORT = config('EMAIL_PORT', cast=int )
+ EMAIL_USE_TLS = config('EMAIL_USE_TLS', cast=bool)
+ EMAIL_HOST_USER = config('EMAIL_HOST_USER')
+ EMAIL_HOST_PASSWORD = config('EMAIL_HOST_PASSWORD')

```

O arquivo `.env` foi gerado pelo script.



## Configurar Heroku

Criando o projeto no Heroku e configurando as variáveis de ambiente:
``` 
(.projeto) projeto$ heroku apps:create seu-projeto
(.projeto) projeto$ heroku config:set SECRET_KEY=`python contrib/keygen.py`
(.projeto) projeto$ heroku config:set DEBUG=False
(.projeto) projeto$ heroku config:set ALLOWED_HOSTS=.herokuapp.com
```


## Configurar SendGrid

``` 
(.projeto) projeto$ heroku addons:create sendgrid:starter
(.projeto) projeto$ heroku config:set EMAIL_BACKEND=django.core.mail.backends.smtp.EmailBackend EMAIL_HOST=smtp.sendgrid.net EMAIL_PORT=587 EMAIL_USE_TLS=True
(.projeto) projeto$ heroku config 
(.projeto) projeto$ heroku config:set EMAIL_HOST_USER=<user criado no sendgrid do heroku> EMAIL_HOST_PASSWORD=<senha criada no sendgrid do heroku>
```


Enviando a sua primeira versão para o **Heroku**:

``` 
(.projeto) projeto$ git add .
(.projeto) projeto$ git commit -m 'inicio'
(.projeto) projeto$ git push heroku master
```


E para terminar é só adicionar no **git** fazer o **commit** e fazer o **push** pro **Heroku** e está no ar. :smiley:
