# Sesión 2: Introduccion a Python 1
**Fecha:** Martes 08/11/2022 | 22/11/2022  
**Duracion:** 3 horas  
**Profesor(es):** Paolo Bejarano  

El objetivo del presente laboratorio es repasar de sobre los conceptos básicos de Python, para que el estudiante pueda recordar el manejo de este lenguaje y así, tener el mejor desempeño en el curso.

Se explicarán los siguientes conceptos:

1. Introducción.
2. Tipo de datos.
3. Contenedores.
4. Slicing.
5. Loops.
6. List comprehesion.

## 1. Introducción

Python es un lenguaje de programación interpretado, que con ayuda de algunos [paquetes](https://packaging.python.org/tutorials/installing-packages/#installing-from-pypi) se torna en un ambiente poderoso para desarollar casi cualquier pieza de softwate.

Para valirdar que se tiene Python instalado, verificamos la version utilizando el siguiente comando desde el programa terminal / command prompt / powershell dependiendo de que sistema operativo uses:

```sh
python3
```

Si todo esta bien deberías ver la versión de Python en la primera línea (debería ser 3.7 o 3.8), de lo contrario dirígite a realizar la configuración correcta desde este [link](../session_1/README.md).

_Pro Tip: No copies los códigos que te brindamos aquí: prueba, equivócate y vuelve a intentarlo tú mismo; es la única forma de aprender Python._

## 2. Tipo de datos

Existen diferentes tipos de datos que, para nosotros pueden significar los mismo, pero para Python se deben tratar de manera distinta. Aquí la lista:

### 2.1. Números

- Existen 2 tipos principales:
    - int: los que conocemos como enteros. Ejemplo: 234
    - float: los que conocemos como decimales. Ejemplo: 2.4

Para comprobar el tipo de dato, se puede correr el siguiente script:

```python
x = 3
print( x , type(x) )
```
También se pueden realizar las siguientes operaciones:
```python
print( x+1 ) # Suma
print( x-1 ) # Diferencia
print( x*2 )  # Multiplicación
print( x**2 ) # Potencia
print( x/2 ) # División
print( x//2 ) # División: denominador 
print( x%2 ) # División: residuo
```
Otro ejemplo con `float`:
```python
y = 2.5
print( type(y) )
print( y, y + 1, y * 2, y ** 2 ) 
```

### 2.2. Booleanos
Corresponde a los datos `True` y `False`. Por ejemplo:
```python
t, f = True, False
print( type(t) )
```
Con estos datos podemos hacer distintas operaciones:

```python
print( t and f ) # y
print( t or f ) # o
print( not t ) # negación
print( t != f ) # no es igual a
print( False == False ) # es igual a
```

### 2.3. Strings
Corresponde a las cadenas (texto). Siempre debe ir entre comillas (puede ser "" o ''). Por ejemplo, si queremos declarar un string:

```python
hello = 'hello'
world = "world"''
print (hello , len(hello))
```

Para concatenar strings:

```python
hw = hello + " " + world
print(hw)
```

Y si desean concatenar con otro tipo de datos, puede usar el siguiente artificio:
```python
hw = "hello" + " " + "world" +" " +str(12)
print(hw)
```
O también de esta forma, usando `%`:
```python
hw12 = '%s %s %d' % (hello, world, 12)
print(hw12)
```
Existen algunos métodos muy comunes para `string`:
```python
s = 'hello'
# Capitaliza un string =>  "Hello"
print(s.title())
# Convierte un string a mayúscula => "HELLO"
print(s.upper())
# Sustituye todas las instancias de uno con otro => "he(ell)(ell)o"
print(s.replace('l', '(ell)'))
# Borra los espacios en blanco de los extremos
print('  world '.strip())
```

## 3. Contenedores
Python otros tipos de datos que se consideran como contenedores, porque pueden guardar otros datos dentro de sí. Por ejemplo: Listas, diccionarios y tuplas.

### 3.1. Listas
También conocidas como `arrays`. Pueden contener **cualquier** tipo de dato en el mismo; esta caracterísca no lo tiene todos los lenguajes.

Declaremos una lista:
```python
lista1 = [3, 1, 2]
print(lista1)
```
Para obtener datos de un lugar de la lista podemos usar entre corchetes la posición:

_PRO TIP: Python inicia en la posición `0` (cero)._
```python
print(lista1[0]) #la primera posición
print(lista1[2]) #la última posición
```
O también podemos acceder con negativos:

```python
print(lista1[-1]) #ultima posición
```
Para agregar un nuevo elemento, podemos usar cualquiera de las dos maneras:
```python
#Forma 1: usando el lugar donde queremos incluir el elemento y el elemento. En este caso se está reemplazando el lugar `2`.
lista1[2] = 'deep'
print(lista1[-1])
#Forma 2: usando append. Tomar en cuenta que append agregará en el último espacio.
lista1.append('learning')
print(lista1)
```
Para más métodos para el uso de listas, puedes revisar [la siguiente documentación.](https://docs.python.org/2/tutorial/datastructures.html#more-on-lists)

### 3.2. Diccionarios
Tienen una complejidad mayor que las listas porque en este caso tendrá la siguiente forma: `diccionario = {"llave": valores}`

```python
d = {'cat':'cute' , 'dog':'furry'}
print(d['cat'])
```
Para agregar elementos al diccionario:

```python
d['fish'] = 'wet'
print(d) 
```

### 3.3. Tuplas
Una tupla es una lista ordenada (inmutable) de valores. Una tupla es similar a una lista; una de las diferencias más importantes es que las tuplas se pueden usar como claves en diccionarios y como elementos de conjuntos, mientras que las listas no. Por ejemplo:

```python
t = (5, 6)       # Crear una tupla
print( type(t) )
print(t[0])
```

## 4. Slicing
`range` Permite acceder a las listas en un lugar específico. Ademas, sigue la siguiente estructura: [cerrado, abierto]. `list` te permitira poder imprimir el resultado de `range`.

```python
#creamos la lista y la mostramos
nums = list(range(5))
print("1)", nums)
#podemos mostrar un bloque de datos
print("2)", nums[2:4])
#Todos desde la posicion 2
print("3)", nums[2:])
#Todos hasta la posicion 2
print("4)", nums[:2])
#Todos
print("5)", nums[:])
#Todos hasta el penúltimo
print("6)", nums[:-1])
#Reemplaza las posiciones 2,3 (porque 4 es abierto)
nums[2:4] = [8, 9]
print("7)", nums)
```
```sh
1) [0 , 1, 2, 3, 4]
2) [2, 3]
3) [2, 3, 4]
4) [0 , 1]
5) [0 , 1, 2, 3, 4]
6) [0 , 1, 2, 3]
7) [0 , 1, 8, 9, 4]
```

## 5. Loops
Los bucles te permiten ejecutar código una y otra vez dependiendo de ciertas condiciones. Tambien permiten iterar dentro de listas, diccionarios, entre otros.
Para el uso de bucles aprenderemos los siguientes términos:

- For in range
- For in list
- while


## 5.1. For in range
Sirve para ejecutar un código una cantidad determinada de veces. Junto con una variable “i” que cambia cada iteración.

```python
for i in range(3):
   print("Hola amigo #",i)
```
Esto devolvera la siguiente lista de saludos.
```sh
Hola amigo # 0
Hola amigo # 1
Hola amigo # 2
```

## 5.2. For in list
Para iterar de la siguiente forma: `for` iterador `in` lista_animales --> imprime sus elementos. El iterador empieza desde la posición cero.

```python
animals = ['cat', 'dog', 'monkey']
for animal in animals:
    print(animal)
```
Esto devolvera la lista de animales
```sh
cat
dog
monkey
```

## 5.3. While
Sirve para ejecutar un código una cantidad indeterminada de veces. Seguirá ejecutándose hasta que la condición sea falsa.

```python
x = 0
while (x<3):
 print("Adios amigo #",x)
 x=x+1
```
Esto devolvera la siguiente lista de despedidas
```sh
Adios amigo # 0
Adios amigo # 1
Adios amigo # 2
```

## Reto: Ahora te toca a ti

1. Deberas replicar esta guia en un python notebook desde tu computadora personal.
2. Implementa un programa de python (script) que permita realizar operaciones matematicas con numeros enteros indicados por el usuario (Ve la funcion `input`). Por ejemplo:

```bash
$ python mate.py
>>> Ingresa el primer numero: 5
>>> Ingresa el segundo numero: 6
>>> Ingresa la operacion que deseas realizar (e.g. +, *, -, /, ^): *
>>> Operacion a realizar:  5 * 6
>>> Resultado: 30
```

3. Implementa un programa de python (script) que dada una lista de numeros enteros y un numero entero te indique la posicion de dicho numero en la lista

```bash
$ python rank.py
>>> Ingresa una lista de numeros (Coloca q para terminar): 
>>> 9
>>> 36
>>> 3
>>> 25
>>> 2
>>> q
>>> Ingresa un numero: 25
>>> Posicion: 4
```

> [Hands-on Python!](nbs/summary_session_1_2.ipynb): Notebook con el resumen de las sesiones 1 y 2.

[<< Inicio](README.md)  |  [< Anterior sesion](session_1.md)  |  [Siguiente lab >](session_3.md)
