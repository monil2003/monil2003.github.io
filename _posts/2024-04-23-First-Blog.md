
# Dockerized Multi-Architecture Web Application (CLOUD IA)

This is a Dockerized web application project consisting of three main components:

1. **Frontend**: Apache HTTP Server serving static files.
2. **Backend**: Custom application backend built using a Dockerfile.
3. **Database**: MySQL database accessed via phpMyAdmin.

## Prerequisites

Before you begin, ensure you have the following installed on your system:

- Docker: [Installation guide](https://docs.docker.com/get-docker/)
- Docker Compose: [Installation guide](https://docs.docker.com/compose/install/)

## Getting Started

1. Clone the repository to your local machine:  
   Repo: [monil2003/Cloud_IA](https://github.com/monil2003/Cloud_IA)
   

    ```bash
    git clone https://github.com/your_username/your_repository.git
    cd your_repository
    ```

2. Place the provided `docker-compose.yml` and `Dockerfile` files in the root directory of the project.

3. Build and start the Docker containers:

    ```bash
    docker-compose up --build
    ```

4. Access the application in your web browser:

    - Frontend: [http://localhost:3000](http://localhost:3000)
    - Backend: [http://localhost:5000](http://localhost:5000)
    - phpMyAdmin: [http://localhost:8080](http://localhost:8080)

## Complete Implementation

# Docker Installation

To check for Docker installation on your system, run the following commands in your terminal (Linux/MacOS) or PowerShell (Windows):

```bash
$ docker version
$ docker compose version
```

Docker is a platform for managing containers, while Docker Compose simplifies the management of multi-container applications. Docker Compose is included with Docker installation.

## A Simple 3-Tier Architecture

Our goal is to build a basic 3-tier architecture using Docker containers, consisting of:

- Frontend container
- Backend container (with an API layer)
- MySQL database container
- PHPMyAdmin for database management

### Frontend App Container

To start, create a new folder for your main project (e.g., docker-tutorial). Inside this folder, create a frontend folder for the frontend application. Add a simple `index.html` file in the frontend folder:

```html
<!-- index.html -->
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>HTML Web Page</title>
  </head>
  <body>
    <h2>Roll No 21BCP217 says hello</h2>
    <h1>Subjects of Semester 6:</h1>
        <div id="todos"></div>

    <script>
      fetch("http://localhost:5000/")
        .then((response) => response.json())
        .then((data) => {
          const todos = document.getElementById("todos");

          data?.forEach((item, index) => {
            const todo = document.createElement("h2");
            todo.textContent = item._id + ". " + item.todo;
            todos.appendChild(todo);
          });
        })
        .catch((error) => {
          console.log("Error: ", error);
        });
    </script>
  </body>
</html>
```

Next, create a `docker-compose.yml` file in the main project directory:

```yaml
version: "3"
services:
  frontend:
    frontend:
    image: httpd:latest
    container_name: frontend_21BCP217
    volumes:
      - "./frontend:/usr/local/apache2/htdocs"
    ports:
      - 3000:80
```

### Backend API Container

Create a new folder named `api` in the main project directory. Inside the `api` folder, create a PHP file named `index.php`. Add a simple todos API endpoint in `index.php`.

```php
// index.php
<?php
// Set the response content type to JSON
header('Content-Type: application/json');
header('Access-Control-Allow-Origin: http://localhost:3000');
header('Access-Control-Allow-Methods: GET, POST, OPTIONS');

require "./app/config.php";
require_once "./app/todos.php";

$todoModel = new Todo();
$response = $todoModel->getTodos(10);
// Create a response array

// Encode the response array as JSON
$jsonResponse = json_encode($response);

// Output the JSON response
echo $jsonResponse;
```

Create a `Dockerfile` in the main project directory to build the backend API container:

```Dockerfile
# Dockerfile
FROM ubuntu:20.04

# Install necessary packages
# Configure PHP and Apache
# Expose port 80
# Start Apache
```

Update `docker-compose.yml` to include the backend service:

```yaml
version: "3"
services:
  frontend:
    # Frontend service configuration...

  backend:
    container_name: backend_21BCP217
    build:
      context: ./
      dockerfile: Dockerfile
    volumes:
      - "./api:/var/www/html/"
    ports:
      - 5000:80
```

Create a new folder named app in api. Add 3 files in it.

```php
// config.php
<?php 
define("DB_HOST", "database");
define("DB_USERNAME", "todo_admin");
define("DB_PASSWORD", "password");
define("DB_NAME", "todo_app");
```

```php
// Database.php
<?php
class Database
{
    protected $connection = null;
    
    public function __construct()
    {
        try {
            $this->connection = new mysqli(DB_HOST, DB_USERNAME, DB_PASSWORD, DB_NAME);
            if (mysqli_connect_errno()) {
                throw new Exception("Database connection failed!");
            }
        } catch (Exception $e) {
            throw new Exception($e->getMessage());
        }
    }

    private function executeStatement($query = "", $params = [])
    {
        try {
            $stmt = $this->connection->prepare($query);
            if ($stmt === false) {
                throw new Exception("Statement preparation failure: " . $query);
            }
            if ($params) {
                $stmt->bind_param($params[0], $params[1]);
            }
            $stmt->execute();
            return $stmt;
        } catch (Exception $e) {
            throw new Exception($e->getMessage());
        }
    }

    public function select($query = "", $params = [])
    {
        try {
            $stmt = $this->executeStatement($query, $params);
            $result = $stmt->get_result()->fetch_all(MYSQLI_ASSOC);
            $stmt->close();
            return $result;
        } catch (Exception $e) {
            throw new Exception($e->getMessage());
        }
        return false;
    }
}
```

```php
// todos.php
<?php 
require_once "./app/Database.php";

class Todo extends Database
{
    public function getTodos($limit)
    {
        return $this->select("SELECT * FROM todos");
    }
}
```

### MySQL Database Container

Add a SQL dump file named `dump.sql` in a `db` folder in the main project directory.

```php
// dump.sql
-- phpMyAdmin SQL Dump
-- version 5.2.1
-- https://www.phpmyadmin.net/
--
-- Host: database:3306
-- Generation Time: Jul 08, 2023 at 05:46 PM
-- Server version: 8.0.33
-- PHP Version: 8.1.17

SET SQL_MODE = "NO_AUTO_VALUE_ON_ZERO";
START TRANSACTION;
SET time_zone = "+00:00";


/*!40101 SET @OLD_CHARACTER_SET_CLIENT=@@CHARACTER_SET_CLIENT */;
/*!40101 SET @OLD_CHARACTER_SET_RESULTS=@@CHARACTER_SET_RESULTS */;
/*!40101 SET @OLD_COLLATION_CONNECTION=@@COLLATION_CONNECTION */;
/*!40101 SET NAMES utf8mb4 */;

--
-- Database: `todo_app`
--

-- --------------------------------------------------------

--
-- Table structure for table `todos`
--

CREATE TABLE `todos` (
  `_id` int NOT NULL,
  `todo` varchar(255) NOT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci;

--
-- Dumping data for table `todos`
--

INSERT INTO `todos` (`_id`, `todo`) VALUES
(1, 'Cloud Computing'),
(2, 'Artificial Intelligence'),
(3, 'Big Data Analytics'),
(4, 'Advanced Web Tech'),
(5, 'Communication Skills');

--
-- Indexes for dumped tables
--

--
-- Indexes for table `todos`
--
ALTER TABLE `todos`
  ADD PRIMARY KEY (`_id`);

--
-- AUTO_INCREMENT for dumped tables
--

--
-- AUTO_INCREMENT for table `todos`
--
ALTER TABLE `todos`
  MODIFY `_id` int NOT NULL AUTO_INCREMENT, AUTO_INCREMENT=6;
COMMIT;
```

Create the MySQL database container configuration in `docker-compose.yml` along with phpmyadmin management:

```yaml
version: "3"
services:
  frontend:
    # Frontend service configuration...

  backend:
    # Backend service configuration...

  database:
    image: mysql:latest
    container_name: database_21BCP217
    environment:
      MYSQL_DATABASE: todo_app
      MYSQL_USER: todo_admin
      MYSQL_PASSWORD: password
      MYSQL_ALLOW_EMPTY_PASSWORD: 1
    volumes:
      - "./db:/docker-entrypoint-initdb.d"

  phpmyadmin:
    image: phpmyadmin/phpmyadmin
    container_name: phpmyadmin_21BCP217
    ports:
      - 8080:80
    environment:
      - PMA_HOST=database
      - PMA_PORT=3306
    depends_on:
      - database
```
## Outcomes

Upon completion of the steps outlined above, your directory structure will resemble the following:

![Directory](assets/images/directory.png)

### Setting Up Containers

To set up containers and run them, execute the following command:

```bash
docker-compose up
```
![compose](assets/images/compose.png)

Containers Running:

![containers](assets/images/containers.png)
![cont_docker](assets/images/cont_docker.png)

### Frontend

![frontend](assets/images/frontend.png)

### Backend

![backend](assets/images/backend.png)

### Database

![database](assets/images/database.png)

## Configuration

You can customize the configuration of each component by modifying the respective configuration files (`frontend/index.html` for frontend, `Dockerfile` for backend, `docker-compose.yml` for services configuration or go to phpmyadmin for database managaement).

## Summary

This tutorial covered the basics of Docker and Docker Compose, demonstrating how to build a simple 3-tier architecture using containers. By leveraging Docker and Docker Compose, you can easily manage multi-container applications.


## License

This project is licensed under the [MIT License](https://www.mit.edu/~amini/LICENSE.md).

## Acknowledgements

- [Docker](https://www.docker.com/) - For containerization technology.
- [Apache HTTP Server](https://httpd.apache.org/) - For serving the frontend.
- [MySQL](https://www.mysql.com/) - For the database management system.
- [phpMyAdmin](https://www.phpmyadmin.net/) - For database administration.
- [Building a Simple 3-Tier Architecture with Docker Compose: A Hands-On Project Journey](https://medium.com/@kesaralive/getting-started-with-docker-compose-hands-on-project-experience-e562ab07e24c) - For reference