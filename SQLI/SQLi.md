# SQLi

Se ha utilizado la herramienta SQLmap para realizar el ataque de inyección SQL y Burp Suite para interceptar
el tráfico entre el cliente y el servidor.

## Bajo
En este nivel, la vulnerabilidad se encuentra claramente en la siguiente línea, que
permite introducir entradas maliciosas manipulando la cadena de consulta SQL:

```php
// Check database
$query  = "SELECT first_name, last_name FROM users WHERE user_id = '$id';"
```

En este nivel se intentó inicialmente introducir entradas maliciosas manualmente.
La siguiente entrada permite obtener la contraseña (aún encriptada):
```
' UNION SELECT first_name,password FROM users;-- -&Submit=Submit
```

El objetivo de la cadena maliciosa es doble:

- En primer lugar, se escapa la consulta inicial y se fusiona con otra consulta que permite obtener la contraseña de todos los usuarios. Cabe destacar que sólo es posible incluir 2 columnas, ya que en caso contrario la petición HTTP falla.
- En segundo lugar, se debe escapar la consulta SQL, comentando el resto de la cadena, para así poder realizar la petición.

## Medio
Ahora la petición se realiza mediante un formulario, por lo que SQLmap es una herramienta ideal en este apartado, aunque se podría hacer de manera manual con la misma consulta ligeramente transformada, pasándola por el formulario de POST, ya que el nivel medio no parchea totalmente la vulnerabilidad (hace uso de la función `mysql_real_escape_string()`).

## Alto

## Decodificación de las contraseñas