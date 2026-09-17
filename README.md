# Lab 1 v20262 — Sistema bancario

Aplicación web para administrar clientes bancarios y registrar transferencias entre cuentas. El proyecto expone una API REST con Spring Boot, persiste la información en MySQL y proporciona una interfaz web estática para gestionar clientes, realizar transferencias y consultar el historial de transacciones.

## Stack

- **Lenguaje:** Java 17
- **Framework / runtime:** Spring Boot 3.5.16
- **Persistencia:** Spring Data JPA, Hibernate y MySQL
- **API:** Spring Web REST
- **Frontend:** HTML, CSS y JavaScript vanilla
- **Mapeo y utilidades:** MapStruct y Lombok
- **Pruebas:** JUnit 5

## Funcionalidades

### Clientes

- Crear clientes.
- Consultar un cliente por ID.
- Listar todos los clientes.
- Actualizar clientes.
- Eliminar clientes.
- Administrar número de cuenta, nombre, apellido y balance.

### Transacciones

- Transferir dinero entre dos cuentas.
- Validar que las cuentas de origen y destino existan.
- Validar que la cuenta de origen tenga saldo suficiente.
- Consultar transacciones asociadas a una cuenta.
- Actualizar transacciones.
- Eliminar transacciones.

### Interfaz web

La interfaz incluye tres secciones principales:

- **Clientes:** operaciones CRUD y listado de clientes.
- **Transacciones:** creación, actualización y eliminación de transferencias.
- **Historial:** consulta de transacciones por número de cuenta o ID de cliente.

## Estructura del proyecto

```text
.
├── pom.xml
├── mvnw
├── mvnw.cmd
└── src/
    └── main/
        ├── java/com/udea/lab1v20262/
        │   ├── Lab1v20262Application.java
        │   ├── DTO/
        │   │   ├── CustomerDTO.java
        │   │   ├── TransactionDTO.java
        │   │   └── TransactionRequestDTO.java
        │   ├── controller/
        │   │   ├── CustomerController.java
        │   │   └── TransactionController.java
        │   ├── entity/
        │   │   ├── Customer.java
        │   │   └── Transaction.java
        │   ├── mapper/
        │   │   ├── CustomerMapper.java
        │   │   └── TransactionMapper.java
        │   ├── repository/
        │   │   ├── CustomerRepository.java
        │   │   └── TransactionRepository.java
        │   └── service/
        │       ├── CustomerService.java
        │       └── TransactionService.java
        └── resources/
            ├── application.properties
            └── static/
                ├── frontend.html
                └── styles.css
```

## Cómo funciona

`Lab1v20262Application` inicia la aplicación Spring Boot. Las solicitudes HTTP son atendidas por `CustomerController` y `TransactionController`, que delegan la lógica de negocio en `CustomerService` y `TransactionService`.

Los servicios utilizan los repositorios JPA para consultar y modificar las entidades `Customer` y `Transaction` en MySQL. Al realizar una transferencia, `TransactionService` valida ambas cuentas, verifica el saldo disponible, actualiza los balances y registra la transacción.

La interfaz ubicada en `src/main/resources/static/frontend.html` consume la API REST mediante `fetch`.

## Requisitos

- Java 17 o superior.
- MySQL 8 o compatible.
- Maven, o utilizar los scripts incluidos `mvnw` / `mvnw.cmd`.

## Configuración de la base de datos

Crea la base de datos en MySQL:

```sql
CREATE DATABASE lab0120262;
```

La configuración actual de `src/main/resources/application.properties` utiliza:

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/lab0120262?useSSL=false&allowPublicKeyRetrieval=true&serverTimezone=UTC
spring.datasource.username=root
spring.datasource.password=root
```

Si tus credenciales son diferentes, actualiza estas propiedades antes de iniciar la aplicación.

> Para entornos reales, evita almacenar credenciales directamente en el repositorio y utiliza variables de entorno o un gestor de secretos.

## Cómo ejecutar

Clona el repositorio y entra en el directorio del proyecto:

```bash
git clone https://github.com/VSofia-1/lab0120262.git
cd lab0120262
```

Inicia la aplicación usando Maven Wrapper:

### Linux / macOS

```bash
./mvnw spring-boot:run
```

### Windows

```bat
mvnw.cmd spring-boot:run
```

También puedes compilar el proyecto y ejecutar las pruebas:

```bash
./mvnw clean test
```

Para generar el archivo `.jar`:

```bash
./mvnw clean package
```

Después, ejecuta el artefacto generado:

```bash
java -jar target/lab1v20262-0.0.1-SNAPSHOT.jar
```

La aplicación se inicia en:

```text
http://localhost:8088
```

La interfaz web está disponible en:

```text
http://localhost:8088/frontend.html
```

## API REST

La aplicación utiliza el prefijo `/api`.

### Clientes

| Método | Endpoint | Descripción |
|---|---|---|
| `GET` | `/api/customers` | Lista todos los clientes |
| `GET` | `/api/customers/{id}` | Consulta un cliente por ID |
| `POST` | `/api/customers` | Crea un cliente |
| `PUT` | `/api/customers/{id}` | Actualiza un cliente |
| `DELETE` | `/api/customers/{id}` | Elimina un cliente |

Ejemplo de creación:

```bash
curl -X POST http://localhost:8088/api/customers \
  -H "Content-Type: application/json" \
  -d '{
    "firstName": "Ana",
    "lastName": "Gómez",
    "accountNumber": "1001",
    "balance": 150000.00
  }'
```

### Transacciones

| Método | Endpoint | Descripción |
|---|---|---|
| `POST` | `/api/transactions` | Realiza una transferencia |
| `GET` | `/api/transactions/{accountNumber}` | Consulta transacciones de una cuenta |
| `PUT` | `/api/transactions/{id}` | Actualiza una transacción |
| `DELETE` | `/api/transactions/{id}` | Elimina una transacción |

Ejemplo de transferencia:

```bash
curl -X POST http://localhost:8088/api/transactions \
  -H "Content-Type: application/json" \
  -d '{
    "senderAccountNumber": "1001",
    "receiverAccountNumber": "1002",
    "amount": 25000.00
  }'
```

Consulta del historial de una cuenta:

```bash
curl http://localhost:8088/api/transactions/1001
```

## Modelo de datos

### Cliente

- `id`: identificador generado automáticamente.
- `accountNumber`: número de cuenta único.
- `firstName`: nombre del cliente.
- `lastName`: apellido del cliente.
- `balance`: saldo disponible.

### Transacción

- `id`: identificador generado automáticamente.
- `senderAccountNumber`: cuenta de origen.
- `receiverAccountNumber`: cuenta de destino.
- `amount`: valor transferido.
- `timestamp`: fecha y hora de la transacción.

## Configuración principal

La aplicación utiliza el puerto `8088`:

```properties
server.port=8088
```

Hibernate actualiza automáticamente el esquema de la base de datos:

```properties
spring.jpa.hibernate.ddl-auto=update
```

Durante el desarrollo también se muestran las consultas SQL generadas:

```properties
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true
```

## Equipo

- Sofia Espinal Vasquez
- Angel Yepes

## Licencia

Este proyecto no especifica una licencia.

## Link del informe

https://drive.google.com/file/d/1Fpj0zsrlNXIa3QxApOIkN2pFj8ijvNJ9/view?usp=sharing
