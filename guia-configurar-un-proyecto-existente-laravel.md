# Para comenzar a trabajar

* Descomprimir el archivo del proyecto en un directorio o clonarlo desde un repositorio git
* Abrir una terminal en el directorio del proyecto
* Abrir el editor de texto en el directorio del proyecto

## Composer Install

Para descargar todas las dependencias del proyecto ejecutar el siguiente comando en la terminal del proyecto.

```shell
composer install
```

## Configurar el entorno local (.env)

Las configuraciones del entorno local deben actualizarse en el archivo **.env**

### Copiar el archivo .env.example

Pero esté archivo no existe en el directorio del proyecto debido a que el sistema de control de versiones de código no lo guarda. Entonces es necesario generar uno nuevo, copiando el archivo de ejemplo con el comando en la terminal del proyecto.

```shell
cp .env.example .env
```

### Clave de seguridad de la aplicación

Generar una nueva clave de seguridad de la aplicación con el siguiente comando en la terminal del proyecto.

```shell
php artisan key:generate
```

Ahora la en el archivo **.env** podemos ver que la entrada **APP_KEY** tiene un nuevo valor.

### Configurar la base de datos

#### MySQL

Debemos tener una base de datos creada en el servidor de base de datos mysql.
Si no tenemos una base de datos o queremos generar una nueva podemos hacerlo desde **MySQL Workbench** ejecutando el comando.

```sql
create database db_web;
```

#### Conexión a la base de datos

Una vez que tenemos la base de datos es hora configurar la conexión a la base de datos en el archivo **.env** modificando las entradas adecuadas para su entorno, las más básicas son:

```php
DB_DATABASE=db_web
DB_USERNAME=root
DB_PASSWORD=claveSecreta
```

#### Migrar la Base de Datos

Una vez que la base de datos está configurada es hora correr la migración desde la aplicación para generar la estructura de datos ejecutando el siguiente comando en la terminal del proyecto.

```shell
php artisan migrate
```

#### Generar los Datos

Hasta el momento tenemos la estructura de datos en la base de datos, pero todas las tablas están vacías. Es el instante oportuno para correr los **Seeder** y de está manera generar los datos programados ejecutando el comando en la terminal del proyecto.

```shell
php artisan db:seed
```

## Iniciar el servidor de Desarrollo

Iniciar el servidor de desarrollo ejecutando el comando en la terminal del proyecto.

```shell
php artisan serve
```
