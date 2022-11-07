# Sesión 1: Configuración inicial

**Fecha:** Lunes 07/11/2022 | 21/11/2022  
**Duración:** 3 horas  
**Profesor(es):** Paolo Bejarano y Claudio Ortega

> Recordemos el curso de pre-selección: [Introducción a la programación](https://docs.google.com/presentation/d/e/2PACX-1vS_UtPYURotAm5zGIYEBCSnHmYunLXGLN7Jgdc-zGop3z-0u6ehjMkAm9Ugm_5fDWdNoijsSV-zra0_/pub?start=false&loop=false&delayms=3000)

## 1. Instalamos Python (bien)

Si ya tienes Python instalado en tu computadora, sabes como lo instalaste y lo utilizas sin ningun problema, puedes omitir esta sección.

Si aún no, te recomendamos que siguas los pasos en la parte 1 y 2 de esta [guía](https://www.wikihow.com/Start-Programming-in-Python) para evitar confusiones y conflictos con otros métodos de instalación más pesados, avanzados y/o específicos como Anaconda. Si ya lo tienes instalado o lo piensas instalar luego, no hay problema, seguir estos pasos te ayudará a comprender mejor que está pasando "por detrás".

### Comprueba que hayas instalado Python correctamente

Abre el progama PowerShell o Command Prompt en Windows o Terminal si estas en un sistma operativo basado en Unix (Linux o Mac OS), y ejecuta el siguiente comando:

```bash
$ python --version
Python 2.7.x
```

Como pudiste ver en la guía de instalación de Python la versión actual es la 3.11. Sin embargo, es muy probable que al ejecutar el comando anterior hayas visto otra versión.

En los sistemas basados en Unix, Python 2.7 viene preinstalado ya que es una dependencia del sistema. Por defecto el comando `python` hace referencia a esta versión.

Pero ... ¿Qué pasó con la versión 3.11 que acabamos de instalar? Para verificar que la hayamos instalado bien ejecutemos un comando muy similar:

```bash
$ python3 --version
Python 3.11.x
```

Si este comando nos da otra version de Python como 3.6 o 3.7, ejecutemos el siguiente comando más específico:

```bash
$ python3.11 --version
Python 3.11.x
```

¡Genial! Ahora nace otra pregunta: ¿Es posible instalar más de una versión al mismo tiempo? Pues sí, veamos este tema con más detalle en la siguiente sección.

## 2. Versiones de Python

A diferencia de otros programas que has utilizado, es posible instalar varias versiones de Python en la misma computadora. ¿Para qué nos sirve esto?:

1. Utilizar librerias "antiguas" que no se han actualizado a la nueva versión de Python pero las necesitas
1. Correr tus propios pogramas desarrollados anteriormente (hace 1 mes, 6 meses, 1 año o más)
1. Crear nuevos programas en la última versión de Python sin perder la capacidad de correr tus programas antiguos
1. ¿A ustedes se les ocurre alguna utilidad más?

### ¿Cómo las diferenciamos?

Dependiendo de tu sistema operativo Python se instala en un directorio específico, por:

- En Linux: `/usr/bin/python3`
- En Mac OS: `/Library/Frameworks/Python.framework`, `\usr\bin\python`, `Applications`
- En Windows: `C:\Python36\`

El siguiente comando nos permite ver qué versiones de Python están instaladas en una computadora con sistema operativo Linux:

```bash
$ ls /usr/bin/ | grep ^python
...
python
python2
python2.7
python3
python3.10
python3.11
python3.6
python3.7
...
```

El comando `ls` muestra los archivos de un directorio, en este caso el directorio es `/usr/bin/`. El comando `grep` filtra los archivos que inicien con la palabra "python" (`^python`). El resultado nos muestra varias versiones de Python instaladas en este sistema. ¿Cuántas son?

- `python`, `python2` y `python2.7` son ejecutables de la versión 2.7
- `python3` y `python3.6`, de la versión 3.6
- Las demás versiones corresponden a los números del nombre de cada archivo respectivamente: 3.7 3.10 y 3.11

Ahora que ya sabemos que podemos instalar diferentes versiones de Python, aprenderemos a usar de manera **segura** Python en nuestros proyectos. Para ello utilizamos ambientes virtuales, detallaremos más sobre este tema en la siguiente sección.

## 3. Ambientes virtuales

Los ambientes virtuales son instalaciones de Python aisladas y desechables que utilizaremos específicamente para un proyecto. Son aisladas porque en una sola carpeta tenemos todo lo que necesitamos para ejecutar programas de Python y son desechables porque si tenemos algún problema con el ambiente virtual simplemente eliminamos la carpeta y podemos volver a crear uno nuevo (algo así como apagar y prender la computadora cuando se congela).

### ¿Por qué los usamos?

- Para evitar "romper" la instalación principal de Python y afectar nuestro sistema.
- Para poder guardar un registro de las librerías necesarias para un proyecto específico (Más adelante en el curso veremos el famoso archivo `requirements.txt`)
- Existen más razones que experimentaremos al utilizar los ambientes virtuales en nuestros proyectos ...

### ¿Cómo los creamos?

Desde nuestro PowerShell o Command Prompt en Windows:

![python-env-windows](imgs/python-env-windows.png)

> **Posibles errores en Windows:**  
> [Añadir variable de entorno “python” / “python3”](https://geek-university.com/python/add-python-to-the-windows-path/)  
> [Solución de “ejecución e Scripts está deshabilitada”](https://www.cdmon.com/es/blog/la-ejecucion-de-scripts-esta-deshabilitada-en-este-sistema-te-contamos-como-actuar)

Desde nuestra Terminal en Mac OS o Linux:

![python-env-linux](imgs/python-env-linux.png)

## Retos: Ahora te toca a tí

1. Completa la instalación de Python correctamente
1. Completa la creación de tu carpeta de proyecto
1. Instala un editor de código. Oficialmente los profesores usaremos [VS Code](https://code.visualstudio.com/)
1. Crea y ejecuta tu primer script que imprima "Hello World!"
1. Instala [Jupyter Notebook](https://jupyter.org/install#jupyter-notebook) lo necesitarás para seguir la siguiente clase desde tu computadora 😉
