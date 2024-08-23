# Primeiro Arquivo

## Primeiros Passos 

Primeiramente iremos criar o core do projeto. Onde estara todos os arquivos de configuração do projeto. Esse será o coração do desenvolvimento.
```bash
django-admin startproject core .
```

Criaremos nosso primeiro app para que possamos apartar nosso desenvolvimento
```bash
python manage.py startapp usuarios
```

* Adicionando app novo para cadastrar 

---

1. precisei criar uma rota dentro de uma rota. Por exemplo? usuários/cadastro. Dessa forma o que foi feito é a criação de um novo arquivo de url dentro do app criado.
```
URL no Core

from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('usuarios/', include('usuarios.urls'))
]


URL no usuarios

from django.urls import path
from . import views

urlpatterns = [
    path('cadastro/', views.cadastro, name="cadastro")
]
```

> Dessa forma ao acessar o usuários, podemos ter sub rotas para elas.


2. Criando templates

> Primeira coisa que tenho que fazer é adicionar ao settings onde se encontra os templates. Então o que eu vou fazer é usar a variavel "BASE_DIR" que já existe e possui o caminho do projeto. Dessa forma somente adicionamos o complemento de onde a pasta de templates se encontra.
```
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [os.path.join(BASE_DIR, 'templates')],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]

Nesse caso importamos a lib "os" para usar o "os.path.join" para fazer com que ele coloque as / na direção correto independente do sistema operacional
```

3. Usando template

Primeira coisa que precisamos fazer ao usar um tamplate é dizer a qual arquivo estamos referenciando usando o "extends" e o nome do template referenciado. O que fazemos é criar o tamplate da forma que queremos e simplemente usarmos blocks para chamar a formatação

```
{% extends "base.html" %}

{%block 'title'%}Teste de Titulo{% endblock %}

{%block 'body'%}
<h1>Titulo do template</h1>

<p>Texto do template base</p>
{% endblock %}
```

> Dessa forma tudo que adicionemos dentro dos blocks será formatado de acordo com o arquivo base e a formatação que está em cadas seção chamada.

4. Confgurando arquivos Static
```
STATIC_URL = '/static/'
STATICFILES_DIRS = (os.path.join(BASE_DIR, '/usuarios/static'),)
STATIC_ROOT = os.path.join('staticfiles')

MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
MEDIA_URL = '/media/'s
```

5. Fazendo migração
```
python manage.py makemigrations 
python manage.py migrate
```

01:08