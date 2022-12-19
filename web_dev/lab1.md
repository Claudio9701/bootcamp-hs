[< Volver al inicio](README.md)

# Lab 1: Creando tu primera aplicación Django I

El objetivo del presente laboratorio es guiar al estudiante en la creación de una aplicación web simple para una empresa de e-commerce.

Consistirá de dos partes:

- Un sitio público donde se verá información de la empresa.
- Un sitio de administrador para manejar los datos de la web.

Asumiendo que se tiene Django instalado, verificamos la version utilizando el siguiente comando desde el programa terminal / command prompt / powershell dependiendo de que sistema operativo uses:

```sh
python -m django version
```

Si todo esta bien deberías ver la versión de Django instalada en tu sistema. Si no, te aparecerá el error "No module named django".

## Creación del proyecto

Django tiene un comando que permite generar automaticamente todos los archivos iniciales necesarios para empezar a desarrollar nuestra aplicación web (Configuración de base de datos, ajustes de servidor, entre otros).

Desde la linea de comandos, colócate en la carpeta donde deseas crear tu proyecto y utiliza el comando:

```sh
django-admin startproject linioexp
```

Esto creará una carpeta llamada `linioexp`, veamos su contenido:

```
linioexp/
    manage.py
    linioexp/
        __init__.py
        settings.py
        urls.py
        asgi.py
        wsgi.py
```

Estos archivos son:

- El primer `linioexp` es la *carpeta raíz* del proyecto.
- `manage.py`: Te permite interactuar con tu proyecto desde la línea de comandos.
- El segundo `linioexp/` es la carpeta donde se encuentra tu aplicación web.
- `linioexp/__init__py`: Es un archivo vacío que indica a Python que esa carpeta debe tratarla como un paquete de Python.
- `linioexp/settings.py`: Es el archivo donde se realiza la configuración para este proyecto.
- `linioexp/urls.py`: Es el archivo donde se declaran las rutas URL que tendrá tu proyecto. Se puede entender como una "tabla de contenidos".
- `linioexp/asgi.py`: Sirve como punto de entrada para servidores compatibles con ASGI.
- `linioexp/wsgi.py`: Sirve como punto de entrada para servidores compatibles con WSGI.

## El servidor de desarrollo

Desde el terminal, entraremos a la carpeta del proyecto en django que acabamos de crear. Esto lo haremos a través del comando cd que significa 'change directory'

```sh
cd linioexp
```

Para verificar si el proyecto funciona bien, nos colocamos en la carpeta raiz `linioexp` y utilizamos el siguiente comando:

```sh
python manage.py runserver
```

Verás el siguiente mensaje en la linea de comandos:

```sh
Performing system checks...

System check identified no issues (0 silenced).

You have unapplied migrations; your app may not work properly until they are applied.
Run 'python manage.py migrate' to apply them.

August 24, 2020 - 15:50:53
Django version 3.1, using settings 'linioexp.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

> *Nota* No te preocupes por el mensaje de la migración de la base de datos. Lo solucionaremos más adelante.

Ya has iniciado tu servidor de desarrollo Django, este te permitirá ver rápidamente lo cambios y actualizaciones que hagas en tu aplicación. Sin embargo, para que esté disponible públicamente en internet hay que realizar otros ajustes que veremos un próximo laboratorio.

Si entras a http://127.0.0.1:8000/ desde tu navegador. Verás una página de bienvenida con un cohete. ¡Todo salió bien!

> *Actualización automática de runserver* Cada vez que realices un cambio en tu código automáticamente se reiniciará el servidor para que puedas visualizarlo, no es necesario que lo reinicies manualmente.

## Creamos nuestra primera aplicación

Con el proyecto ya configurado, ahora podemos empezar a desarrollar nuestra página.

Django tiene un comando para crear automáticamente aplicaciones dentro de nuestros proyectos. Las aplicaciones sirven para organizar mejor diferentes funcionalidades de nuestro proyecto, sin embargo, para un ejemplo simple solo necesitamos una aplicación.

Para crearla debemos colocarlos en la carpeta raíz de nuestro proyecto y ejecutar el comando:

```sh
python manage.py startapp main
```

Esto creará la siguiente estructura de carpetas:

```
main/
    __init__.py
    admin.py
    apps.py
    migrations/
        __init__.py
    models.py
    tests.py
    views.py
```


## Implementando nuestra primera vista

Debemos abrir el archivo `main/views.py` y colocar el siguiente código:

```python
from django.http import HttpResponse

def home(request):
  return HttpResponse("Hola Mundo. Te encuentras en la página de inicio del Linio Express")
```

Esta es la vista más simple en Django. Para poder ingresar a esta es necesario relacionarla con una URL.

Para esto debemos crear una un archivo `urls.py` dentro de la carpeta `main`. Luego, incluimos el código:

```python
from django.urls import path

from . import views

urlpatterns = [
    path('', views.home, name='home'),
]
```

Luego debemos incluir las URLs de `main` en las URLs del proyecto principal desde `linioexp/urls.py`. Para esto nos ayudamos de la función `include()`, importada desde `django.urls`:

```python
from django.contrib import admin
from django.urls import include, path

urlpatterns = [
    path('', include('main.urls')),
    path('admin/', admin.site.urls),
]
```

[Siguiente lab >](lab2.md)
