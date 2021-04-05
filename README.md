<a id="top"></a>
# Tu proyecto Laravel a Producción por 1ª vez

_Con esta guía aprenderás a hacer **Deploy** de Tu Proyecto Laravel en un **hosting compartido** por Primera Vez._

### Aclaraciones 📣️

- Todos los ejemplos en interfaz web de esta guía se basan en el administrador de hosting `cPanel`.

- De igual manera es posible realizar **procedimientos similares en otros administradores**, ante la duda puedes consultarlo con tu proveedor generando un nuevo Ticket.

- Esta guía aplica **SOLO a proyectos listos** para producción.

**Ninguna de las optimizaciones realizadas aquí debe ser aplicada durante la etapa de desarrollo.**

> Si detectas algún error, comenta en la sección "issues" del [proyecto en git](https://github.com/Guilledll/laravel-a-produccion-por-1-vez/issues).

### Índice de contenido
* **[Requisitos previos 📋️](#Requisitos_previos)**
	* [Dependencias](#Dependencias)
	* [Terminal](#Terminal)
	* [No tengo terminal ¿qué hago?](#No_tengo_terminal)
* **[Comenzando 🚀️](#Comenzando)**
* **[Comprimiendo 📦️](#Comprimiendo)**
	* [No necesitamos](#No_necesitamos)
	* [Necesitamos](#Necesitamos)
* **[Cargando los archivos 🗃️](#Cargando_los_archivos)**
* **[Gestionando los archivos 🗂️](#Gestionando_los_archivos)**
	* [Cambiar los permisos](#Cambiar_los_permisos)
	* [Re-ubicando public](#Re_ubicando_public)
	* [Modificando index.php](#Modificando_index.php)
* **[Conexion a base de datos 💾️](#Conexion_a_base_de_datos)**
	* [Configurando desde 0](#Configurando_desde_0)
	* [Permisos de usuario](#Permisos_de_usuario)
* **[Ajustando variables de entorno 🔧️](#Ajustando_variables_de_entorno)**
	* [Variables APP_](#Variables_APP_)
	* [Variables DB_](#Variables_DB_)
	* [Variables MAIL_](#Variables_MAIL_)
	* [Otras variables](#Otras_variables)
* **[Cargando la base 🔋️](#Cargando_la_base)**
	* [Cargar por terminal web](#Cargar_por_terminal_web)
	* [Cargar de forma manual](#Cargar_de_forma_manual)
	* [phpMyAdmin](#phpMyAdmin)
* **[Optimizar Laravel 🚀️](#Optimizar_laravel)**
	* [Comandos](#Comandos)
	* [Generar nuevo cache en el servidor](#Generar_nuevo_cache_en_el_servidor)
	* [Por qué gestionar el cache](#Por_qué_gestionar_el_cache)
* **[Puesta en marcha 🛰️](#Puesta_en_marcha)**
* **[Gestión de registros 📑️](#Gestión_de_registros)**
* **[Solución de problemas 🛠️](#Solución_de_problemas)**
	* [Modo mantenimiento](#Modo_mantenimiento)
	* [Activando modo debug](#Activando_modo_debug)
* **[Problemas generales 📜️](#Problemas_generales)**
	* [Permisos de archivos](#Permisos_de_archivos)
	* [Base de datos](#Base_de_datos)
	* [Configuración antigua](#Configuración_antigua)
	* [Versión php](#Versión_php)
	* [Correos](#Correos)
	* [Otros errores](#Otros_errores)
* **[Verificando los cambios 🏷️](#Verificando_los_cambios)**
* **[Activar la web 💡️](#Activar_la_web)**
* **[Extras](#Extras)**
	* [Sugerencias](#Sugerencias)
	* [Autor](#Autor)

<a id="Requisitos_previos"></a>
## Requisitos previos 📋️

_A continuación veremos todo lo que necesitamos para cargar nuestro proyecto a un hosting compartido._

<a id="Dependencias"></a>
### Dependencias 📦️

Se asume desde ya que tu proyecto se encuentra listo para ser cargado, también que tienes instalado y conoces los manejadores [Composer](https://getcomposer.org/) y [NPM](https://www.npmjs.com/get-npm).

<a id="Terminal"></a>
### Terminal 🖥️

Para completar al **100%** esta guía `es necesario tener acceso a la Terminal Web en tu hosting`, en ella podrás gestionar algunos de los **comandos artisan** que veremos más adelante.

> En cPanel puedes acceder en `Avanzado` -> `Terminal`.

<a id="No_tengo_terminal"></a>
#### No tengo la terminal, pero la quiero usar ¿Qué hago?

En muchos casos `la terminal web no está habilitada por defecto`, por lo que debes pedir a tu proveedor de servicio que la habilite. Lo más sencillo es **enviar un correo** o abrir un nuevo **Ticket de consulta**.

Una vez habilitada se verá similar a esto:

![Terminal](https://mishorasweb.com/img/guia-prod/terminal.png)

<a id="Comenzando"></a>
## Comenzando 🚀️

_Una vez terminado el desarrollo, nos vamos a la raíz de nuestro proyecto e ingresamos los siguientes comandos en una **terminal local**:_

_Para optimizar JS y CSS_
```
npm run production
```

Se debe activar si trabajas con **Frameworks JS y/o CSS** tales como:
* **JS** - `React`, `Vue`, `Angular`, `Alpine.Js`, etc
* **CSS** - `Bootstrap`, `Tailwind`, etc.

_Para optimizar paquetes en laravel_
```
composer install --optimize-autoloader --no-dev
```
Este comando de Composer permite optimizar toda la carga de clases y paquetes dentro de tu aplicación.

> ❗️ Se debe ejecutar cuando se hayan modificado los paquetes o dependencias ❗️


_Para limpiar todo el cache local_
```
php artisan route:clear && php artisan config:clear && php artisan cache:clear
```
<a id="Comprimiendo"></a>
## Comprimiendo el proyecto 📦️

_Procedemos a crear el comprimido que contendrá todas los archivos necesarios del programa, veremos que hace falta y que no._

<a id="No_necesitamos"></a>
#### No necesitamos ✖️

_No es necesario comprimir ni subir algunos archivos, podemos eliminarlos en el gestor de archivos o antes de generar el comprimido._

> Con esto puedes optimizar espacio y capacidad de archivos en tu hosting.

`/.git` - Contiene la información relacionado a la gestión de GIT.

`/storage/logs/xxx.log` (contenido de la carpeta logs)

_Los archivos existentes en la carpeta Logs se generaron durante el desarrollo, debemos borrarlos para que existan solo los nuevos generados en producción._

> **Nota:** En algunos sistemas es posible eliminar estas carpetas del comprimido sin necesidad de re-comprimir.

<a id="Necesitamos"></a>
#### Necesitamos ✔️

_Comprima la carpeta raíz de su proyecto, incluyendo `vendor` y `node_modules` (contienen las dependencias php y js)._

El resultado final será un comprimido (.zip, .tar, etc.) que contiene todas las carpetas y archivos: `app`, `database`, `vendor`, `artisan`, `webpack`, etc.

❗️ **Atención**: En algunos casos los archivos dot (.) no se incluyen durante la compresión, por ejemplo `.htaccess en /public` o `.env en /raíz`. De ser así no se preocupe, los podremos generar manualmente más adelante.

<a id="Cargando_los_archivos"></a>
## Cargando los archivos 🗃️

_Veamos los procedimientos que debemos seguir para cargar nuestro proyecto en el servidor._

Este ejemplo está basado en interfaz de cPanel, el proceso no suele variar mucho entre distintos hostings:

* **Ir a Archivos**
* **Sección de carga de archivos**
* **Cargar comprimido en directorio raíz**
* **Descomprimir**

El resultado será parecido a este:

```
🏠️ /home/tu-usuario/tu-proyecto-laravel
```

Felicidades tu aplicación ya está cargada! 🏆️🎉️

<a id="Gestionando_los_archivos"></a>
## Gestionando los archivos 🗂️

_Ahora es necesario realizar algunos ajustes antes de pasar a configuraciones más profundas. Revisaremos la seguridad general._

<a id="Cambiar_los_permisos"></a>
### Cambiar los permisos 🔒️

_Es común que desarrollemos el proyecto como `administradores`, con `permiso 777` o `sudo` en linux, si no cambiamos esto en producción los **archivos pueden ser modificados por terceros**._

🛡️ Por defecto `Laravel/Symphony` notifica que los archivos tienen `permiso 777` y por ende personas no deseadas pueden modificarlos, **veamos como evitar esta vulnerabilidad**:

Para no extender de más la guía incluiré en esta sección a un **tutorial** que te enseña **[Como Gestionar y Cambiar los Permisos en tu Proyecto Laravel 🌐️](https://qastack.mx/programming/30639174/how-to-set-up-file-permissions-for-laravel)**.

> Todos los créditos a sus respectivos creadores

Una vez **[completado el tutorial](https://qastack.mx/programming/30639174/how-to-set-up-file-permissions-for-laravel)** nuestra aplicación se encuentra **segura frente a escrituras y ejecuciones maliciosas.**

<a id="Re_ubicando_public"></a>
### Re-ubicando public 📌️

Como buena practica separaremos el proyecto en dos secciones, la carpeta "public" y el resto. Hacemos esto ya que **no es necesario tener todo el contenido del programa en la carpeta public_html** de nuestro hosting, con solo algunos archivos allí Laravel hará funcionar la aplicación.

Moveremos el contenido la carpeta "public" ``` /home/tu-usuario/tu-proyecto-laravel/public ``` hacia ``` /home/public_html ```, esta ruta es donde accede el usuario cuando visita la página. La carpeta `public_html` será similar a esta:

 ![public_html](https://mishorasweb.com/img/guia-prod/public.png)

_Las carpetas y archivos varían según cada proyecto pero la estructura es similar._


> Queda a criterio de cada uno si eliminar o no la carpeta `/tu-proyecto-laravel/public`.


<a id="Modificando_index.php"></a>
### Modificando index.php ⚙️

_Este archivo se encarga de hacer funcionar nuestra aplicación, si lo examinamos vemos que invoca archivos de dos carpetas, entre otras varias cosas. Las archivos son ` /vendor/autoload.php ` y ` /bootstrap/app.php `._

La primera inclusión es: `__DIR__.'/../vendor/autoload.php `

![vendor](https://mishorasweb.com/img/guia-prod/index-autoload.png)
 
La segunda inclusión es: `__DIR__.'/../bootstrap/app.php `

![bootstrap](https://mishorasweb.com/img/guia-prod/index-bootstrap.png)

Si lo analizamos, las inclusiones buscan las carpetas `/vendor` y `/bootstrap` que se encuentren en esa ruta, pero estos no existen allí porque **los separamos de public en el paso anterior!**.

_Para logar acceder a estos archivos debemos modificar los "require"_.

Como el contenido de public ahora está en public_html y vendor/bootstrap en la carpeta ``` /home/tu-usuario/tu-proyecto-laravel ``` debemos indicar la búsqueda de los archivos en una ruta anterior, para ello **cambiamos los "require"**:

La nueva primera inclusión: `__DIR__.'/../tu-proyecto-laravel/vendor/autoload.php `

La nueva segunda inclusión: `__DIR__.'/../tu-proyecto-laravel/bootstrap/app.php `

❗️ **Importante:** Evite modificar el archivo index.php al cargar nuevas versiones. De ser así, actualice las inclusiones de inmediato❗️

**De esta forma, nuestro proyecto ya puede cargar todo lo necesario!** 🏆️

<a id="Conexion_a_base_de_datos"></a>
## Conexión a Base de Datos 💾️

_Realizaremos la conexión completa con la base de datos de producción._

❗️ Los siguientes ejemplos se basan en cPanel, los procesos no suelen variar mucho entre los distintos proveedores.

Recuerda copiar los datos que generaremos ahora: `usuario`, `contraseña` y `base de datos.`

<a id="Configurando_desde_0"></a>
### Configurando desde 0

_Crearemos todo mediante la interfaz web._

`Databases` -> `MySQL Databases`
_Aquí podremos crear una **nueva base** para nuestro proyecto._

![db-crear](https://mishorasweb.com/img/guia-prod/db-crear.png)

_**Creamos el usuario** que utilizará la base de datos._

![db-crear](https://mishorasweb.com/img/guia-prod/db-user.png)

_**Agregamos el usuario** a la base de datos para que la pueda manipular._

![db-crear](https://mishorasweb.com/img/guia-prod/db-agregar-user.png)

_Si tenemos acceso a un gestor de BD podemos comprobar que la base se haya configurado de manera correcta._

<a id="Permisos_de_usuario"></a>
#### Permisos de usuario 🔓️

_Evitemos errores y revisemos que los permisos del usuario sean correctos para esa base._

Esto permite que el usuario pueda ejecutar las sentencias `SELECT`, `INSERT`, `UPDATE`, `DELETE`, etc. de `SQL`.

<a id="Ajustando_variables_de_entorno"></a>
## Ajustando variables de entorno 🔧️

_Ahora trabajaremos con el archivo .env de tu proyecto._

Nos aseguramos que **.env** se haya respaldado en el comprimido, de no ser así, creamos un nuevo .env en la raíz de nuestro proyecto `/tu-proyecto-laravel/.env` y copiamos dentro todos los datos del archivo local.

_Cambio de variables: Revisaremos 3 secciones de nuestro .env_

> Los datos pueden variar según cada proyecto.

<a id="Variables_APP_"></a>
#### Variables APP_

![env_data](https://mishorasweb.com/img/guia-prod/env-app.png)

* ENV = `local` por `production`

_Con esto Laravel sabe que el entorno ahora es de Producción._

* KEY = `base64:xxx...` por `key:generate`

_Key es un atributo de seguridad, para actualizarlo en producción crearemos una nueva clave en la terminal local._

Ingresamos comando: `php artisan key:generate`

Nos creará una nueva key en **.env local** la cual copiamos y **pegamos en el .env de producción**.

* DEBUG = `true` por `false`

_Debug define si mostrar errores estando en Producción, es importante establecerlo FALSE para que el usuario no vea información sensible en caso de error._

* URL = `http://localhost` por `https://mi-dominio.com`

_Cambiamos la URL local por nuestro dominio._

<a id="Variables_DB_"></a>
#### Variables DB_

❗️ Es aquí cuando utilizamos los datos `usuario`, `contraseña` y `base de datos` que habíamos guardado antes.

![env_db](https://mishorasweb.com/img/guia-prod/env-db.png)

* DATABASE = `tu-base-local` por `tu-base-web`

_La base local será remplazada por la base web._

* USERNAME = `user-local` por `user-web`

_Ingresamos el usuario con acceso a la DB de nuestro hosting._

* PASSWORD = `pass-local` por `pass-web`

_Cambiamos por la contraseña de ese usuario._

<a id="Variables_MAIL_"></a>
#### Variables MAIL_

❗️ Esta configuración no es necesaria si **no pensamos utilizar los servicios de correo** de nuestro dominio o de terceros.

En este ejemplo se ve el uso de [Mailtrap](https://mailtrap.io/) para la prueba de correos durante desarrollo, vamos a cambiar esto.

![env_mail](https://mishorasweb.com/img/guia-prod/env-mail.png)

_Necesitamos contactar con nuestro proveedor para obtener algunos datos:_ `MAILER`,  `PORT` nos lo brinda nuestro proveedor.

* HOST = `smtp.mailtrap.io` por `tu-dominio.com`

_Cambiamos el host de prueba (si existe) por nuestro dominio web._

📨️ Para estos datos debemos de tener un correo existente bajo nuestro dominio.

* USERNAME = `test-user` por `real-mail-user`

_Ingresamos el correo completo **example@tu-dominio**._

* PASSWORD = `test-pass` por `real-mail-pass`

_La contraseña de ese correo._

* FROM_ADDRESS = `quien-envia@tu-dominio.com`

<a id="Otras_variables"></a>
#### Otras variables

_Recuerda además cambiar **todas las variables** que hayas modificado en tu proyecto._

---

Ya realizadas las modificaciones ingresamos en la **Terminal Web** el comando: `php artisan config:cache` para almacenar las nuevas configuraciones.

**En caso de no tener Acceso a la Terminal Web**, procederemos a borrar el cache de nuestro navegador para que se descargue el nuevo archivo de configuración al recargar.

**Nuestro .env ya está configurado y listo para funcionar! 🎉️**

<a id="Cargando_la_base"></a>
## Cargando la base 🔋️

_Veremos dos formas de cargar la base de datos en nuestro servidor, utilizando la Terminal Web o cargando la BD manualmente._

<a id="Cargar_por_terminal_web"></a>
#### Cargar por Terminal Web 📟️

❗️ **Si tienes la Terminal Web habilitada.**

Ingresamos a la terminal y nos situamos en el directorio raíz de nuestro proyecto con este comando: `cd /home/usuario/ruta/a/tu/proyecto`.

Estando allí ingresamos el comando:
```
php artisan migrate
```

_Con este comando **quedará generada nuestra base de datos** sin ningún registro, completamente limpia y lista para usar!_

<a id="Cargar_de_forma_manual"></a>
#### Cargar de forma manual 🤲️

Para los que no tienen acceso a la Terminal Web, ingresaremos un comando que nos permite generar una nueva BD limpia y lista para cargar:

1. Respaldar la BD de desarrollo Local (Si es necesario)
2. En la raíz de nuestro proyecto Local ingresamos

```
php artisan migrate:fresh
```

Este comando limpiará nuestra BD Local. Una vez terminadas las migraciones accederemos a la interfaz de phpMyAdmin en nuestro entorno Local. Generalmente la URL es: ``` http://localhost/phpmyadmin ```.

<a id="phpMyAdmin"></a>
#### phpMyAdmin

Estando en la interfaz iremos a nuestra BD limpia y en la barra superior seleccionaremos **"exportar"**, se generará un **.sql** que contiene la BD sin registros.

Con este archivo nos iremos a nuestro gestor **phpMyAdmin Web o al gestor de BD que nos ofrezca nuestro hosting** e importamos nuestro .sql en la base de datos que creamos antes.

Finalizada la importación comprobaremos que nuestra nueva base de datos se haya cargado correctamente.

**En este punto tendremos nuestra Base de Datos pronta para usar 👏️**

<a id="Optimizar_laravel"></a>
## Optimizar Laravel 🚀️

_Ahora nos enfocaremos en optimizar la carga y velocidad de nuestra página aplicando algunos comandos `artisan` que Laravel tiene para ofrecernos._

Esta sección tiene 2 procesos:
* Limpiar el cache generado durante el desarrollo
* Generar nuevo cache estando en el servidor

🗳️ **Recordemos que ya limpiamos el cache al inicio de la guía!**

Es necesario haber limpiado el cache almacenado durante el desarrollo, ya que conservarlo puede generar errores.

Generaremos nuevo cache **SOLO si tenemos acceso a la terminal web**,
esto agiliza los tiempos de carga y procesamiento.

<a id="Comandos"></a>
### Comandos ⌨️

❗️ **Requiere el uso de la terminal web!** Aplicaremos estos comandos **SOLO** si tenemos la terminal web.

Nos situamos en la raíz de nuestro proyecto. Por ejemplo en `/home/tu-usuario/tu-proyecto-laravel/`.

🧐️ _Si no podemos utilizar los comandos quiere decir que estamos parados en la ruta incorrecta!_

_Limpiar el cache de las rutas_
```
php artisan route:clear
```
_Limpiar las configuraciones_
```
php artisan config:clear
```
_Limpieza de cache general_
```
php artisan cache:clear
```

_Esta linea realiza todo en una sola maniobra_ 📃️✂️
```
php artisan route:clear && php artisan config:clear && php artisan cache:clear
```

En este punto **se habrá eliminado todo el cache generado durante el desarrollo local.**

<a id="Generar_nuevo_cache_en_el_servidor"></a>
### Generar nuevo cache en el servidor 📔️

❗️ **NO utilizar los siguientes comandos si no tenemos acceso a la Terminal Web** ❗️

_Optimiza los archivos de configuración_
```
php artisan config:cache
```
_Optimiza las carga de rutas_
```
php artisan route:cache
```
_Compila archivos blade para no hacerlo en demanda_
```
php artisan view:cache
```

_Esta linea realiza todo en una sola maniobra_ 📃️✂️
```
php artisan route:cache && php artisan config:cache && php artisan view:cache
```

En este punto **se habrá generado nuevo cache con las directivas del servidor.**

❗️ **NO utilizar los comandos anteriores si no tenemos acceso a la Terminal Web** ❗️

<a id="Por_qué_gestionar_el_cache"></a>
### ¿Por qué gestionar el cache? 🤔️

Al desarrollar de manera local las configuraciones y datos se almacenan con rutas similares a  `/var/www/html/tu-proyecto-laravel/`, si no eliminamos el cache, el servidor buscará trabajar con esas rutas y esto generará errores.

_Limpiar cache_
```
La limpieza de cache debe ser ejecutada SI o SI ✔️
```
_Generar nuevo cache_

❗️ SOLO debemos generar nuevo cache si tenemos acceso a La Terminal Web ❗️

<a id="Puesta_en_marcha"></a>
## Puesta en marcha 🛰️

_Llegó la hora de probar la web, prueba ingresar a tu dominio y admira tu aplicación Laravel._

La primera vez que ingreses a tu dominio, se generará en tu navegador cache perteneciente e este, debemos ser conscientes que cualquier configuración mal realizada YA ESTÁ almacenada.

**¿Qué sucede si debemos cambiar cosas?**

Es normal que ocurran errores cuando ingreses a tu web:
* Error de acceso
* Pantalla en blanco
* Las rutas no funcionan
* La API no responde
* Y un largo etc.

Para esto nos encargaremos de revisar algunos posibles errores y como solucionarlos.

❗️ **Recomendación:** Tener acceso a configuración para **borrar cache en tu navegador**, es posible que lo necesitemos hacerlo varias veces.

<a id="Gestión_de_registros"></a>
## Gestión de registros (logs) 📑️

_¿Algún error? Laravel cuenta con su propio sistema de registros integrado. Aquí vemos la importancia de limpiar la carpeta ```/storage/logs``` de tu proyecto._

**Laravel / Symphony**

El Framework nos provee un sistema de registros que indica errores dentro del sistema, aquí podemos ver que es lo que está fallando.

**Logs del hosting**

Nuestro hosting nos provee registros de errores que suceden más allá de lo que Laravel puede registrar, con esto nos referimos a errores de servidor, configuración general, entre varios otros.

En cPanel los podemos encontrar en  `Metrics` -> `Errors`

<a id="Solución_de_problemas"></a>
## Solución de problemas 🛠️

_Esta sección es la más amplia y compleja de la guía. Aquí se deja a libre criterio y capacidad de cada uno para resolver los distintos problemas que se puedan generar._

**¿Errores?¿No sabes porque o de donde vienen?** Probemos activando el modo Mantenimiento y cambiando configuraciones.

<a id="Modo_mantenimiento"></a>
### Modo mantenimiento ⚙️

_Es ideal para que nadie pueda acceder a tu web mientras tu haces las pruebas necesarias. Veremos como configurar para que puedas acceder solo tu._

El **Modo Mantenimiento inhabilita el acceso a la web** para que podamos trabajar los cambios necesarios sin que haya conflictos. Al intentar ingresar se mostrará el mensaje "503 servicio en mantenimiento".

> Te invito a ver luego:

**[Cambiar pantalla de mantenimiento "503"](https://youtu.be/tFBfPKSBG4Y)** - Ver vídeo - _Simply UY_

❗️ **Requiere uso de terminal web** ❗️

La indicación **--secret** permite dar acceso a tu web solo a los usuario que tienen el **token de seguridad**, vamos a ver un ejemplo de como funciona esto.

_Accedemos a la **terminal web** e ingresamos en el directorio raíz de nuestro proyecto:_
```
php artisan down --secret="tu-token-secreto"
```
Se indicará modo mantenimiento activo.

_En este punto, probamos acceder a nuestra URL y comprobamos el mensaje "503"._

Ahora ingresamos de la siguiente forma para tener acceso a nuestra web:

En la URL del navegador ingresamos:
```
http://tu-dominio.com/tu-token-secreto
```
Enseguida estaremos dentro de nuestra web. Ahora podremos cambiar la configuración para mostrar errores.

<a id="Activando_modo_debug"></a>
### Activando modo debug 🐞️

_Nuestro archivo .env tiene el modo debug desactivado porque así se lo indicamos, cambiaremos esto para que se indiquen los posibles errores._

En nuestro .env cambiamos:

* APP_DEBUG = ```false``` por ```true```

**¿Todavía no aparecen los errores?** Intenta borrando el cache para que se cargue la nueva configuración o aplica `php artisan config:cache`.

Ahora seremos capaces de ver los errores que `Laravel/Symphony` tienen para mostrar.

<a id="Problemas_generales"></a>
## Problemas generales 📜️

_Es imposible que esta guía resuelva todos los problemas que pueden surgir durante el deploy, de todas formas, haremos referencia a los más recurrentes y como solucionarlos._

❗️ No te quedes solo con esto, **busca en internet otras posibles soluciones** a los problemas que vayas encontrando ❗️

<a id="Permisos_de_archivos"></a>
#### Permisos de archivos

Es posible que el servidor (apache u otro) nos indique vulnerabilidades como ocurre cuando se detecta que los permisos de los archivos no son correctos. Esto sucede si no se agregaron algunos permisos necesarios o si por lo contrario, están todos los permisos dados.

<a id="Base_de_datos"></a>
#### Base de datos

Existe la posibilidad de que algunas configuraciones en .env sean incorrectas. Revisa los datos de conexión, también si el usuario del hosting tiene los permisos necesarios para modificar la base de datos.

<a id="Configuración_antigua"></a>
#### Configuración antigua

De reportarse algún problema relacionado a configuración es recomendable limpiar el cache de la app como lo hicimos anteriormente.

<a id="Versión_php"></a>
#### Versión php

Es posible que el servidor ejecute una versión antigua de php y que Laravel no soporte, puedes revisar esto en la sección "versión php" del hosting.

<a id="Correos"></a>
#### Correos

La configuración de correos es compleja y sensible, tu proveedor de servicio puede asesorarte en este tema. Asegúrate de ingresar las credenciales correctas en tu .env.

<a id="Otros_errores"></a>
#### Otros errores

_De encontrar **errores relacionados al desarrollo e instrucciones de esta guía**, por favor **[reportarlo como problema (issue)](https://github.com/Guilledll/laravel-a-produccion-por-1-vez/issues)**._

<a id="Verificando_los_cambios"></a>
## Verificando los cambios 🏷️

_Una vez realizados los cambios debemos comprobarlos, pero como mencionamos antes, el cache del navegador ya está guardado, debemos borrarlo._

Con **cada cambio que realizamos en nuestro .env**, debemos borrar el cache generado por nuestra web en el navegador y/o ejecutar `php artisan config:cache`.

> De esta manera almacenamos las nuevas configuraciones.

<a id="Activar_la_web"></a>
## Activar la web 💡️

_Una vez comprobados los cambios y viendo que nuestra web funciona correctamente, es momento de revertir las últimas modificaciones._

Cambiando **.env**

* APP_DEBUG = ```true``` por ```false```

**En terminal web**

```
php artisan config:clear && php artisan config:cache
```

Apagando **Modo mantenimiento**

```
php artisan up
```

Ahora cualquier usuario puede ingresar en nuestra web.

🎉️ **Felicidades! Tu web ya está lista y en línea!** 🎉️

<a id="Extras"></a>
## Extras 

_Has llegado al final de esta guía! De verdad espero que te haya sido de utilidad y que hayas completado la carga de tu proyecto Laravel con éxito!_

<a id="Sugerencias"></a>
### Sugerencias 📮️

_Siéntete libre de realizar cualquier sugerencia o comentario. Esta guía se realiza en base a experiencia y no es perfecta._

> Todas las contribuciones serán tomadas en cuenta, experiencias, comentarios, agregados, etc.

**No olvides compartir con tus compañeros desarrolladores** 💬️

<a id="Autor"></a>
### Autor ✒️

Realizado por Guillermo de León, desarrollador FullStack Laravel + Vue.js, con ganas de compartir conocimiento y experiencia.

_Puedes encontrarme en_

* **[Guilledll](https://github.com/Guilledll)** - GitHub
* **[Simply UY](https://www.youtube.com/channel/UChhbijYNjlgiVuqPEIMXuzQ)** - Youtube
* **[Guillermo de León](https://www.linkedin.com/in/guillermo-de-le%C3%B3n-02a2ba206/)** - LinkedIn

En youtube soy [Simply UY](https://www.youtube.com/channel/UChhbijYNjlgiVuqPEIMXuzQ), subo programación relacionada a Laravel y Vue.js, tecnología y cualquier cosa que valga la pena compartir.

[Volver arriba](#top)

---

⌨️ con ❤️ por [Guilledll](https://github.com/Guilledll)
