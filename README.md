# Manual de instalacion de la aplicacion web



## Decisiones de proyecto

|Elemento|Version|Decision|Justificacion|
|--------|-------|--------|-------------|
|Serviodor web|Apache|2|Sencillo de usar|
|Base de datos|MySQL|8|Experiencia previa, popular|
|Lenguaje servidor|Python|3|Muy interesante para ASIR, uso extendido|
|Framework|Flask|3|Sencillo de usar, pensado expecificamente pa4ra web(formularios, sesiones)|
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
   
1. creamos la base de datos con `create database incidencias;`
2. Creamos usuario `create user 'incidencias'@'localhost' identified by 'incidencias';` y le damos permiso a un usuario `grant all privileges on incidencias.* to 'incidencias'@'localhost';`
3. Crear una tabla `create table registro( id int auto_increment primary key, aula varchar(30), descripcion text, usuario varchar(20), estado varchar(30) );`
4. Metemos datos a esa tabla `insert into registro (aula, descripcion, usuario, estado) values ('taller 1', 'PC 7 no da señal', 'afigueroa', 'Abierto');`


## Crear github

1. Crear repositorio local crear repositorio `git init`, añadir repositorio que no este `git add.`,  `git commit -m "comentario"`
2. Crear cuenta gihub, crear repositorio en github.
3. Conectar repositorio local con remoto `git remote add origin https://github.com/Figueroa379/incidencias-Figueroa.ies.teis.git`, `git branch -M main`, `git push -u origin main`.
4. Para guardar usaremos `git add.`, `git commit -m "comentario"` y por ultimo para subirlo `git push`.

## Instala e iniciar python

1. En visual studio code nos iremos a extensiones y añadiremos el python de microsoft.
2. Luego intalaremos con un comando en la terminal dos extensiones de python `sudo apt install python3 python3-pip python3-venv -y`
3. Desde el terminal de visual para que ya se habra desde la ubicacion de nuestros archivos crearemos el entorno virtual pondremos `sudo apt install python3 python3-pip python3-venv -y`y `luego source venv/bi
n/activate`
4. Vamos a instalar desde el terminal de visual lo siguiente para conectar nuestro mysqlserver `pip install flask` `pip install flask` `pip install flask`

## Crearemos restriciones para lo que se guarda en github

Crearemos un nuevo documento en visual el cual llamaremos `.gitignore` luego dentro de este documento le pondremos las reglas de los documentos que no queremos que guarde:
venv/
__pycache__/
*.pyc
.env

siempre al empezar el proyecto se lanza el entorno virtual con `source venv/bin/activate` y para lanzarla `python app.py`, para para la app presionaremos ctrl+c y para salir del entorno pondremos `deactivate`.

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
sudo a2enmiod proxy
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

    return "Incidencia recibida"

if __name__ == "__main__":
    app.run(debug=True)
```

## Introducir los datos en la BD

1. Importamos 