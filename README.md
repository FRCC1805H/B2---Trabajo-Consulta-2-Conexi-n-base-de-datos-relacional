#   B2---Trabajo-Consulta-2-Conexi-n-base-de-datos-relacional


### ¿Qué es JDBC y cuáles son sus componentes?




JDBC (Java Database Connectivity) es una API (Application Programming Interface) de Java que permite a las aplicaciones interactuar con bases de datos relacionales. Proporciona un conjunto de clases e interfaces para conectarse a una base de datos, enviar consultas SQL y manejar los resultados. Es una parte del paquete estándar de Java (java.sql) y sirve como puente entre aplicaciones Java y sistemas de gestión de bases de datos (DBMS).

Componentes principales de JDBC
	1.	Driver JDBC
Es un controlador que actúa como intermediario entre la aplicación Java y la base de datos. Traduce las llamadas JDBC en comandos específicos del DBMS. Hay cuatro tipos de drivers JDBC:
	•	Tipo 1: Puente JDBC-ODBC (obsoleto).
	•	Tipo 2: Driver nativo API.
	•	Tipo 3: Driver de red intermedio.
	•	Tipo 4: Driver puro Java (directo al protocolo de la base de datos).
	2.	Connection
Representa la conexión con una base de datos específica. Es el primer paso para realizar cualquier operación con la base de datos. Proporciona métodos para:
	•	Establecer la conexión.
	•	Administrar transacciones (commit, rollback).
	•	Configurar propiedades de conexión.
	3.	Statement
Se utiliza para ejecutar consultas SQL estáticas (que no tienen parámetros). Tiene tres variantes:
	•	Statement: Para ejecutar sentencias SQL simples.
	•	PreparedStatement: Para consultas SQL precompiladas con parámetros.
	•	CallableStatement: Para ejecutar procedimientos almacenados.
	4.	ResultSet
Es el conjunto de resultados obtenido después de ejecutar una consulta SQL. Permite iterar sobre las filas de datos devueltas y proporciona métodos para obtener datos de columnas específicas.
	5.	DriverManager
Administra los controladores JDBC y establece la conexión con la base de datos. Localiza el driver adecuado utilizando la URL de conexión proporcionada por la aplicación.
	6.	DataSource
Es una alternativa moderna al DriverManager para administrar conexiones. Se usa principalmente en aplicaciones empresariales y servidores de aplicaciones para implementar el concepto de connection pooling.
	7.	Excepciones de JDBC (SQLException)
Maneja los errores y excepciones que ocurren durante las operaciones de la base de datos.

Flujo típico de trabajo con JDBC
	1.	Cargar el driver JDBC (aunque en las versiones modernas de Java se hace automáticamente).
	2.	Establecer la conexión con la base de datos mediante DriverManager o DataSource.
	3.	Crear un objeto Statement o PreparedStatement para ejecutar consultas.
	4.	Ejecutar la consulta y procesar los resultados con un ResultSet.
	5.	Cerrar los recursos (ResultSet, Statement, Connection) para liberar memoria.


## Documente 2 librerías de Scala que permitan conectarse a una base de datos relacional. En una tabla resuma sus diferencias.


1. Slick (Scala Language-Integrated Connection Kit)
	•	Descripción: Es un marco de trabajo funcional y tipado para interactuar con bases de datos relacionales. Ofrece un DSL (Domain-Specific Language) que permite escribir consultas SQL directamente en Scala.
	•	Características principales:
	•	Totalmente tipado: Detecta errores en tiempo de compilación.
	•	Soporta consultas funcionales y composables.
	•	Compatible con varias bases de datos (PostgreSQL, MySQL, SQLite, etc.).
	•	Soporte para programación asíncrona y funcional.

2. Doobie
	•	Descripción: Es una librería funcional pura que facilita la interacción con bases de datos JDBC. Está diseñada para integrarse con bibliotecas funcionales como Cats y ZIO.
	•	Características principales:
	•	Basada en la programación funcional pura.
	•	Integración profunda con Cats Effect para manejo de efectos.
	•	Proporciona un fuerte control sobre el mapeo y manejo de errores.
	•	Compatible con bases de datos relacionales a través de JDBC.

Comparación de Slick y Doobie

Aspecto	Slick	Doobie
Paradigma	Orientado a DSL funcional	Programación funcional pura
Soporte tipado	Sí, consultas DSL con tipado en tiempo de compilación	Sí, consultas manuales con tipado fuerte
Manejo de efectos	Opcionalmente asíncrono con Futures	Basado en Cats Effect (IO)
Composición de consultas	Muy funcional y composable	Composición explícita usando Cats
Integración con JDBC	Oculta los detalles de JDBC	Requiere interacción más explícita con JDBC
Facilidad de uso	Más amigable para principiantes	Requiere conocimientos avanzados de FP
Rendimiento	Optimizado para grandes volúmenes	Control explícito permite optimización manual
Bases de datos soportadas	PostgreSQL, MySQL, SQLite, etc.	Cualquier base de datos con un driver JDBC



 ![image](https://github.com/user-attachments/assets/f7b0805e-afba-495b-8231-adaeb28fd258)


Documentar cómo establecer una conexión a una base de datos relacional (mysql). Siga los siguientes pasos:
Genere una base de datos en mysql
Genere una tabla con datos de prueba
Desde Scala establezca la conexión a la base datos
(opcional) Desde Scala realice la consulta de todos los datos de la tabla de prueba.




-- Crear la base de datos
CREATE DATABASE ejemplo_db;

-- Usar la base de datos
USE ejemplo_db;

-- Crear una tabla de prueba
CREATE TABLE empleados (
    id INT AUTO_INCREMENT PRIMARY KEY,
    nombre VARCHAR(100),
    puesto VARCHAR(50),
    salario DECIMAL(10, 2)
);

-- Insertar datos de prueba
INSERT INTO empleados (nombre, puesto, salario) VALUES
('Ana Pérez', 'Desarrolladora', 45000.00),
('Luis Gómez', 'Analista', 40000.00),
('Clara López', 'Gerente', 60000.00);





// build.sbt
lazy val root = (project in file("."))
  .settings(
    name := "ConexionScalaMySQL",
    version := "1.0",
    scalaVersion := "2.13.12"
  )

// Dependencias
libraryDependencies ++= Seq(
  "mysql" % "mysql-connector-java" % "8.0.34",   // Driver JDBC de MySQL
  "com.typesafe.slick" %% "slick" % "3.4.1",     // Librería Slick
  "com.typesafe.slick" %% "slick-hikaricp" % "3.4.1" // HikariCP para pooling
)




import slick.jdbc.MySQLProfile.api._

import scala.concurrent.Await
import scala.concurrent.duration._

object ConexionMySQL {
  def main(args: Array[String]): Unit = {
    // URL de conexión, usuario y contraseña
    val dbUrl = "jdbc:mysql://localhost:3306/ejemplo_db"
    val user = "root"         // Cambia por tu usuario
    val password = "password" // Cambia por tu contraseña

    // Establecer conexión con la base de datos usando Slick
    val db = Database.forURL(
      url = dbUrl,
      user = user,
      password = password,
      driver = "com.mysql.cj.jdbc.Driver"
    )

    try {
      // Definir la consulta para obtener todos los datos de la tabla 'empleados'
      val consulta = sql"SELECT * FROM empleados".as[(Int, String, String, BigDecimal)]

      // Ejecutar la consulta
      val resultado = Await.result(db.run(consulta), 10.seconds)

      // Imprimir los datos obtenidos
      println("Datos de la tabla 'empleados':")
      resultado.foreach { case (id, nombre, puesto, salario) =>
        println(s"ID: $id, Nombre: $nombre, Puesto: $puesto, Salario: $salario")
      }
    } catch {
      case ex: Exception =>
        ex.printStackTrace()
    } finally {
      // Cerrar la conexión a la base de datos
      db.close()
    }
  }
}


import java.sql.{Connection, DriverManager, ResultSet}

object ConexionMySQLJDBC {
  def main(args: Array[String]): Unit = {
    val url = "jdbc:mysql://localhost:3306/ejemplo_db"
    val user = "root"         // Cambia por tu usuario
    val password = "password" // Cambia por tu contraseña

    var connection: Connection = null

    try {
      // Establecer la conexión
      connection = DriverManager.getConnection(url, user, password)
      println("Conexión establecida con éxito.")

      // Crear una consulta y ejecutarla
      val statement = connection.createStatement()
      val resultSet: ResultSet = statement.executeQuery("SELECT * FROM empleados")

      // Imprimir los resultados
      println("Datos de la tabla 'empleados':")
      while (resultSet.next()) {
        val id = resultSet.getInt("id")
        val nombre = resultSet.getString("nombre")
        val puesto = resultSet.getString("puesto")
        val salario = resultSet.getBigDecimal("salario")
        println(s"ID: $id, Nombre: $nombre, Puesto: $puesto, Salario: $salario")
      }
    } catch {
      case ex: Exception => ex.printStackTrace()
    } finally {
      // Cerrar la conexión
      if (connection != null) connection.close()
    }
  }
}
