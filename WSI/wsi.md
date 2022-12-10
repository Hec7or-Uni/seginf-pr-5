# Weak Session Id

La vulnerabilidad Weak Session Id se produce cuando el servidor no genera un identificador de sesión seguro. Esto permite que un atacante pueda predecir el identificador de sesión y así poder acceder a la sesión de un usuario.

## Bajo

Tras ejecutar 5 peticiones POST mediante el botón de "Generate" de la página, se observa que el identificador de sesión es monótono y se creciente, por lo que se puede intuir que el siguiente identificador de sesión será el 6.

![5 peticiones](/WSI/assets/requests-low.png)

| #    | dvwaSession |
| ---: |    :---:    |
|  50  |      1      |
|  51  |      2      |
|  52  |      3      |
|  53  |      4      |
|  54  |      5      |


### Código vulnerable

```php
 $_SESSION['last_session_id']++; 
```

## Medio

Para analizar como está funcionando la generación de identificadores de sesión, se ha realizado un análisis a través de Burp Suite.

Para empezar, se ha realizado una petición POST mediante el botón `generate` de la página y se ha analizado la respuesta del servidor.
A continuación, como no se puede sacar ninguna conclusión de un único valor, se ha usado el secuenciador de Burp Suit para generar 100 peticiones POST y analizar las respuestas del servidor. 

![secuenciador](/WSI/assets/sequencer-mid.png)

Trás analizar los resultados se ha podido observas que cada segundo incrementa en 1 el identificador de sesión además el identificador de sesión se parece a un timestamp.

|   #  | dvwaSession | [Time GMT]  |
| ---: |    :---:    | ------ |
| 0    | 1669840379  | Wednesday, 30 November 2022 20:32:59| 
| 1    | 1669840379  |        | 
| 2    | 1669840380  | Wednesday, 30 November 2022 20:33:00| 
| 3    | 1669840380  |        | 
| 4    | 1669840381  | Wednesday, 30 November 2022 20:33:01|  
| 5    | 1669840381  |        | 
| 6    | 1669840382  | Wednesday, 30 November 2022 20:33:02| 
| 7    | 1669840382  |        | 
| 8    | 1669840383  | Wednesday, 30 November 2022 20:33:03| 
| 9    | 1669840383  |        | 

### Código vulnerable

```php
$cookie_value = time(); 
```

## Alto

En este nivel, las cookies se ven así: `eccbc87e4b5ce2fe28308fd9f2a7baf3` lo que parece ser un hash md5.

Al igual que en el nivel medio, comenzamos recuperando una lista de IDs de sesión, para ello usamos el secuenciador de Burp Suite para generar varias peticiones POST y analizar las respuestas del servidor. 

![historial](/WSI/assets/history-high.png)

![secuenciador-2](/WSI/assets/sequencer-high.png)

Tras guardar los resultados, se ha utilizado hashcat para descifrar los hashes md5 y como se ha podido comprobar, los hashes siguen un patrón ascendente.

> **Nota:** En la salida de hashcat se ha usado la opción `--show` para mostrar los hashes descifrados previamente.

```bash
┌──(kali㉿kali)-[~/Desktop]
└─$ hashcat -a 3 -m 0 tokens --increment --show
eccbc87e4b5ce2fe28308fd9f2a7baf3:3
a87ff679a2f3e71d9181a67b7542122c:4
e4da3b7fbbce2345d7772b0674a318d5:5
1679091c5a880faf6fb5e6087eb1b2dc:6
8f14e45fceea167a5a36dedd4bea2543:7
c9f0f895fb98ab9159f51fd0297e236d:8
45c48cce2e2d7fbdea1afc51c7c6ad26:9
d3d9446802a44259755d38e6d163e820:10
6512bd43d9caa6e02c990b0a82652dca:11
c20ad4d76fe97759aa27a0c99bff6710:12
```

### Código vulnerable

Como se puede ver en el código del servidor, el valor de la cookie es `md5($_SESSION['last_session_id_high']++;)` como habiamos predicho.

```php
$_SESSION['last_session_id_high']++;
$cookie_value = md5($_SESSION['last_session_id_high']); 
```

## Referencias

- [epochconverter](https://www.epochconverter.com/) Conversor de marcas de tiempo unix para desarrolladores.