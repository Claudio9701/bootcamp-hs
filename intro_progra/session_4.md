# Sesión 4: Manejo de Librerias

**Fecha:** Jueves 10/11/2022 | 24/11/2022
**Duracion:** 3 horas  
**Profesor(es):** Paolo Bejarano  

El objetivo del presente laboratorio es ir más allá de la sintaxis basica de Python (sigan practicando!), ahora entraremos a ver librerias que vienen preinstaladas y externas. Estas son basicamente grupos de funciones para realizar una tarea especifica de manera sencilla.

> **No reinventemos la rueda:** Veamos si existe una libreria que podamos usar directamente o como inspiracion para hacer lo que queremos.

## ¡Viene *gratis*! Librerías Estándar de Python

Estas librerias no son necesarias de instalar por su cuenta, vienen por defecto con la instalacion de Python.

### Tiempo, horas y fechas: un dolor de cabeza

#### Libreria [time](https://docs.python.org/3/library/time.html)

```python
# Dependencies
import time
```

Una forma muy directa y sencilla de medir el tiempo de ejecucion de alguna parte de nuestro codigo es utilizar la funcion `time` al incio y al final; y por ultimo calculamos la diferencia entre los dos. Veamos:

```python
start = time.time()

long_data = [i for i in range(100000)]
long_data_squared = map(lambda x: x**2, long_data)

end = time.time()
print("Start time:", start)
print("End time:", end)
print("Time elapsed:", end - start)
```

- ¿Qué significa el resultado de la función? Los segundos, como numero decimal, desde *epoch*. Veamos quien encuentra primero a que se refiere este tiempo.
- ¿Como podemos hacer que el resultado de la diferencia sea mas sencillo de entender? Podemos dividir entre 60 y colocarlo en dias.

Tambien podemos usar la funcion `sleep` para detener nuestro codigo por un determinado numero de segundos:

```python
start = time.time()

# Simulate a code that takes 3 seconds to run
time.sleep(3)

end = time.time()
print("Start time:", start)
print("End time:", end)
print("Time elapsed:", end - start)
```

Puedes explorar mas funcionalidades y detalles de este modulo en la documentacion oficial de Python. Asimismo, esta no es la mejor forma para sistematizar el calculo de tiempos de ejecucion e identificar posibles cuello de botella. Para ello, podemos utilizar una herramienta de [*profiling*](https://docs.python.org/3/library/profile.html).

#### Libreria [datetime](https://docs.python.org/3/library/datetime.html)

Este es una de mis librerias favoritas, parece magia! Con datetime tenemos diversas funciones con las que podemos generar y transformar fechas como un tipo de dato especifico, con reglas e incluso operaciones aritmeticas propias:

El tipo de dato que nos permite trabajar con los diferentes formatos de fecha y hora se llama igual que la libreria: datetime. Para evitar conflictos podmeos realizar la importacion del modulo y su clase principal asi:

```python
import datetime as dt
from datetime import datetime
```

Veamos como funciona:

```python
start_of_class = datetime(year=2022, month=11, day=10, hour=9, minute=30, second=0, microsecond=0)
print("Class started:", start_of_class)
```

¿Que paso aqui? Al mostrar la variable `start_of_class` que es de tipo `datetime.datetime` automaticamente detecto que estaba siendo usada dentro de `print` y se muestra en un formato comprensible por el humano: **[año]-[mes]-[dia] [horas]-[minutos]-[segundos]**. Podemos cambiar este formato a uno adecuado a nuestra region. Por ejemplo, en Peru usamos el formato de fecha **[dia]-[mes]-[año]**:

```python
# string (str) formatted (f) time
date_string = start_of_class.strftime("%d/%m/%y %H:%M:%S")
print("Tipo de dato:", type(date_string))
print("Fecha con nuevo formato:", date_string)
```

Este modulo tambien nos permite generar un `datetime` a partir de una cadena de caracteres (`str`) si le indicamos el formato en el que se encuentra la fecha:

```python
processed_datestring = datetime.strptime("27-01-1997, 06:00", "%d-%m-%Y, %I:%M")
print("Tipo:", type(processed_datestring))
print("Fecha procesada:", processed_datestring)
```

¿Se puede calcular el intervalo de tiempo entre fechas? Si, pero cuando hacemos operaciones se genera un nuevo tipo de dato `timedelta`:

```python
interval = start_of_class - processed_datestring
print("Tipo:", type(interval))
print("Intervalo de tiempo:", interval)
```

¿Es posible saber la edad de una persona solo con la variable `interval`? ¿Por que? ¿Como lo podemos solucionar?.

### Interactuamos con nuestro sistema de archivos

#### Libreria [os](https://docs.python.org/3/library/os.html)

Esta es la libreria tradicional para interactuar con el sistema de archivos, actualmente Python recomienda utilizar la nueva [`pathlib`](https://docs.python.org/3/library/pathlib.html#correspondence-to-tools-in-the-os-module). Sin embargo, es bueno revisar las funciones principales de esta libreria `os` porque ha sido muy utilizada y probablemente te encuentres con ella en mucho codigo disponible online.

Para obtener el directorio actual, desde el cual se esta ejecutando el script:

```python
current_working_dir = os.getcwd()
print(current_working_dir)
```

Par ver los contenidos de las carpetas:

```python
cwd_content = os.listdir()
print(cwd_content)
```

Para crear nuevas carpetas:

```python
new_folder_dir = os.path.join(current_working_dir, "new_folder")
os.mkdir(new_folder_dir)
print(os.listdir())
```

Para cambiar nombres de archivos:

```python
new_folder_name = os.path.join(current_working_dir, "new_folder_renamed")
os.rename(new_folder_dir, new_folder_name)
print(os.listdir())
```

Para eliminar carpetas:

```python
os.rmdir(new_folder_name)
print(os.listdir())
```

> [Notebook interactivo con el codigo realizado hasta aqui.](nbs/session_4.ipynb)

---

### Otras librerias utiles

- [math](https://docs.python.org/3/library/math.html): Funciones para operaciones matematicas mas complejas.
- [pickle](https://docs.python.org/3/library/pickle.html): Libreria para guardar elementos arbitrarios de Python. 

### Manos a la obra

Ejercicio 1: Crear/Leer un archivo txt y csv/tsv

> Hint: Usa las funciones `open` y `close` para manipular archivos.

## Subamos a los hombros de gigantes: Librerias Externas

### Recomendaciones

- Tener su ambiente de desarrollo local configurado (Python, Ambiente virtual, Editor de codigo y/o Jupyter Notebooks)
- Tener instaladas las librerias basicas como pandas, numpy y matplotlib.

```sh
(.env) $ pip install pandas numpy matplotlib
```

> Si no tienes puedes seguir la clase desde un servicio de notebooks en linea como [Google Colab](https://colab.research.google.com/)

### Notebook de ejemplo

- [Introduccion a numpy & pandas](nbs/intro_numpy_pandas.ipynb)*
- **Descarga el archivo de datos de calidad de aire aqui: [airquality.csv](nbs/data/airquality.csv)**

## Retos: Ahora te toca a ti

- Leer un archivo de datos, modificarlo, guardarlo con pandas.
- Generar graficos sobre ese archivo de datos y guardarlos en imagenes .png o .jpg usando matplotlib.

[<< Inicio](README.md)  |  [< Anterior sesion](session_3.md)
