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