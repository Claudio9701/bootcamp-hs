[< Volver al inicio](README.md)

# Sesión 2: Introduccion a Python 1
**Fecha:** Martes 08/11/2022 | 22/11/2022  
**Duracion:** 3 horas  
**Profesor(es):** Paolo Bejarano  

El objetivo del presente laboratorio es validar la comprension de la parte 1 de Python mediante la utilizacion de
conceptos mas abstractos como las funciones y clases.

## Funciones
Nos permiten encapsular una funcionalidad para mejorar el performance de nuestro código y podamos reutilizarlas solo declarándolas. Estas funciones se declaran usando `def` al inicio.

```python
#esta función tiene el parámetro "x" y evalúa si "x" es positivo, negativo o cero
def sign(x):
    if x > 0:
        return 'positivo'
    elif x < 0:
        return 'negativo'
    else:
        return 'cero'
```
Luego para llamar la función que acabamos de crear, lo podemos hacer de diversas maneras:

```python
#solo imprimir lo que nos da la función
print(sign(-1))

#también lo podemos agregar a una variables (castear)
validador_signo = sign(-1)
print(validador_signo)
```

## Clases
Una clase corresponde a un objeto en progamación. Este concepto nos servirá mucho en el desarrollo del trabajo final (ecommerce).
Primero utilizaremos un ejemplo para entender la logica a través de una clase Carro que tiene un **Constructor, Atributos y Métodos**.

Un carro tiene **Atributos** como el año de edición, los kilometros recorridos y el color. Por lo tanto nustra clase Carro tambien lo tendrá.
```python
def __init__(self, col, año, km):
    self.color = col
    self.año = año
    self.kilómetros = km
```

Adicionalmente un carro puede realizar acciones, como avanzar y frenar. Por lo que nuestra clase Carro tambien podrá hacerlo a través de los **Métodos**
```python
def avanzar(self):
       return "Rum Rum... Carro avanzando." 

def frenar(self):
       return "uuu... El carro ha frenado."
```

Seguro te preguntaras donde quedo el **Constructor**. Por el momento no hay problema, puesto que ya lo hemos utilizado. Es una función que utiliza las propiedades de las clases para crear objetos. Además, puede incluir variables que permitirán personalizarlo. Puedes verlo como una fabrica de autos que puede componer diferentes modelos.
```python
class Carro:
    # __init__ declara las variables como parte del nuevo objeto.
   def __init__(self, col, año, km):
       self.color = col
       self.año = año
       self.kilómetros = km
```
Finalmente todo junto se veria así:
```python
class Carro:
   def __init__(self, col, año, km):
       self.color = col
       self.año = año
       self.kilómetros = km

   def avanzar(self):
       return "Rum Rum... Carro avanzando."
   def retroceder(self):
       return "uuu... El carro ha frenado."
```
Ahora que tenemos todo listo ya podemos fabricar nuestro propio auto.
```python
#Fabricamos un nuevo auto rojo del 2016 que ha recorrido 0.0 kilometros
MiCarro = Carro("rojo",2016,0.0);
#Hacemos que el auto nuevo avance
print(MiCarro.avanzar());
```

Acá podemos ver otro ejemplo.
```python
#declaramos la clase
class Greeter:
    # Constructor
    def __init__(self, name):
        self.name = name  # Create an instance variable
    # Métodos
    def greet(self, loud):
        if loud==1:
            print('HELLO, %s!' % self.name.upper())
        else:
            print('Hello, %s' % self.name)

```
Reutilizamos nuestra clase Greeter:
```python
g = Greeter('Fred')  # Gracias al constructor podemos crear la clase 'Freed'
g.greet(loud=1) # Llamamos al método usando 1. Ingresa al if
g.greet(loud=0) # Llamamos al método usando 0. Ingresa al else
```

## Retos: Ahora te toca a ti

1. Crea un programa que te permita interactuar con objetos mediante clases y metodos (funciones):
  - Un ejemplo seria pensar en un programa que te permita seleccionar un tipo de pokemon y luego tener batallas con otros.
  - La interfaz o medio de interaccion sera la linea de comandos. En otras palabras solo necesitaras usar las funciones `print` e `input`.
  - 3 alumnos seran seleccionados al azar la siguiente clase para que presenten su codigo y demo.

[< Anterior lab](session_1.md)
