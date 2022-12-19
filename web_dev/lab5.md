{% raw %}
# Lab 5: Búsqueda e Imágenes de Productos

### Introducción
En este lab vamos a aprender cómo hacer que nuestros usuarios puedan buscar productos. También, veremos cómo anadir imágenes de los productos y mejoraremos un poco la aparencia general de la tienda.

### ¿Cómo te encuentro?

Una funcionalidad básica para cualquier ecommerce es permitir que sus usuarios puedan buscar los productos por nombre, categoría, entre otros. En este ejemplo vamos a implementar una caja de búsqueda por nombre. Para ello debemos realizar una comunicación entre cliente y servidor, como ya hemos visto anteriormente para esto se utilizan los formularios. Los formularios pueden realizar peticiones de POST o GET. Para simplificar las cosas, quedemos en que las peticiones POST implican envío de datos sensibles (normalmente privados) del cliente hacia el servidor con los cuales se realizarán modificaciones en la base de datos. Por otro lado las peticiones GET envian datos no sensibles mediante la URL hacia el servidor (Por ejemplo: https://pythonprogramming.net/search/?q=django), estos datos no modifican la base de datos.

Para agregar nuestra caja de búsqueda debemos adicionar el siguiente código en el archivo `main/templates/base.html` debajo del título de la página:

```HTML
...
<div class="content">
    <form action="{% url 'product-list' %}" method="get">
        <input class="input is-rounded" type="text" name="q" placeholder="Busca tu producto...">
    </form>
</div>
...
```

Al escribir una palabra en la caja de búsqueda y apretar la tecla Enter estaremos enviando dicha palabra hacia la vista de lista de productos `ProductListView` mediante el parámetro `q` en la URL: http://localhost:8000/productos?**q=drone**. Ahora para utilizar este parámetro debemos personalizar el método `get_queryset` de `ProductoListView`, para ello vamos al archivo `main/views.py`:

```python
def get_queryset(self):
    query = self.request.GET.get('q')
    if query is not None:
        object_list = Producto.objects.filter(nombre__icontains=query)
        return object_list
    else:
        return Producto.objects.all()
```

¡Listo! Probemos nuestra cajita de búsqueda.

### Con fotito pues ...

Como nosotros sabemos que *la belleza entra por los ojos* vamos a agregar imágenes a nuestros productos para que nuestra página se vea mejor.

#### Trabajemos el back-end :hammer:

Gracias a el módulo `models` de `Django` anadir un atributo de imagen es muy sencillo, solo debemos usar la clase `models.ImageField`. ¿Y si quiero anadir más de 1 imagen? Fácil, creamos un modelo que tenga una relación de uno a muchos (Un producto - muchas imágenes de productos) utilizando el viejo conocido `ForeignKey` del mismo módulo. Para ello en el archivo `main/models.py` debemos colocar:

```python
...
class ProductoImage(models.Model):
    product = models.ForeignKey('Producto', on_delete=models.CASCADE, related_name='images')
    image = models.ImageField(upload_to="products", null=True, blank=True)
...
```

> *Importante:*  Antes de aplicar los comandos de migraciones debemos instalar la librería [Pillow](https://pypi.org/project/Pillow/) - con el comando `pip install Pillow` - que es necesaria para el uso de las imágenes en Python.

Vemos un parámetro nuevo al usar `ForeignKey`: `related_name`. Este nombre (`images`) que hemos colocado nos permitira acceder a todas las imágenes asociadas a un producto de manera sencilla: `producto.images.all()`. Por otro lado en `ImageField` estamos indicando el parámetro `upload_to`, esto para que las imágenes sean almacenadas en una carpeta llamada `products`.

Ya casi terminamos, para facilitar la subida de imágenes de cada producto vamos a colocar un `Inline` en el admin de `Producto` (al igual que hicimos con `Profile`, `Cliente` y `Colaborador`). Debemos ir al archivo `main/admin.py` y colocar:

```python
from .models import *
...
class ProductoImageInline(admin.TabularInline):
    model=ProductoImage


class ProductoAdmin(admin.ModelAdmin):
    inlines = [
        ProductoImageInline,
    ]
...
# admin.site.register(Producto) <-- Borra o comenta esta linea
admin.site.register(Producto, ProductoAdmin)
...
```

Finalmente para que nuestra aplicación pueda acceder al folder donde se almacenarán las imágenes debemos configurar las variables `MEDIA_ROOT` y `MEDIA_URL` en los archivos `setttings.py` y `urls.py`:

En `settings.py`:

```python
...
import os

MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
MEDIA_URL = '/media/'
```

En `urls.py`:

```python
...
from django.conf import settings
from django.conf.urls.static import static

urlpatterns = [
    ...
] + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

> **Nota:** Es recomendable agregar la carpeta "media" en su archivo ".gitignore" para no subir imágenes al repositorio de GitHub.

Subamos imágenes de prueba mediante el admin, preferiblemente cuadradas, para probar nuestra implementación.

#### Ahora toca el front-end

En nuestro template para el detalle de producto vamos a mostrar todas las imágenes del producto. En el archivo `producto_detail.html` debemos colocar el siguiente código debajo del título:

```HTML
...
<div class="columns">
    {% for image in object.images.all %}
    <div class="column is-3">
        <figure class="image is-square">
            <a href="{{ image.image.url }}">
                <img src="{{ image.image.url }}" alt="No hay imagen disponible">
            </a>
        </figure>
    </div>
    {% empty %}
    <div class="column content">
        <p>Aún no hay imágenes disponibles</p>
    </div>
    {% endfor %}
</div>
...
```

Ahora aprovechando que tenemos imágenes para cada producto cambiemos nuestras simples listas por unas tarjetas con imágenes para mostrar los productos disponibles en la tienda.
Para esto debemos ir al archivo `producto_list.html` y reemplazar el código que se encuentra debajo del título y antes de la lista de links por lo siguiente:

```html
<div class="columns is-multiline">
    {% for producto in object_list %}
        <div class="column is-4">
            <div class="card">
                <div class="card-image">
                    <figure class="image">
                        <img src="{{ producto.images.first.image.url|default:'https://via.placeholder.com/128' }}" alt="Imagen no disponible">
                    </figure>
                </div>
                <div class="card-content">
                    <h6>
                        <a href="{% url 'product-detail' producto.pk %}">
                            {{ producto.nombre }}
                        </a>
                    </h6>
                    <p>$ {{ producto.precio }}</p>
                </div>
            </div>
        </div>
    {% empty %}
        <div class="content">
            <h6>Aun no hay productos disponibles.</h6>
        </div>
    {% endfor %}
</div>
```

Gracias al framework de CSS, podemos utilizar un sistema de columnas (`columns`) y tarjetas (`cards`) para motrar nuestros productos de una manera más amigable con el usuario. Asimismo, usando el templatetag `|default:` hemos podido indicar que si el producto no tiene ninguna imagen relacionada coloque una imagen *placeholder* desde un servidor externo, para evitar que las tarjetas se desencajen.

{% endraw %}

[< Anterior lab](lab4.md) 