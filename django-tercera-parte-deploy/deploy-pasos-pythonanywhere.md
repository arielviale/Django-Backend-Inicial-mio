Deploy en PythonAnywhere
---

NOMBRE_DE_USUARIO = <nombre_usuario_de_pythonwhere>


- Tener el repositorio del Proyecto Armado en github
    - https://github.com/andru-oca/vinoteca_django_deploy.git

- Generar una cuenta de en pythonanywhere
    - https://www.pythonanywhere.com/user/NOMBRE_DE_USUARIO/

- Generar una API token _esto se encuentra en account_

- Generar una consola en la dashboard , es necesario entender que es una máquina virtual en donde se está colocando el codigo como elemento necesario para poder levantarlo como elemento único a usar en ese entorno.
    - New Console >> Bash\
    <img src="./images/dashboard_console.png" width="300"/>

Steps en la máquina virtual
---

- verificar la versión de python disponible: por lo general es una version de +3.10 
- como buena práctica generar un usuario en este caso para seguir la documentación vamos usar el usuario pythonanywhere

    - pip install --user pythonanywhere

Forma manual entendiendo los pasos generales.

- clonar el repositorio
    - git clone <repositorio-git>

- crea el virtual env , suele tardar un poco, pero directamente te genera y activa el entorno

    - mkvirtualenv --python=python3 venv

- moverte al project descargado y trabajar internamente
    - cd <nombre_de_la_carpeta>

- instalar las dependecias en ese entorno usando los requirements.txt
    - pip install -r requirements.txt

- posterior a levantar el entorno y haber instalado las dependecias, es necesario ubicar los siguientes archivos:
    - **manage.py** =>  en mi caso : /home/NOMBRE_DE_USUARIO/vinoteca_django_deploy/vinoteca
    - **settings.py** => /home/NOMBRE_DE_USUARIO/vinoteca_django_deploy/vinoteca
    - el **virtualenv** => /home/NOMBRE_DE_USUARIO/.virtualenvs/venv

    - con esa información ahora podemos ir al sector de web de pythonanywhere > en este caso como es manual vamos a usar una configuracion manual > seleccionamos la version de python 3.10 > con este caso vamos a utilizar archivos como el wsgi de django

---

## Volvamos a la pagina principal en el sector del dashboard y crear una WEB App
- crear una web app utilizando de manera manual el proyecto.
- Add New Wep App > crear manualmente con la ultima version de python
- sector Code :

    - Source code: (el path de la carpeta donde estaría el proyecto)
        - /home/NOMBRE_DE_USUARIO/vinoteca_django_deploy/vinoteca


- modificacion del wsgi_file:

```
# +++++++++++ DJANGO +++++++++++
import os
import sys

# le indicamos el path del project
path = '/home/NOMBRE_DE_USUARIO/vinoteca_django_deploy/vinoteca'
if path not in sys.path:
    sys.path.append(path)

from django.core.wsgi import get_wsgi_application
# le indicamos el lugar de los settings
os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'vinoteca.settings')
application = get_wsgi_application()
```


## Volvamos a la pagina principal en el sector web app y agregamos

- agregamos la fuente del entorno virtual
    - /home/NOMBRE_DE_USUARIO/.virtualenvs/venv

---
### Ir a la consola de PythonAnywhere y seguir los pasos en el proyecto

- editemos los settings.py para generar el deploy necesario: 

    - Modificaciones: 
        - DEBUG= False
        - ALLOWED_HOSTS = ["NOMBRE_DE_USUARIO.pythonanywhere.com"]
        - STATIC_ROOT = BASE_DIR / "static"
        - comentamos el directorio de los STATICFILES_DIRS

- al tener esto ya creado en sí podemos correr, pero nos van a dar unos errores pero son inherentes  a la configuracion de los settings.


- migramos la base de datos, en este caso aun mantenemos el archivo sqlite3, pero lo podemos migrar con alguna base de datos, como la que nos presta pythonanywhere en mysql.
    - python manage.py makemigrations
    - python manage.py migrate



- generemos los archivos en un solo lugar que serán los archivos staticos.
    -   python manage.py collectstatic
        - seleccionamos la opcion de "yes" para  hacer una collect de los statics

- Agregar en web app page en el sector de STATIC URL  /static/ => y apregar el path   
    - /home/NOMBRE_DE_USUARIO/vinoteca_django_deploy/vinoteca/static

- Paso final hacemos un reload para cargar la nueva versión.



Integracion con una base de datos en PythonAnyWhere
---

- Creacion de una database en el sector de Database
- Crear un database con Database name en el sector de Create a Database
- DATABASE NAME:
       ==> vinoteca
  Creandome una base de datos con el nombre de **NOMBRE_DE_USUARIO**$vinoteca
  
    * host : <nombre_de_usuario>.mysql.pythonanywhere-services.com
    * username : <nombre_de_usuario>
    * port : 3306
    * password : vinoteca1234
    * database : <nombre_de_usuario>$vinoteca

-   Integracion en settings.py

    ```
    DATABASES = {
    'default': {
                'ENGINE': 'django.db.backends.mysql',
                'NAME': 'NOMBRE_DE_USUARIO$vinoteca',
                'USER': 'NOMBRE_DE_USUARIO',
                'PASSWORD': 'vinoteca1234',
                'HOST': 'NOMBRE_DE_USUARIO.mysql.pythonanywhere-services.com',
                'PORT': '3306',
            }
    }
    ```

- Hacer la migración:
    - python manage.py makemigrations
    - python manage.py migrate

HACER UN RELOAD EN EL SECTOR DE WEB


[BIBLIOGRAFIA EXTRA Deploy en pythonanywhere](https://zappycode.com/tutorials/deploy-django-project-on-pythonanywhere)
