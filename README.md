# servidor_web
 
 🚀 Servidor LAMP en Raspberry Pi  
 Montaje de un servidor web con **Apache2**, **PHP** y **MariaDB** para servir datos a Unity u otras aplicaciones.

 ---

 ## 📌 1. Instalación de Apache

 ---
sudo apt update
sudo apt install apache2 -y

Verificar que funciona:

para ver la ip usa el comando ifconfig

http://<IP-de-la-pi> → aparece la página de bienvenida de Apache.

 ##📌 2. Configuración del directorio web

Ruta por defecto: /var/www/html/

Dar permisos al usuario actual:

sudo chown -R $USER:$USER /var/www/html

##📌 3. Instalación de PHP

sudo apt install php libapache2-mod-php -y
sudo systemctl restart apache2

Probar PHP:
Abrir en navegador: http://<IP-de-la-pi>/info.php

##📌 4. Instalación de MariaDB

sudo apt install mariadb-server php-mysql -y

Asegurar instalación:

sudo mysql_secure_installation

##📌 5. Crear base de datos y usuario

Entrar como root:

sudo mysql -u root -p

Ejecutar:

CREATE DATABASE miweb;
CREATE USER 'wigfloo'@'localhost' IDENTIFIED BY '1096185682';
GRANT ALL PRIVILEGES ON miweb.* TO 'wigfloo'@'localhost';
FLUSH PRIVILEGES;

Probar conexión:

mysql -u wigfloo -p miweb

##📌 6. Crear tabla de prueba

USE miweb;

CREATE TABLE usuarios (
  id INT AUTO_INCREMENT PRIMARY KEY,
  nombre VARCHAR(50),
  puntuacion INT
);

INSERT INTO usuarios (nombre, puntuacion)
VALUES ('George', 100), ('Ana', 150), ('Carlos', 200);

##📌 7. Endpoint en PHP

Archivo /var/www/html/getUsuarios.php:

<?php
error_reporting(E_ALL);
ini_set('display_errors', 1);

$servername = "localhost";
$username = "wigfloo";
$password = "1096185682";
$dbname = "miweb";

$conn = new mysqli($servername, $username, $password, $dbname);

if ($conn->connect_error) {
    die("❌ Conexión fallida: " . $conn->connect_error);
}

$sql = "SELECT id, nombre, puntuacion FROM usuarios";
$result = $conn->query($sql);

$usuarios = array();
while($row = $result->fetch_assoc()) {
    $usuarios[] = $row;
}

header('Content-Type: application/json');
echo json_encode($usuarios);

$conn->close();
?>

Probar en navegador:
http://<IP-de-la-pi>/getUsuarios.php

Salida esperada:

[
  {"id":1,"nombre":"George","puntuacion":100},
  {"id":2,"nombre":"Ana","puntuacion":150},
  {"id":3,"nombre":"Carlos","puntuacion":200}
]


