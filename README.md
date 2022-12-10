# DVWA

## Brute Force 
El objetivo es conseguir la contrase~na (en claro) del administrador (y de los otros usuarios del sistema también) con un ataque de fuerza bruta.

## [Command Injection](/CI/CI.md)
El objetivo es averiguar todos los usuarios del sistema, y además todos los nombres de dominio y correspondientes direcciones IP.

## CSRF. 
El objetivo es cambiar la contrase~na del usuario admin sin su consentimiento. El atacante explota exclusivamente la vulnerabilidad CSRF para conseguir su objetivo como se ha explicado en clase.

## [File Inclusion](/FI/FI.md) 
El objetivo es conseguir leer el contenido de un fichero (el fichero almacenado en local ../hackable/flags/fi.php) que no está disponible directamente como enlace en la página web.

## [File Upload](/FU/FU.md)
El objetivo es conseguir ejecutar una cualquier función PHP de elección. 

## Insecure CAPTCHA 
El objetivo es cambiar la contrase~na del administrador de forma automatizada.

## SQLi. 
El objetivo es robar las contrase~nas de todos los usuarios del DBMS.
SQLi blind. El objetivo es encontrar la versión del servidor DBMS.

## [Weak Session IDs](/WSI/WSI.md)
El objetivo es averiguar cómo la aplicación genera las cookies de
sesión y luego inferir las cookies de sesión de los otros usuarios de la aplicación.

## XSS (DOM) El objetivo es conseguir robar la cookie de sesión de un usuario (que haya
iniciado la sesión) ejecutando un propio JavaScript en el navegador de otro usuario.

## XSS (Reected). 
El objetivo es robar las cookies de sesión de un usuario.

## XSS (Persistent). 
El objetivo es redirigir cualquier usuario que visite la página web a otra página de libre elección.

## CSP Bypass
El objetivo es evitar la política de seguridad de contenido implementada por la aplicación y conseguir que el navegador ejecute un código JavaScript de libre elección.

## Javascript
El objetivo es evitar el mecanismo de protección implementado para ganar el desafío, introduciendo la palabra indicada.
