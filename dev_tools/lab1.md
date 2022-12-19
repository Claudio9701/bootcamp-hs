

## ¿Y si quiero compartir mi código cómo hago?

Para eso está Github (o cualquier otro servicio similar). Primero debemos crearnos una cuenta en Github [desde aquí](https://github.com/join?ref_cta=Sign+up&ref_loc=header+logged+out&ref_page=%2F&source=header-home).

Luego, para Windows, puedes seguir este [tutorial de instalación](https://phoenixnap.com/kb/how-to-install-git-windows)

Antes de empezar, es necesario crear el repositorio en Github desde tu perfil dando click en `New`. SOLO completa el nombre y la descripción al crear el repo y puedos ponerlo en público. Con ello, nos dirigimos a la carpeta principal desde el terminal si estas en Mac o desde GIT si estas en windows y hacemos lo siguiente:

```sh
git init
```

Agregamos a la carpeta principal el gitignore, el cual sirve para ignorar todos los archivos que nos son importantes. Para esto crearemos un file **desde Visual Studio Code** dentro de nuestra carpeta principal llamado `.gitignore` y el contenido copiarlo [desde aquí](https://github.com/Mapaz04/linioexp/blob/main/.gitignore). No te preocupes si no ven el archivo desde tu Explorador, esta es una carpeta oculta. Seguimos agregando todas nuestras carpetas en el repositorio utilizando

```sh
git add .
```

> Dado que es la primera vez que utilizamos Github, siempre usemos el comando `git status`

A patir de este punto son pasos que se ven dentro de Github luego de creado el repo. A continuación hacemos nuestro primer **commit** usando el comando

```sh
git commit -m "mi primer commit"
```

Luego seguimos los pasos que muestran en github, aquí cambiamos el nombre de la rama principal a `main`en vez de `master`

```sh
git branch -M main
```

Después vamos a agregar todo a la nube usando lo siguiente:

```sh
git remote add origin https://github.com/{usuario}/{nombre-repo}.git
```

Finalmente usamos el comando, después le debes dar refrescar al repo en github y debes ver todos tus archivos :D

```sh
git push -u origin main
```

Estos pasos también los encontrarás al crear tu primer repositorio vacío.