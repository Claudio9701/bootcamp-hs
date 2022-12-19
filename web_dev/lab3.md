{% raw %}
# Lab 3: Sistema de usuarios

### Introducción
En este lab vamos a aprender como implementar el sistema de usuarios de Django. Con esto nuestros usuarios podran registrarse, iniciar sesion, cerrar sesion, recuperar su contraseña. Tambien vamos a poder gestionar nuestros usuarios mediante la pagina de administrador y agregar tipos de usuarios personalizados.

### Modelos de datos para usuarios

Para empezar debemos abrir nuestro proyecto y agregar 3 nuevos modelos. Primero el modelo base `Profile`, y a partir de este crearemos `Colaborador` y `Cliente`. Para poder integrar nuestros usuarios personalizados con el sistema de usuarios de Django debemos importar la siguiente clase en `main/models.py`:

```python
from django.contrib.auth.models import User
```

Luego definimos el modelo base `Profile` y usaremos el campo especial `OneToOneField` para relacionarlo con la clase `User` que hemos importado antes:

```python
class Profile(models.Model):
    # Relacion con el modelo User de Django
    user = models.OneToOneField(User, on_delete=models.CASCADE)
```

Finalmente agregamos los demas atributos igual que en el lab anterior:

```python
    ...
    # Atributos adicionales para el usuario
    documento_identidad = models.CharField(max_length=8)
    fecha_nacimiento = models.DateField()
    estado = models.CharField(max_length=3)
    ## Opciones de genero
    MASCULINO = 'MA'
    FEMENINO = 'FE'
    NO_BINARIO = 'NB'
    GENERO_CHOICES = [
        (MASCULINO, 'Masculino'),
        (FEMENINO, 'Femenino'),
        (NO_BINARIO, 'No Binario')
    ]
    genero = models.CharField(max_length=2, choices=GENERO_CHOICES)

    def __str__(self):
        return self.user.get_username()
```

Para el atributo `genero` hemos hecho algo nuevo, hemos creado algunas variables que se mostraran como opciones para nuestros usuarios a la hora de registrarse.

Ahora crearemos `Cliente`, este debe estar integrado con el modelo `Profile`:

```python
class Cliente(models.Model):
    # Relacion con el modelo Perfil
    user_profile = models.OneToOneField(Profile, on_delete=models.CASCADE)

    # Atributos especificos del Cliente
    preferencias = models.ManyToManyField(to='Categoria')

    def __str__(self):
        return f'Cliente: {self.user_profile.user.get_username()}'
```

Finalmente crearemos el modelo `Colaborador`:

```python
class Colaborador(models.Model):
    # Relacion con el modelo Perfil
    user_profile = models.OneToOneField(Profile, on_delete=models.CASCADE)

    # Atributos especificos del Colaborador
    reputacion = models.FloatField()
    cobertura_entrega = models.ManyToManyField(to='Localizacion')

    def __str__(self):
        return f'Colaborador: {self.user_profile.user.get_username()}'
```

> **No te olvides de migrar:** Luego de modificar el archivo models.py debemos utilizar los comandos `makemigrations` y `migrate` para que nuestros cambios hagan efecto.

#### Quiero espiar a mis usuarios!

Para visualizar nuestros modelos desde la página de administrador debemos añadir el siguiente código al archivo `main/admin.py`:

```python
# Importar clases Cliente y Colaborador
from .models import Cliente, Colaborador, Profile

class ClienteInline(admin.TabularInline):
    model=Cliente

class ColaboradorInline(admin.TabularInline):
    model=Colaborador

class ProfileAdmin(admin.ModelAdmin):
    inlines = [
        ClienteInline,
        ColaboradorInline
    ]

admin.site.register(Cliente)
admin.site.register(Colaborador)
admin.site.register(Profile, ProfileAdmin)
```


#### Ahora crearemos el formulario para registro

Django nuevamente nos da una base para comenzar, pero tendremos que agregar todos los atributos personalizados que tenemos de las clases `Profile`,  `Colaborador` y `Cliente`. Para crear un formulario debemos crear el archivo `main/forms.py`, dentro primero importaremos lo siguiente:

```python
from django import forms
from django.contrib.auth.forms import UserCreationForm
from django.contrib.auth.models import User
from .models import Localizacion, Categoria
```

Ahora vamos a crear una clase `UserForm` que tendra como super clase a `UserCreationForm` que acabamos de importar, esto nos dara agunas funcionalidades utiles como validacion de usurios duplicados, contrasenas, etc. En esta clase agregaremos todos los atributos de `User` y `Profile` que necesitamos obtener de nuestros usuarios:

```python
class UserForm(UserCreationForm):
    # django.contrib.auth.User attributes
    first_name = forms.CharField(max_length=150, required=False)
    last_name = forms.CharField(max_length=150, required=False)
    email = forms.EmailField(max_length=150)

    # Profile attributes
    documento_identidad = forms.CharField(max_length=8)
    fecha_nacimiento = forms.DateField()
    estado = forms.CharField(max_length=3)
    ## Opciones de genero
    MASCULINO = 'MA'
    FEMENINO = 'FE'
    NO_BINARIO = 'NB'
    GENERO_CHOICES = [
        (MASCULINO, 'Masculino'),
        (FEMENINO, 'Femenino'),
        (NO_BINARIO, 'No Binario')
    ]
    genero = forms.ChoiceField(choices=GENERO_CHOICES)
```

Tambien necesitamos agregar los datos de los modelo `Cliente` y `Colaborador`:

```python
class UserForm(UserCreationForm):
  ...
  # Cliente attributes
  is_cliente = forms.BooleanField(required=False)
  preferencias = forms.ModelChoiceField(queryset=Categoria.objects.all(), required=False)

  # Colaborador attributes
  is_colaborador = forms.BooleanField(required=False)
  reputacion = forms.FloatField(required=False)
  cobertura_entrega = forms.ModelChoiceField(queryset=Localizacion.objects.all(), required=False)
```

Hay dos intrusos! He anadido dos datos de ayuda: `is_cliente` e `is_colaborador`, mas adelante usaremos estas variables para saber que instancias debemos crear y guardar en nuestra base de datos.

> **ModelChoiceField:** Este campo permite utilizar los objetos de otro modelo de datos como opciones a seleccionar por el usuario. Muy util!

Finalmente dentro de la clase `UserForm` usaremos la clase `Meta`, esta clase nos permite indicarle que modelo de datos esta asociado a este formulario y tambien personalizarlo. Por ejemplo el atributo `field` permite definir el orden en el que los usuarios veran los campos a llenar del formulario:

```python
class Meta:
    model = User
    fields = ['username',
    'first_name',
    'last_name',
    'email',
    'documento_identidad',
    'fecha_nacimiento',
    'estado',
    'genero',
    'is_cliente',
    'preferencias',
    'is_colaborador',
    'reputacion',
    'cobertura_entrega',
    ]
```

¡Ahora hemos terminado con los modelos de datos y sus formularios!

# Veamos las vistas (pun intented)

Primero debemos importar algunas herramientas dentro de main/views.py:

```python
from django.views.generic import FormView, TemplateView
from django.urls import reverse_lazy
from django.contrib.auth import login

# Importamos forms.py
from .forms import *

#Importamos las clases recien creadas
from .models import *
```

Primero vamos a actualizar nuestra vista `home` y pasaremos de usar una funcion a una clase (porque somos cheveres):

 ```python
 class HomePageView(TemplateView):

    template_name = "main/home.html"

    def get_context_data(self, **kwargs):
        context = super().get_context_data(**kwargs)
        context['latest_products'] = Producto.objects.all()[:5]

        return context
```

Ahora vamos a crear nuestra vista para mostrar el formulario de registro de usuarios y recibir los datos de los usuarios:

```python
class RegistrationView(FormView):
    template_name = 'registration/register.html'
    form_class = UserForm
    success_url = reverse_lazy('home')
```

Esto seria suficiente si no tuvieramos los campos adicionales para `Profile`, `Cliente` y `Colaborador`, por ello necesitamos personalizar el metodo `form_valid` que se activa cuando nuestro usuario se registra con datos correctos:

```python
class RegistrationView(FormView):
  ...
  def form_valid(self, form):
    # This methos is called when valid from data has been POSTed
    # It should return an HttpResponse

    # Create User
    username = form.cleaned_data['username']
    first_name = form.cleaned_data['first_name']
    last_name = form.cleaned_data['last_name']
    email = form.cleaned_data['email']
    password = form.cleaned_data['password1']

    user = User.objects.create_user(username=username, first_name=first_name, last_name=last_name, email=email, password=password)
    user.save()
```

Aqui hemos grabado a nuestro usuario, ahora guardemos el `Profile` asociado:

```python
class RegistrationView(FormView):
  ...

  def form_valid(self, form):
    ...
    # Create Profile
    documento_identidad = form.cleaned_data['documento_identidad']
    fecha_nacimiento = form.cleaned_data['fecha_nacimiento']
    estado = form.cleaned_data['estado']
    genero = form.cleaned_data['genero']

    user_profile = Profile.objects.create( user=user, documento_identidad=documento_identidad, fecha_nacimiento=fecha_nacimiento, estado=estado, genero=genero)
    user_profile.save()
```

Luego, debemos verificar si es `Cliente` y guardarlo, aqui hay un nuevo tipo de dato `set` esto hace referencia a un conjunto de instancias de otro modelo, en este caso el atributo `preferencias` es un conjunto de instacias de `Categoria`.


```python
class RegistrationView(FormView):
  ...

  def form_valid(self, form):
    ...
    # Create Cliente if needed
    is_cliente = form.cleaned_data['is_cliente']
    if is_cliente:
        cliente = Cliente.objects.create(user_profile=user_profile)

        # Handle special attribute
        preferencias = form.cleaned_data['preferencias']
        preferencias_set = Categoria.objects.filter(pk=preferencias.pk)
        cliente.preferencias.set(preferencias_set)

        cliente.save()
```

Hacemos lo mismo para `Colaborador`:

```python
class RegistrationView(FormView):
  ...

  def form_valid(self, form):
    ...
    # Create Colaborador if needed
    is_colaborador = form.cleaned_data['is_colaborador']
    if is_colaborador:
        reputacion = form.cleaned_data['reputacion']
        colaborador = Colaborador.objects.create(user_profile=user_profile, reputacion=reputacion)

        # Handle special attribute
        cobertura_entrega = form.cleaned_data['cobertura_entrega']
        cobertura_entrega_set = Localizacion.objects.filter(pk=cobertura_entrega.pk)
        colaborador.cobertura_entrega.set(cobertura_entrega_set)

        colaborador.save()
```

Finalmente, logueamos al usuario y lo redirigimos a la url indicada en el atributo `success_url` que pusimos al inicio de la clase:

```python
class RegistrationView(FormView):
  ...

  def form_valid(self, form):
    ...
    # Login the user
    login(self.request, user)

    return super().form_valid(form)
```

### Si creo o modifico vistas debo actualizar mis urls!

En el archivo `main/urls.py` vamos a actulizar la vista para la pagina de inicio. Debemos borrar `path(‘’, views.home, name=’home’)` y agregaremos las siguientes url:

```python
...
urlpatterns = [
    path('', views.HomePageView.as_view(), name='home'),
    ...
    path('registro/', views.RegistrationView.as_view(), name='register'),
]
```

Luego en el archivo `urls.py` de tu aplicacion base (en mi caso es `linioexp/urls.py`) debemos agregar las urls de Django Auth:

```python
urlpatterns = [
    ...
    path('accounts/', include('django.contrib.auth.urls')),
]
```

### Un par de templates y nos vamos!

Primero crearemos un nuevo archivo en `main/templates/base.html`. Este archivo contendrá el siguiente código:

```html
<!DOCTYPE html>
<html>

<head>
    <!-- Usaremos utf-8 como formato de codificación -->
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
    <!-- Título de nuestra página -->
    <title>Linio Expres - Compras por internet</title>
    <meta name="viewport" content="width=device-width, initial-scale=1">
</head>

<body>
    {% block content %}{% endblock %}
</body>
```


Actualicemos `main/templates/main/home.html` con lo que hemos aprendido hoy:

```html
{% extends "base.html" %}

{% block content %}
    {% if user.is_authenticated %}
    <h3> Hola {{ user.get_username }} </h3>
    <p>
      <a href="{% url 'logout' %}">Cierra Sesion</a>
    </p>
    {% else %}
    <h3> Hola </h3>
    <p>
      <a href="{% url 'login' %}">Inicia Sesion</a> o
      <a href="{% url 'register' %}">Registrate</a>
    </p>
    {% endif %}

    <hr>
    <h5> Ultimos productos </h5>
    <ul>
      {% for producto in latest_products %}
        <li>
          <a href="{% url 'product-detail' producto.pk %}">
            {{ producto.nombre }}
          </a>
          - {{ producto.precio }}
        </li>
      {% empty %}
        <li>Aun no hay productos disponibles.</li>
      {% endfor %}
    </ul>
    <hr>
    <ul>
      <li><a href="{% url 'product-list' %}">Ver Lista de Productos Completa</a></li>
    </ul>
{% endblock %}
```

Con `user.is_authenticated` y `user.get_username` vamos a personalizar el saludo a nuestros visitantes.

Django Auth nos permite crear un template en `main/templates/registration/login.html` donde automaticamente mostrara un formulario para iniciar sesion:

```html
{% extends "base.html" %}

{% block content %}

{% if form.errors %}
<p>Your username and password didn't match. Please try again.</p>
{% endif %}

<form method="post" action="{% url 'login' %}">
{% csrf_token %}
<table>
<tr>
    <td>{{ form.username.label_tag }}</td>
    <td>{{ form.username }}</td>
</tr>
<tr>
    <td>{{ form.password.label_tag }}</td>
    <td>{{ form.password }}</td>
</tr>
</table>

<input type="submit" value="login">
<input type="hidden" name="next" value="{% url 'home'%}">
</form>

{# Assumes you setup the password_reset view in your URLconf #}
<p><a href="{% url 'password_reset' %}">Lost password?</a></p>

{% endblock %}

```

y tambien creemos `main/templates/registration/register.html` donde mostraremos el formulario de registrro:

```html
{% extends "base.html" %}

{% block content %}

<form method="post">
    {% csrf_token %}
    {{ form.as_p }}
    <input type="submit" value="Registrate">
</form>

{% endblock %}
```

### Ultimos detalles

 - Para ser redirigido a la pagina de inicio al cerrar sesion se debe definir siguiente variable al final del archivo `linioexp/settings.py`:

```python
LOGOUT_REDIRECT_URL = '/'
```

- Para poder utilizar la funcionalidad de recuperación de contraseña Django debe ser capaz de mandar correos, para ello es necesario configurar un server SMTP:

  1. Entra a https://mailtrap.io/ y regístrate
  2. Haz click en la ruedita de configuracion de 'Demo Inbox'
  3. En la lista desplegable que se encuentra debajo donde dice 'Integrations' selecciona 'Django'
  4. Copia y pega las variables mostradas en el archivo `linioexp/settings.py`.
  5. ¡Listo! Recibirás los mails en la web que te encuentras ahora mismo.


### Probemos las nuevas funciones!

## Siguiente Lab

Aprenderemos a implementar el carrito de compras (órdenes de compra) y procesar una compra.

{% endraw %}

[< Anterior lab](lab2.md)  
[Siguiente lab >](lab4.md)
