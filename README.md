# LAMP EN MANJARO
 GUÍA DE INSTALACIÓN DE LAMP EN MANJARO (ARCH LINUX)

Instalación Linux Apache MySQL PHP (LAMP) en Manjaro (Archlinux)


## Actualizar el sistema desde terminal
```
sudo pacman -Syy
```

## Instalación de Apache
Para instalar Apache desde la terminal:

```
sudo pacman -S apache
```

## Habilitar, inicializar y configurar el inicio automático de Apache:
Habilitar el servicio de Apache
```
sudo systemctl enable httpd
```
Inicializar el servicio de Apache
```
sudo systemctl start httpd
```

## Creemos una página de prueba:

```
sudo nano /srv/http/index.html
```

Pegar el siguiente código

```
<html>
    <title>Manjaro</title>
    <body>
        <h2>Apache on Manjaro</h2>
    </body>
 </html>
```

## Instalación de MySQL (mariadb)
Para instalar MySQL desde la terminal, emita este comando:
```
sudo pacman -Syu mariadb mariadb-clients libmariadbclient
```
Instale la ruta de la flecha mariadb:
```
sudo mysql_install_db --user=mysql --basedir=/usr --datadir=/var/lib/mysql
```

## Habilitar, inicializar y configurar el inicio automático de MySQL:
Habilitar el servicio de MySQL
```
sudo systemctl enable mysqld.service
```
Inicializar el servicio de MySQL
```
sudo systemctl start mysqld.service
```

## Asegurar MySQL
Al ejecutar este comando, se abrirá un script interactivo que le guiará a través de una serie de pasos para realizar varias acciones de seguridad.
```
sudo mysql_secure_installation
```
Aparecerá lo siguiente, se le harán algunas preguntas que podrá responder según sus preferencias.
```
/usr/bin/mysql_secure_installation: Deprecated program name. It will be removed in a future release, use 'mariadb-secure-installation' instead

NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MariaDB
      SERVERS IN PRODUCTION USE!  PLEASE READ EACH STEP CAREFULLY!

In order to log into MariaDB to secure it, we'll need the current
password for the root user. If you've just installed MariaDB, and
haven't set the root password yet, you should just press enter here.

Enter current password for root (enter for none): 
OK, successfully used password, moving on...

Setting the root password or using the unix_socket ensures that nobody
can log into the MariaDB root user without the proper authorisation.

You already have your root account protected, so you can safely answer 'n'.

Switch to unix_socket authentication [Y/n] n
 ... skipping.

You already have your root account protected, so you can safely answer 'n'.

Change the root password? [Y/n] n
 ... skipping.

By default, a MariaDB installation has an anonymous user, allowing anyone
to log into MariaDB without having to have a user account created for
them.  This is intended only for testing, and to make the installation
go a bit smoother.  You should remove them before moving into a
production environment.

Remove anonymous users? [Y/n] y
 ... Success!

Normally, root should only be allowed to connect from 'localhost'.  This
ensures that someone cannot guess at the root password from the network.

Disallow root login remotely? [Y/n] n
 ... skipping.

By default, MariaDB comes with a database named 'test' that anyone can
access.  This is also intended only for testing, and should be removed
before moving into a production environment.

Remove test database and access to it? [Y/n] y
 - Dropping test database...
 ... Success!
 - Removing privileges on test database...
 ... Success!

Reloading the privilege tables will ensure that all changes made so far
will take effect immediately.

Reload privilege tables now? [Y/n] y
 ... Success!

Cleaning up...

All done!  If you've completed all of the above steps, your MariaDB
installation should now be secure.

Thanks for using MariaDB!
```

## Reiniciar MySQL:
```
sudo systemctl restart mysqld
```

## Instalación de PHP:
Para instalar php, escriba el siguiente comando:
```
sudo pacman -S php php-apache
```

### Abra el siguiente archivo:
```
sudo nano /etc/httpd/conf/httpd.conf
```
Comentar esta línea:
```
LoadModule mpm_event_module modules/mod_mpm_event.so
```
Descomentar esta línea:
```
#LoadModule mpm_prefork_module modules/mod_mpm_prefork.so
```
Agregue este código al final del archivo:
```
LoadModule php_module modules/libphp.so
 AddHandler php-script php
 Include conf/extra/php_module.conf
```
Guarde y cierre el archivo.

Creemos un archivo phpinfo:
```
sudo nano /srv/http/info.php
```
Agrega las siguientes líneas:
```
<?php
 phpinfo();
 ?>
```
### Manejador de errores
Abra el archivo ```/etc/php/php.ini``` y modifique ```display_errors = Off``` por:
```
display_errors = On
```
Reiniciemos Apache:
```
sudo systemctl restart httpd
```
Conéctese a su dirección IP pública o localhost:
```
http://localhost/
```
Script PHP que proporciona información sobre la configuración de PHP y el servidor web
```
http://localhost/info.php
```

## Crear una cuenta de administrador
Entramos a mariadb con:
```
sudo mariadb
```
Entramos a la consola mysql y ponemos
```
GRANT ALL ON *.* TO 'miusuarioAdmin'@'localhost' IDENTIFIED BY 'mipassword' WITH GRANT OPTION;
```
```
flush privileges;
```
```
exit;
```
Entramos a mariadb con nuestra credencial
```
mysql -u miusuarioAdmin -p 
```

## Instalar PhpMyAdmin
```
sudo pacman -S phpmyadmin
```
Abra el archivo ```/etc/php/php.ini``` y descomente las líneas ```extension=mysqli```, ```extension=pdo_mysql``` y ```extension=iconv``` eliminando el punto y coma (;) que las precede.
```
extension=iconv 
;extension=intl
;extension=ldap
extension=mysqli
;extension=odbc
;zend_extension=opcache
;extension=pdo_dblib
extension=pdo_mysql
;extension=pdo_odbc
```
Ahora configure Apache para que funcione con phpmyadmin creando el archivo de configuración principal de phpmyadmin.
```
sudo nano /etc/httpd/conf/extra/phpmyadmin.conf
```
Agrega las siguientes líneas:
```
Alias /phpmyadmin "/usr/share/webapps/phpMyAdmin"
<Directory "/usr/share/webapps/phpMyAdmin">
	DirectoryIndex  index.php
    AllowOverride All
    Options FollowSymlinks
    Require all granted
</Directory>
```
A continuación, incluye la ruta a este archivo de configuración en el archivo de configuración principal de Apache.
```
sudo nano /etc/httpd/conf/httpd.conf
```
Agregue la siguiente línea en la parte inferior.
```
Include conf/extra/phpmyadmin.conf
```
Finalmente, reinicie el servicio web Apache.
```
sudo systemctl restart httpd
```
Ahora acceda a PhpMyAdmin desde el navegador.
```
http://localhost/phpmyadmin
```