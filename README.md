# Tarea API REST básica con PHP

Las API REST son esenciales para permitir la comunicación entre diferentes aplicaciones y servicios en la web. Haremos una API REST básica que realiza operaciones CRUD (Crear, Leer, Actualizar, Eliminar) en una tabla de usuarios.

## Requisitos previos

- PHP instalado en tu servidor o máquina local.
- Servidor web como Apache o Nginx.
- MySQL o cualquier otro sistema de gestión de bases de datos.
- Conocimientos básicos de PHP y SQL.

## Configuración de la base de datos

Primero, crea una base de datos llamada `api_db` y una tabla `users`:

```sql
CREATE DATABASE api_db;

USE api_db;

CREATE TABLE users (
  id INT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(100) NOT NULL,
  email VARCHAR(100) NOT NULL UNIQUE
);
```

## Conexión a la base de datos

Crea un archivo `database.php` para manejar la conexión:

```php
<?php
class Database {
  private $host = "localhost";
  private $db_name = "api_db";
  private $username = "tu_usuario";
  private $password = "tu_contraseña";
  public $conn;

  public function getConnection() {
    $this->conn = null;
    try {
      $this->conn = new PDO(
        "mysql:host=" . $this->host . ";dbname=" . $this->db_name,
        $this->username,
        $this->password
      );
    } catch(PDOException $exception) {
      echo "Error de conexión: " . $exception->getMessage();
    }
    return $this->conn;
  }
}
?>
```

Asegúrate de reemplazar `tu_usuario` y `tu_contraseña` con tus credenciales de MySQL.

## Creación de la API

Crea un archivo `api.php` que manejará las solicitudes HTTP:

```php
<?php
header("Content-Type: application/json; charset=UTF-8");

include_once 'database.php';

$database = new Database();
$db = $database->getConnection();

$method = $_SERVER['REQUEST_METHOD'];

switch ($method) {
  case 'GET':
    // Código para manejar GET
    break;
  case 'POST':
    // Código para manejar POST
    break;
  case 'PUT':
    // Código para manejar PUT
    break;
  case 'DELETE':
    // Código para manejar DELETE
    break;
  default:
    echo json_encode(['mensaje' => 'Método no soportado']);
    break;
}
?>
```

### Manejo de solicitudes GET

```php
case 'GET':
  $sql = "SELECT * FROM users";
  $stmt = $db->prepare($sql);
  $stmt->execute();
  $users = $stmt->fetchAll(PDO::FETCH_ASSOC);
  echo json_encode($users);
  break;
```

### Manejo de solicitudes POST

```php
case 'POST':
  $data = json_decode(file_get_contents('php://input'), true);
  $sql = "INSERT INTO users (name, email) VALUES (:name, :email)";
  $stmt = $db->prepare($sql);
  $stmt->bindParam(':name', $data['name']);
  $stmt->bindParam(':email', $data['email']);
  if($stmt->execute()) {
    echo json_encode(['mensaje' => 'Usuario creado']);
  } else {
    echo json_encode(['mensaje' => 'Error al crear usuario']);
  }
  break;
```

### Manejo de solicitudes PUT

```php
case 'PUT':
  $data = json_decode(file_get_contents('php://input'), true);
  $sql = "UPDATE users SET name = :name, email = :email WHERE id = :id";
  $stmt = $db->prepare($sql);
  $stmt->bindParam(':id', $data['id']);
  $stmt->bindParam(':name', $data['name']);
  $stmt->bindParam(':email', $data['email']);
  if($stmt->execute()) {
    echo json_encode(['mensaje' => 'Usuario actualizado']);
  } else {
    echo json_encode(['mensaje' => 'Error al actualizar usuario']);
  }
  break;
```

### Manejo de solicitudes DELETE

```php
case 'DELETE':
  $id = $_GET['id'];
  $sql = "DELETE FROM users WHERE id = :id";
  $stmt = $db->prepare($sql);
  $stmt->bindParam(':id', $id);
  if($stmt->execute()) {
    echo json_encode(['mensaje' => 'Usuario eliminado']);
  } else {
    echo json_encode(['mensaje' => 'Error al eliminar usuario']);
  }
  break;
```

## Probando la API

Utiliza herramientas como **Postman** o **cURL** para probar tu API, preferiblemente **Postman**.

### Solicitud GET

```bash
curl -X GET http://localhost/api.php
```

### Solicitud POST

```bash
curl -X POST -H "Content-Type: application/json" -d '{"name":"Ana","email":"ana@example.com"}' http://localhost/api.php
```

### Solicitud PUT

```bash
curl -X PUT -H "Content-Type: application/json" -d '{"id":1,"name":"Ana Pérez","email":"anap@example.com"}' http://localhost/api.php
```

### Solicitud DELETE

```bash
curl -X DELETE http://localhost/api.php?id=1
```
## Entrega vía github classroom
