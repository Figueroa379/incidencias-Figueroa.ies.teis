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