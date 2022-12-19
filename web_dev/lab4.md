{% raw %}
# Lab 4: Carrito de compras y Pagos

### Introducción
En este lab vamos a aprender como implementar un carrito de compras y recibir pagos a traves de PayPal!. Con esto nuestros usuarios podran añadir y eliminar productos de su carrito, colocar sus datos de envío y realizar el pago de su pedido. También vamos a usar un framework de CSS para que nuestra página se vea más moderna.

### Modelos de datos para pedidos

Si revisamos nuestro diagrama de clases ([lab 1](lab1.md)), veremos que solo nos faltan implementar las clases Pedido y Detalle de Pedido. Vamos al archivo `main/models.py` y colocamos:

```python
class Pedido(models.Model):
    # Relaciones
    cliente = models.ForeignKey('Cliente', on_delete=models.CASCADE, null=True)
    repartidor = models.ForeignKey('Colaborador', on_delete=models.SET_NULL, null=True)
    ubicacion = models.ForeignKey('Localizacion', on_delete=models.SET_NULL, null=True)

    # Atributos
    fecha_creacion = models.DateTimeField(auto_now=True)
    fecha_entrega = models.DateTimeField(blank=True, null=True)
    estado = models.CharField(max_length=3)
    direccion_entrega = models.CharField(max_length=100, blank=True, null=True)
    tarifa = models.FloatField(blank=True, null=True)

    def __str__(self):
        return f'{self.cliente} - {self.fecha_creacion} - {self.estado}'

    def get_total(self):
        detalles = self.detallepedido_set.all()
        total = 0
        for detalle in detalles:
            total += detalle.get_subtotal()
        total += self.tarifa
        return total

class DetallePedido(models.Model):
    # Relaciones
    producto = models.ForeignKey('Producto', on_delete=models.CASCADE)
    pedido = models.ForeignKey('Pedido', on_delete=models.CASCADE, null=True)

    # Atributos
    cantidad = models.IntegerField(blank=True, null=True)

    def __str__(self):
        return f'{self.pedido.id} - {self.cantidad} x {self.producto.nombre}'

    def get_subtotal(self):
        return self.producto.precio_final() * self.cantidad
```

> **Cambio en el modelo `Producto`:** El nombre del metodo `precio_final` ahora es `get_precio_final`, simplemente para darme cuenta que es un metodo y no un atributo :smile:

Ahora usaremos los dos comandos para efectuar los cambios en la base de datos. Desde el terminal ejecuta:

```sh
python manage.py makemigrations
python manage.py migrate
```

## ¿Cómo es el proceso de compra?

Para este ejemplo utilizaremos 5 pasos posibles en el proceso de compra:

- Añadir/Eliminar productos del carrito
- Ver el carrito
- Llenar datos de envío
- Pagar el pedido
- Confirmación del pago

### Anadir/Elminar productos del Carrito

Vamos a crear dos vistas: una para anadir 1 unidad del producto seleccionado y otra para remover 1 unidad del producto seleccionado. Vamos a al archivo `main/views.py` y colocamos:

```python
from django.shortcuts import ..., redirect
from django.views.generic import ..., View, UpdateView
from django.db.models import F
from random import randint
from django.contrib import messages

...
class AddToCartView(View):
    def get(self, request, product_pk):
        # Obten el cliente
        user_profile = Profile.objects.get(user=request.user)
        cliente = Cliente.objects.get(user_profile=user_profile)
        # Obtén el producto que queremos añadir al carrito
        producto = Producto.objects.get(pk=product_pk)
        # Obtén/Crea un/el pedido en proceso (EP) del usuario
        pedido, _  = Pedido.objects.get_or_create(cliente=cliente, estado='EP')
        # Obtén/Crea un/el detalle de pedido
        detalle_pedido, created = DetallePedido.objects.get_or_create(
            producto=producto,
            pedido=pedido,
        )

        # Si el detalle de pedido es creado la cantidad es 1
        # Si no sumamos 1 a la cantidad actual
        if created:
            detalle_pedido.cantidad = 1
        else:
            detalle_pedido.cantidad = F('cantidad') + 1
        # Guardamos los cambios
        detalle_pedido.save()
        # Recarga la página
        return redirect(request.META['HTTP_REFERER'])

class RemoveFromCartView(View):
    def get(self, request, product_pk):
        # Obten el cliente
        user_profile = Profile.objects.get(user=request.user)
        cliente = Cliente.objects.get(user_profile=user_profile)
        # Obtén el producto que queremos añadir al carrito
        producto = Producto.objects.get(pk=product_pk)
        # Obtén/Crea un/el pedido en proceso (EP) del usuario
        pedido, _  = Pedido.objects.get_or_create(cliente=cliente, estado='EP')
        # Obtén/Crea un/el detalle de pedido
        detalle_pedido = DetallePedido.objects.get(
            producto=producto,
            pedido=pedido,
        )
        # Si la cantidad actual menos 1 es 0 elmina el producto del carrito
        # Si no restamos 1 a la cantidad actual
        if detalle_pedido.cantidad - 1 == 0:
            detalle_pedido.delete()
        else:
            detalle_pedido.cantidad = F('cantidad') - 1
            # Guardamos los cambios
            detalle_pedido.save()
        # Recarga la página
        return redirect(request.META['HTTP_REFERER'])
```

### Ver el carrito

Para ver el carrito utilizaremos la clase `DetailView` y agregaremos los detalles de pedido relacionado al pedido seleccionado en el contexto:


```python
...
class PedidoDetailView(DetailView):
    model = Pedido

    def get_object(self):
        # Obten el cliente
        user_profile = Profile.objects.get(user=self.request.user)
        cliente = Cliente.objects.get(user_profile=user_profile)
        # Obtén/Crea un/el pedido en proceso (EP) del usuario
        pedido  = Pedido.objects.get(cliente=cliente, estado='EP')
        return pedido

    def get_context_data(self, **kwargs):
        context = super().get_context_data(**kwargs)
        context['detalles'] = context['object'].detallepedido_set.all()
        return context
```

### Llenar datos de envío

Para esto utilizaremos una clase nueva `UpdateView`, esta clase permite que le indiquemos qué modelo y cuáles de sus atributos queremos actualizar/modificar:

```python
class PedidoUpdateView(UpdateView):
    model = Pedido
    fields = ['ubicacion', 'direccion_entrega']
    success_url = reverse_lazy('payment')

    def form_valid(self, form):
        # This method is called when valid form data has been POSTed.
        # It should return an HttpResponse.
        self.object = form.save(commit=False)
        # Calculo de tarifa
        self.object.tarifa = randint(5, 20)
        return super().form_valid(form)
```

### Pagar el pedido

Mas adelante utilizaremos un html de Paypal, por ello nuestra vista usara la clase `TemplateView`:

```python
...
class PaymentView(TemplateView):
    template_name = "main/payment.html"

    def get_context_data(self, **kwargs):
        context = super().get_context_data(**kwargs)
        # Obten el cliente
        user_profile = Profile.objects.get(user=self.request.user)
        cliente = Cliente.objects.get(user_profile=user_profile)
        context['pedido'] = Pedido.objects.get(cliente=cliente, estado='EP')

        return context
```

### Confirmación del pago

Una vez recibida la confirmacion de pago debemos actualizar el estado del pedido de EP (En Proceso) a PAG (Pagado), y asignarle un repartidor. Tambien enviaremos un mensaje a nuestro usuario indicando que el pago esta conforme:

```python
class CompletePaymentView(View):
    def get(self, request):
        # Obten el cliente
        user_profile = Profile.objects.get(user=request.user)
        cliente = Cliente.objects.get(user_profile=user_profile)
        # Obtén/Crea un/el pedido en proceso (EP) del usuario
        pedido = Pedido.objects.get(cliente=cliente, estado='EP')
        # Cambia el estado del pedido
        pedido.estado = 'PAG'
        # Asignacion de repartidor
        pedido.repartidor = Colaborador.objects.order_by('?').first()
        # Guardamos los cambios
        pedido.save()
        messages.success(request, 'Gracias por tu compra! Un repartidor ha sido asignado a tu pedido.')
        return redirect('home')
```

### Relacionamos las vistas con una url

En el archivo `main/urls.py` colocaremos:

```python
...
urlpatterns = [
    ...
    path('add_to_cart/<int:product_pk>', views.AddToCartView.as_view(), name='add-to-cart'),
    path('remove_from_cart/<int:product_pk>', views.RemoveFromCartView.as_view(), name='remove-from-cart'),
    path('carrito/', views.PedidoDetailView.as_view(), name='pedido-detail'),
    path('checkout/<int:pk>', views.PedidoUpdateView.as_view(), name='pedido-update'),
    path('payment/', views.PaymentView.as_view(), name='payment'),
    path('complete_payment/', views.CompletePaymentView.as_view(), name='complete-payment'),
]
```

Hemos terminado con el codigo de python por esta sesion, ahora vamos a utilizar HTML, para tener una pagina que se vea un poco mas bonita y moderna, utilizaremos el framework de CSS [Bluma](https://bulma.io). En nuestro archivo `base.html` colocaremos:

```html
...
  <head>
    ...
    <meta name="viewport" content="width=device-width, initial-scale=1"> <!-- Ensures optimal rendering on mobile devices. -->
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bulma@0.9.1/css/bulma.min.css">
  </head>
  <body>
  <section class="section">
    <div class="container">
      <h1 class="title is-1"> Linio Express </h1>
      <hr>
      {% block content %}
      {% endblock %}
    </div>
  </section>
  </body>
...
```

Ahora en `producto_detail.html` vamos a agregar dos botones debajo del detallo del producto. Uno para añadir el producto al carrito y otro para ver el carrito:


```html
...
<div class="block">
    <a href="{% url 'add-to-cart' product_pk=object.pk %}" class='button is-primary'>
      Añadir al carrito
    </a>
    <a href="{% url 'pedido-detail' %}" class='button'>
      Ver carrito
    </a>
</div>
...
```

Luego vamos a crear el archivo `pedido_detail.html` y colocamos:

```html
{% extends "base.html" %}

{% block content %}
    <h3 class="title"> Carrito </h3>
    <div class="block">
        <h5 class="subtitle"> Información General </h5>
        <div class="content">
            <ul>
              <li> <strong>Cliente:</strong> {{ object.cliente.user_profile.user.get_username }}</li>
              <li> <strong>Fecha Creación:</strong> {{ object.fecha_creacion }}</li>
              <li> <strong>Estado:</strong> {{ object.estado }}</li>
            </ul>
        </div>
    </div>
    <div class="block">
        <h5 class="subtitle"> Detalle </h5>
        <div class="content">
            <ul>
              {% for detalle in detalles %}
                <li>
                  {{ detalle }}
                  <a href="{% url 'add-to-cart' product_pk=detalle.producto.pk %}" class="button is-success">
                    +
                  </a>
                  <a href="{% url 'remove-from-cart' product_pk=detalle.producto.pk %}" class="button is-danger">
                    -
                  </a>
                </li>
              {% endfor %}
            </ul>
        </div>
    </div>
    <a href="{% url 'pedido-update' pk=object.pk %}" class='button is-info'>
      Checkout
    </a>
    <hr>
    <div class="content">
        <ul>
          <li><a href="{% url 'product-list' %}">Ver Lista de Productos</a></li>
          <li><a href="{% url 'home' %}">Inicio</a></li>
        </ul>
    </div>
{% endblock %}
```

Para que los clientes puedan enviar su informacion de ubicacion y direccion de entrega, creemos el archivo `pedido_form.html` y colocamos:

```html
{% extends "base.html" %}

{% block content %}
    <h3 class="title"> Checkout </h3>
    <div class="block">
        <h5 class="subtitle"> Completa tus datos </h5>
        <form method="post">
          {% csrf_token %}
          {{ form.as_p }}
          <input type="submit" value="Submit" class="button is-info">
        </form>
    </div>
    <hr>
    <div class="content">
        <ul>
          <li><a href="{% url 'product-list' %}">Ver Lista de Productos</a></li>
          <li><a href="{% url 'home' %}">Inicio</a></li>
        </ul>
    </div>
{% endblock %}
```

Ahora debemos [registrarnos en Paypal](https://www.paypal.com/pe/welcome/signup). Una vez hayamos iniciado sesion, vamos al [Dashboard de desarrolladores](https://developer.paypal.com/developer/applications/) ahi debemos hacer click en **Default Application** y copiamos el Client ID en un blog de notas para tenerlo a la mano. Asimismo vamos a la opcion [Sandbox Accounts](https://developer.paypal.com/developer/accounts/), hacemos click en los "..." al costado cualquier usuario, seleccionamos la opcion "View/Edit account" y copiamos el correo y la contraseña en el mismo blog de notas.

Ahora vamos a copiar el siguiente codigo en un archivo llamado `payment.html`:

```html
{% extends "base.html" %}

{% block content %}
    <script src="https://www.paypal.com/sdk/js?client-id=YOUR_SB_CLIENT_ID"> // Replace YOUR_SB_CLIENT_ID with your sandbox client ID
    </script>

    <h3 class="title">Precio total del pedido: {{ pedido.get_total }}</h3>

    <div class="block" id="paypal-button-container"></div>

    <!--Esto se agrego para testear que pasaria si se completa el pago-->
    <a href="{% url 'complete-payment' %}">Completar pago de prueba</a>

    <!-- Add the checkout buttons, set up the order and approve the order -->
    <script>
      var total = {{ pedido.get_total }}
      var complete_payment_url = {% url 'complete-payment' %}

      paypal.Buttons({
        createOrder: function(data, actions) {
          return actions.order.create({
            purchase_units: [{
              amount: {
                value: `${total}`
              }
            }]
          });
        },
        onApprove: function(data, actions) {
          return actions.order.capture().then(function(details) {
            window.location.href = `${complete_payment_url}`;
          });
        }
      }).render('#paypal-button-container'); // Display payment options on your web page
    </script>
{% endblock %}
```

Para mayor informacion sobre el funcionamiento de los botones de PayPal pueden revisar la [documentacion](https://developer.paypal.com/docs/business/checkout/set-up-standard-payments/#).

Finalmente, en el archivo `home.html`, arriba del titulo de "Ultimos productos", coloca este codigo para visualizar el mensaje de comprobacion de pago:


```html
...
{% if messages %}
    {% for message in messages %}
    <div class="notification {% if message.tags %}is-{{ message.tags }}{% endif %}">
        <button class="delete"></button>
        {{ message }}
    </div>
    {% endfor %}
{% endif %}
...
```

### Probemos las nuevas funciones!

## Siguiente Lab

- Aprenderemos a buscar productos, añadir imagenes a los productos.

{% endraw %}


[< Anterior lab](lab3.md)  
[Siguiente lab >](lab5.md)
