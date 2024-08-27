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


## Criando Tela de Cadastro

Pegamos os arquivos html já criadas e vamos apenas fazer o tratamento com as ações que serão tomadas pelo front e criar o processamento do backend.

1. Criamos 
```
<form action="{% url 'cadastro' %}" method="POST">{% csrf_token %}
    <label>Username</label>
    <input type="text" class="form-control input-pers" name="username" placeholder="Digite seu username">
    <br />
                    
    <label>Senha</label>
    <input type="password" class="form-control input-pers" name="senha" placeholder="Digite sua senha">
    <br />

    <label>Confirmar senha</label>
    <input type="password" class="form-control input-pers" name="confirmar_senha" placeholder="Confirme sua senha">

    <div class="centralizar">
        <input type="submit" class="btn btn-indigo" value="Cadastrar">
        <a href="{% url 'logar' %}" class="texto">Já possuo uma conta</a>
    </div>
</form>
```

> Adicionaremos o action do form dizendo que a url que será enviada é a propria e o method de envio é um post. Adicionando tbm o método de segunraça evitando dessa forma maneira de evitar falsificação.


Fomos até a view dá página especifica e adicionamos lógica:

if request.method == "GET":
        return render(request, 'cadastro.html')
    elif request.method == "POST": 
        username = request.POST.get('username')
        senha = request.POST.get('senha')
        confirmar_senha = request.POST.get('confirmar_senha')

        if not senha == confirmar_senha:
            messages.add_message(request, constants.ERROR, 'As senhas não coincidem.')
            return redirect('/usuarios/cadastro')
        
        if len(senha) < 6:
            messages.add_message(request, constants.ERROR, 'A senha precisa ter pelo menos 6 dígitos')
            return redirect('/usuarios/cadastro')


        users = User.objects.filter(username=username)
        if users.exists():
            messages.add_message(request, constants.ERROR, 'Já existe um usuário cadastrado com esse username')
            return redirect('/usuarios/cadastro')
    
        user = User.objects.create_user(
            username=username,
            password=senha
        )

        return  redirect('/usuarios/logar')

* Verificamos se a chamada está sendo feita um método GET ou POST pois cada uma tem uma maneira diferente de agir

* Adicionamos verificações especificas para não cumprir o cadastro que seria senha divergente, senha muito curta e usuário já usado

* Depois usamos uma lib do Django cadastramos um usuário na tabela "auth_user"

* Configuramos os alertas para mandar um personalizado, precisamos cadastrar no settings e importar e usar no view
```
from django.contrib import messages
from django.contrib.messages import constants
```

## Logando

O processo de Login é muit similar ao que fazemos para o o cadastro. Pegamos da requisição o username e password e instaciamos esse usuário user usando essa ferramenta de authenticate passando as variaveis. Com essa instancia temos somente a informação se o usários se encontra na base. Caso esteja nós fazemos o login com outro atributo da lib, caso não esteja levantamos erro.

```
def logar(request):
    if request.method == "GET":
        return render(request, 'login.html')
    if request.method == "POST":
        username = request.POST.get('username')
        senha = request.POST.get('senha')

        user = auth.authenticate(request, username=username, password=senha)
        if user:
            auth.login(request, user)
            return redirect('/empresarios/cadastrar_empresa')
        
        messages.add_message(request,constants.ERROR, 'Usuário ou senha inválidos')
        return redirect('/usuarios/logar')
```

> Se tiver o usuário retorna o nome, se não tiver retorna none

> python manage.py createsuperuser 


### Criando novo app

Agora estamos criando um novo app para comportar todas as interações referente a cadastro de empresas



```
Passando Valor na View

# Create your views here.
def cadastrar_empresa(request):
    x = 1
    if request.method == 'GET':
        return render(request, 'cadastrar_empresa.html', {'valor': x})
```

> enctype='multipart/form-data' Usado quando queremos enviar arquivos em nosso formulario, precisamos passar essa propriedade