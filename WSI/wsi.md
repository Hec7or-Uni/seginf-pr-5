# Weak Session Id

La vulnerabilidad Weak Session Id se produce cuando el servidor no genera un identificador de sesión seguro. Esto permite que un atacante pueda predecir el identificador de sesión y así poder acceder a la sesión de un usuario.

## Bajo

Tras ejecutar 5 peticiones POST mediante el botón de "Generate" de la página, se observa que el identificador de sesión es monotono y se creciente. Por lo que se puede asegurar que depués de la petición 5 el identificador de la siguiente sesión será 6.

![5 peticiones](https://github.com/Hec7or-Uni/seginf-pr-5/blob/main/WSI/assets/requests-1.png)

| #    | dvwaSession |
| ---: |    :---:    |
|  50  |      1      | 
|  51  |      2      | 
|  52  |      3      | 
|  53  |      4      | 
|  54  |      5      |  

## Medio

Para analizar como esta funcionando la generación de identificadores de sesión, se ha realizado un análisis a través de Burp Suite.
Para empezar, se ha realizado una petición POST mediante el boton `generate` de la página y se ha analizado la respuesta del servidor.
A continuación, como no se puede sacar ninguna conclusión de un único valor, se ha usado el secuenciador de Burp Suit para generar 100 peticiones POST y analizar las respuestas del servidor. 

![secuenciador](https://github.com/Hec7or-Uni/seginf-pr-5/blob/main/WSI/assets/secuenciador.png)

Trás analizar los resultados se ha podido observas que cada segundo incrementa en 1 el identificador de sesión además el identificador de sesión se parece a un timestamp.

|   #  | dvwaSession | Time GMT  |
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

## Alto