# Manual de instalacion de la aplicacion web



## Decisiones de proyecto

|Elemento|Version|Decision|Justificacion|
|--------|-------|--------|-------------|
|Servidor web|Apache|2|Sencillo de usar|
|Base de datos|MySQL|8|Experiencia previa, popular|
|Lenguaje servidor|Python|3|Muy interesante para ASIR, uso extendido|
|Framework|Flask|3|Sencillo de usar, pensado expecificamente para web(formularios, sesiones)|
|Control de versiones|Git|2|Muy extendido|
|Documentacion|Markdown|-|Muy utilizado con git|

## Que hace un servidor web?

Recibe peticiones HTTP y devuelve recursos al navegador del usuario.

## Proceso de instalacion / puesta en marcha

1. Actualizar el sistema
`sudo apt update`
`sudo apt upgrade`
2. Instalar git
`sudo apt install git`
3. Instalar VSCode + plugins:
    - Markdown all in one
4. Instalar Apache2
`sudo apt install apache2`
5. Cambiar permisos carpeta /var/www/html
`sudo chown -R $USER:$USER /var/www/html`
`sudo chmod -R u=rwX,go=rX /var/www/html`
6. Vamos a instalar mysql server
`sudo apt install mysql-server`
1. Accedemos a mysql con `sudo mysql`. 

 ## Configuramos nuestra base de datos  
   
1. creamos la base de datos con `create database incidencias;`.
2. Creamos usuario `create user 'incidencias'@'localhost' identified by 'incidencias';` y le damos permiso a un usuario `grant all privileges on incidencias.* to 'incidencias'@'localhost';`
3. Seleccionamos la base de datos que vamos a usar con `use incidencias` creamos una tabla `create table registro( id int auto_increment primary key, aula varchar(30), descripcion text, usuario varchar(20), estado varchar(30) );`
4. Metemos datos a esa tabla `insert into registros (aula, descripcion, usuario, estado) values ('taller 1', 'PC 7 no da señal', 'afigueroa', 'Abierto');`


## Crear github

1. Crear repositorio local crear repositorio `git init`, añadir repositorio que no este `git add .`,  `git commit -m "comentario"`
2. Crear cuenta gihub, crear repositorio en github.
3. Conectar repositorio local con remoto `git remote add origin https://github.com/Figueroa379/incidencias-Figueroa.ies.teis.git`, `git branch -M main`, `git push -u origin main`.
4. Para guardar usaremos `git add .`, `git commit -m "comentario"` y por ultimo para subirlo `git push`.
5. Si lo queremos descargar desde otro lugar usaremos `git pull`y en caso de no tener nada podemos hacer un `git clone` para bajar todo.

## Instala e iniciar python

1. En visual studio code nos iremos a extensiones y añadiremos el python de microsoft.
2. Luego intalaremos con un comando en la terminal dos extensiones de python `sudo apt install python3 python3-pip python3-venv -y`
3. Desde el terminal de visual para que ya se habra desde la ubicacion de nuestros archivos crearemos el entorno virtual pondremos `python3 -m venv venv` y luego `source venv/bin/activate`
4. Vamos a instalar desde el entorno virtual que acabamos de entrar lo siguiente para conectar nuestro mysqlserver `pip install flask mysql-connector-pyhon`.

## Crearemos restriciones para lo que se guarda en github

Crearemos un nuevo documento en visual el cual llamaremos `.gitignore` luego dentro de este documento le pondremos las reglas de los documentos que no queremos que guarde:
venv/
__pycache__/
*.pyc
.env

## Lanzar app.py

1. siempre al empezar el proyecto se lanza el entorno virtual con `source venv/bin/activate`.
2. Para lanzar nuestro python usaremos `python app.py`.
3. Para parar la app presionaremos *ctrl+c* y para salir del entorno virtual pondremos `deactivate`.

## Crear archivo app.py 

1. Creamos el archivo que llamaremos "app.py"
2. En el archivo pondremos lo siguiente:
```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def hello():
    return "<h1>Incidencias Ies Teis</h1>"

if __name__ == "__main__":
    app.run(debug=True)
```
3. para probarla lanzaremos `python3 app.py`
    - Esto crea un servidor web alternativo levantado en localhost en el puerto 5000. Se podria poner Apache de intermediario utilizando un proxy
    - Habria que modificar /etc/apache2/sites-avaliable/incidencias-Figueroa.ies.teis.conf añadiendo esto dentro de virtualhost:
```
    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/

```
- Y activar los modulos
```
sudo a2enmod proxy
sudo a2enmod proxy_http
sudo systemctl restart apache2
```
4. Para ver si funciona podremos verlo desde el navegador  teniendo en cuenta que trabajamos en el puerto 5000 `*:5000`

## Migracion de formulario a Python/Flask

1. Creamos la carpeta templates y movemos ahi el index
2. Modificamos el app.py:
```python
from flask import Flask, render_template

app = Flask(__name__)

@app.route("/")
def hello():
    return render_template("index.html")

if __name__ == "__main__":
    app.run(debug=True)
```

## Recibir datos del formulario

Cambiamos el app.py:
```python
from flask import Flask, render_template, request

app = Flask(__name__)

@app.route("/")
def hello():
    return render_template("index.html")

@app.route("/incidencia", methods=["POST"])
def crear_incidencia():

    aula = request.form["aula"]
    usuario = request.form["usuario"]
    descripcion = request.form["descripcion"]

    print("Aula:" + aula)
    print("Usuario:" + usuario)
    print("Descripción:" + descripcion)

    return "<h1>Incidencia recibida</h1><ul><li>Aula: " + aula + "</li></ul>"

if __name__ == "__main__":
    app.run(debug=True)
```

## Introducir los datos en la BD

1. Importamos  el conector de mysql despues de las importaciones de Flask. El acceso a la base de datos es independiente de Flask:
```app.py
from flask import Flask, render_template, request
import mysql.connector
```

2. Ahora vamos a modificar el app.py para enlazar la bd, nos aseguraremos que losnombres de los datos sean exactamente los mismos que tenemos en la base de datos.
```python
from flask import Flask, render_template, request
import mysql.connector

app = Flask(__name__)

@app.route("/")
def hello():
    return render_template("index.html")

@app.route("/incidencia", methods=["POST"])
def crear_incidencia():

    aula = request.form["aula"]
    usuario = request.form["usuario"]
    descripcion = request.form["descripcion"]

    print("Aula:" + aula)
    print("Usuario:" + usuario)
    print("Descripcion:" + descripcion)

    conexion = mysql.connector.connect(
        host="localhost",
        user="incidencias",
        password="incidencias",
        database="incidencias"
    )

    cursor = conexion.cursor()

    sql = """
    INSERT INTO registros
    (aula, usuario, descripcion, estado)
    VALUES (%s, %s, %s, %s)
    """

    valores = (
        aula, 
        usuario, 
        descripcion, 
        "Abierta"
        )
    cursor.execute(sql, valores)

    conexion.commit()

    cursor.close()
    conexion.close()
    
    
    return "<h1>Incidencia recibida</h1><ul><li>Aula: " + aula + "</li></ul>"

if __name__ == "__main__":
    app.run(debug=True)
```

3. Ahora que ya esta enlazada cuando introduzcamos los datos desde la pagina se añadiran automaticamente.