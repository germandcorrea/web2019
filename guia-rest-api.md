# Guía REST API Laravel 6

## Crear un nuevo Proyecto

```shell
composer create-project laravel/laravel --prefer-dist l6proy
```

* **composer** es el gestor de dependencias de PHP. [sitio oficial de composer](https://getcomposer.org/)
* **create-project** generar un proyecto nuevo
* **laravel/laravel** el framework que usaremos.
* **--prefer-dist** utilizar la versión estable del los paquetes.
* **l6proy** nombre del nuevo proyecto. Se generará una nueva carpeta con el mismo nombre en el directorio actual.

## Cambiar el directorio de trabajo

es muy importante trabajar siempre parado en el directorio de trabajo, todos los comandos que usa el desarrollador para interactuar con la webapp mediante **artisan**, **composer**, **npm**, **yarn**, etc. lo necesitan.

```shell
cd l6proy
```

## Agregar el paquete de laravel-debugbar

[sitio oficial de laravel-debugbar](https://github.com/barryvdh/laravel-debugbar)

```shell
composer require barryvdh/laravel-debugbar --dev
```

* **composer** es el gestor de dependencias de PHP.
* **install** agregar la dependencia al proyecto.
* **barryvdh/laravel-debugbar** es una barra de depuración, muy recomendable para desarrollar.
* **--dev** sólo para el entorno **dev** de desarrollo, en el entorno **prod** de producción no estará disponible.

## Abrir el directorio actual con el editor de texto

En la maquina virtual tienen instalado **Visual Studio Code** ([Web Oficial](https://code.visualstudio.com/)), con un conjunto de extensiones adecuadas para el desarrollo web en laravel.

```shell
code .
```

## Ciclo Básico de Desarrollo

**artisan** es el script que nos permite al desarrollador comunicarse con la webapp, para obtener datos específicos de la aplicación, y también modificar los componentes de la misma.

### Ejecutar el servidor de desarrollo

Laravel tiene un servidor de desarrollo integrado que se inicia con el siguiente comando

```shell
php artisan serve
```

**Importante** para detener el servidor de desarrollo es necesario usar la convinación de teclas **CTRL+C**

### Mostrar una lista de las rutas disponibles en la webapp

```shell
php artisan route:list
```

### Agregar ejemplos simples de rutas que obtienen datos desde la API

en el archivo **routes/api.php** debemos configurar las entradas adecuadas para publicar las apis, laravel por defecto configura todas las rutas relativas a la URI **/api** de tal forma que las **URL** de cada recurso configurado tendrá el prefijo **api**.

por ejemplo, para obtener un dato desde un endpoint necesitamos solicitar una **URL** con verbo **GET**, entonces la configuración de la ruta debería quedar de la siguiente manera.

```php
// solicitar la URL http://127.0.0.1:8000/api/ping
// la respuesta es un texto simple con el texto pong
Route::get('ping',function(){
    return response('pong');
});

// solicitar la URL http://127.0.0.1:8000/api/ping-json
// la respuesta es un objeto JSON con el atributo msg con el valor pong
Route::get('ping-json',function(){
    $data=[
        'msg' => 'pong'
    ];
    return response()->json($data);
});
```

### Crear una base de datos en mysql

Para crear una base de datos es necesario usar un cliente de administración de MySQL, en la maquina virtual tienen instalado **MySQL Workbench** buscar en el menu **Inicio --> Programación --> MySQL Workbench** o el icono del escritorio.

```sql
create database pf;
```

### Modificar el archivo de configuración de entorno **.env**

```php
DB_DATABASE=pf
DB_USERNAME=root
DB_PASSWORD=claveSecreta
```

### Migrar la Base de Datos

Una vez configurada la base de datos, podemos comenzar a interactuar desde **artisan** con la base de datos. de manera que si la base de datos está vacía podremos generar las tablas manejadas por los modelos que tenemos definidos hasta este momento.

```shell
php artisan migrate
```

## Desplegar la aplicación en Heroku

[guia oficial de heroku](https://devcenter.heroku.com/articles/getting-started-with-laravel)

Lo primero que necesitamos es una cuenta en heroku, es gratuito para eso hay que ir a la página de heroku.com y registrase.

Luego es necesario instalar la herramienta de linea de comandos para comunicarse con heroku, que en la maquina virtual ya está instalada.

Iniciar Sesión en heroku, nos va a pedir el correo electrónico y la password con la que nos registramos en el sitio.

```shell
heroku login
```

Generar el archivo **Procfile** donde indicamos que vamos a usar como servidor WEB una instancia de apache2 con php configurado apuntando **DocumentRoot** al directorio **public** de nuestra aplicación.  

```shell
echo "web: vendor/bin/heroku-php-apache2 public/" > Procfile
```

Heroku como la mayoría de servicios web en la nube, utilizan **GIT** para subir los archivos al repositorio del servidor. por lo tanto es necesario iniciar un repositorio git dentro de nuestro proyecto **git init**, agregar todos los archivos del proyecto al repositorio **git add .** y por ultimo confirmar los que los archivos están listos en nuestro repositorio **git commit -m "app: inicio de repositorio"**, todos esto es en nuestro repositorio local.

```shell
git init
git add .
git commit -m "app: inicio de repositorio"
```

Crear una nueva aplicación en el servidor de Heroku. el nombre de la aplicación se generará automáticamente para no producir conflictos en los nombres (podemos también poner un nombre a gusto agregando el nombre al final, pero corremos el riesgo de producir una colisión de nombres).

```shell
heroku create
```

Agregar PostgreSQL a nuestra aplicación en el servidor de heroku, luego podremos ver que en el heroku tenemos una variable de entorno **DATABASE_URL** que contiene los parámetros de conexión hacia el servidor de base de datos.

```shell
heroku addons:create heroku-postgresql:hobby-dev
```

### Declaramos las variables que necesita laravel, para funcionar

Configuramos las variables de entorno en el servidor de heroku, es decir las variables que teníamos definidas en nuestro archivo **.env** tienen que definirse como variables de entorno en los servidores de heroku.

#### Variables para la clave secreta de la aplicación

```shell
heroku config:set APP_KEY=$(php artisan key:generate --show)
```

#### Variables para el driver de base de datos

 Como la base de datos ahora es PostgreSQL debemos indicar la configuración adecuada de la variable de entorno **DB_CONNECTION** para que laravel pueda usar el driver adecuado **pgsql**.

```shell
heroku config:set DB_CONNECTION=pgsql
```

### Subir la app a producción

Una vez configurada la application es hora de subir la aplicación al servidor de Heroku, nuevamente mediante **GIT**

```shell
git push heroku master
```

#### Correr migraciones si es necesario

Corremos las migraciones en el servidor de heroku

```shell
heroku run php artisan migrate
```

#### Lanzamos nuestra aplicación en el navegador web

```shell
heroku open
```

## Parte 2 - Generar la API para los Modelos

### Crear el modelo para los Artículos

```php
php artisan make:model Article -m
```

* **make:model** generar un nuevo modelo
* **Article** nombre del Modelo. en el directorio **app** se genera el archivo **Article.php**
* **-m** genera el archivo de migraciones. En el directorio de migraciones(**database/migrations**) encontraremos un nuevo archivo **fecha-de-hoy-en-formato-iso_create_articles_table.php**

#### Configurar las columnas del en la migración del Modelo Article

Editar el archivo **database/migrations/fecha-de-hoy-en-formato-iso_create_articles_table.php** en el método **up** dentro de la función anónima de **Schema::create** agregar los nuevos atributos.

* [Documentación Oficial Migraciones](https://laravel.com/docs/6.x/migrations)

```php
$table->string('title');
$table->text('body');
```

#### Ejecutar la migración

Cada vez que creamos un nuevo modelo y terminamos la definición de los atributos en la migración es necesario correr la migración para que la base de datos tome la nueva estructura.

```shell
php artisan migrate
```

#### Configurar atributos de asignación masiva en el Modelo Article

Laravel usa una propiedad protegida denominada **fillable** en cada uno de los modelos para indicar cuales son los atributos que se permiten actualizar masivamente, por lo general mediante los **seeders** o cuando se usa el método **create** del modelo.

Editar el archivo **app/Article.php** y agregar el atributo.

```php
protected $fillable = ['title','body'];
```

#### Poblando la tabla de artículos

Generamos un nuevo Seeder para el modelo Article con el objetivo de crear 50 artículos nuevos en la tabla, usando **faker** para crear datos de prueba [documentación oficial de faker](https://github.com/fzaninotto/Faker).

```shell
php artisan make:seeder ArticleTableSeeder
```

* **make:seeder** crear un nuevo Sedeer.
* **ArticleTableSeeder** el nombre del Sedeer.

Con el comando anterior se genera el archivo **database/seeds/ArticleTableSeeder.php**, en el mismo
tenemos que generar los datos agregando el siguiente código al método **run**

```php
$faker = \Faker\Factory::create(); //creamos una instancia de faker para obtener el generador de datos de prueba
for ($i=0; $i < 50; $i++) {  // generar 50 artículos
    Article::create([
        'title' => $faker->sentence, // un título de prueba
        'body' => $faker->paragraph  // un texto largo de prueba
    ]);
}
```

Ahora necesitamos correr el Seed para poder ver los datos generados en la base de datos.

```shell
php artisan db:seed --class=ArticleTableSeeder
```

* **db:seed** ejecutar un Seeder.
* **--class=ArticleTableSeeder** indicar la clase específica del Sedeer.

#### Crear un controlador para un api

```shell
php artisan make:controller API/ArticleController --api -m Article
```

genera el archivo **app/Http/Controllers/API/ArticleController.php**

* **make:controller** crear un controlador con los métodos vacíos.
* **API/ArticleController** nombre del controlador **ArticleController** dentro del namespace **API**.
* **--api** genera sólo los métodos necesarios para un recurso **REST** (index, store, show, update, destroy).
* **-m Article** indicamos en nombre del modelo.
  
Editar el archivo **app/Http/Controllers/API/ArticleController.php**

##### Modificar el método index

```php
/**
get api/articles
una solicitud get a api/article retorna un listado de artículos
*/

// $articles = Article::all();   // buscar todos los artículos
$articles = Article::paginate(); // buscar todos los artículos pero paginados de a 15
return $articles; //retorna los artículos encontrados
```

##### Modificar el método store

```php
/**
post api/articles
una solicitud post a api/article crea un nuevo artículo y luego lo retorna.
*/

$article = Article::create($request->all()); //Crear un Artículo con los atributos que llegan en la solicitud.
return response()->json($article,201); //retornar el nuevo Artículo con el código de estado 201 (CREATED)
```

##### Modificar el método show

```php
/**
get api/articles/{article}
una solicitud get a api/article/15  retorna el artículo con id 15
*/

return $article; // retornar el Artículo encontrado por su id
```

##### Modificar el método update

es necesario agregar un parámetro **Article $article** al método update para que laravel entienda que tiene que inyectar el objeto del modelo **Article** que encuentre con el id pasado por la url

```php
public function update(Request $request, Article $article)
{
    /**
    put api/articles/{article}
    una solicitud put a api/article/15  modifica el artículo con id 15
    */
    $article->update($request->all()); // modificar los atributos del artículo encontrado con los valores que llegan en la solicitud.
    return $article; // retornamos el objeto modificado
}
```

##### modificar el método destroy

```php
/**
delete api/articles/{article}
una solicitud delete a api/article/15  elimina el artículo con id 15
*/
$article->delete(); // eliminar el artículo
return response()->json(null,204); // retornar vacío con el código de estado 204 (NO CONTENT)
```

#### Agregar las rutas

Editar el archivo de rutas **routes/api.php** y agregar la ruta.
[Documentación rutas](https://laravel.com/docs/6.x/routing)

```php
Route::apiResource('articles', 'API\ArticleController');
```

### Ahora es necesario agregar un nuevo Modelo Comentario

Cada articulo puede tener comentarios generados por los usuarios.
Es evidente la existencia de una relación de **uno a muchos (hasMany)** entre el modelo **Article** y **Comment**.

#### Crear el modelo para los comentarios

* [Documentación Oficial Relaciones](https://laravel.com/docs/6.x/eloquent-relationships)
* [Documentación Oficial Migraciones](https://laravel.com/docs/6.x/migrations)

```php
php artisan make:model Comment -m
```

* **make:model** generar un nuevo modelo
* **Comment** nombre del Modelo. en el directorio **app** se genera el archivo **Comment.php**
* **-m** genera el archivo de migraciones. En el directorio de migraciones(**database/migrations**) encontraremos un nuevo archivo **fecha-de-hoy-en-formato-iso_create_comments_table.php**

##### Modificar la Migración

Editar el archivo **database/migrations/fecha-de-hoy-en-formato-iso_create_comments_table.php** en el método **up** dentro de la función anónima de **Schema::create** agregar los nuevos atributos.

```php
$table->string('comentario');
$table->unsignedBigInteger('article_id'); //definir la columna que es clave foránea, por defecto laravel usa números enteros grandes sin signo.
$table
    ->foreign('article_id') // nombre de la columna que es clave foránea
    ->references('id') // nombre de la columna que clave primaria
    ->on('articles') //nombre de la tabla
    ;
```

##### Modificar el Modelo Comment

Editar el archivo **app/Comment.php** y agregar el nuevo método que contiene la relación desde **Comment** hacia **Article** que es de tipo pertenece a **belongsTo**.

*Un Comentario **(Comment)** pertenece a **(belongsTo)** un Artículo **(Article)***

```php
function article(){
    return $this->belongsTo('App\Article');
}
```

##### Modificar el Modelo Article

Ahora nos toca modificar la relación inversa.
Editar el archivo **app/Article.php** y agregar el nuevo método que contiene la relación desde **Article** hacia **Comment** es de tipo tiene muchos **hasMany**.

*Un Artículo **(Article)** tiene muchos **(hasMany)** Comentarios **(Comment)***

```php
function comments(){
    return $this->hasMany('App\Comment');
}
```

##### Generar un Seeder de Comentarios con el objetivo de crear entre uno y diez comentarios para cada Articulo

```shell
php artisan make:seeder CommentTableSeeder
```

editar el nuevo archivo generado **database/seeds/CommentTableSeeder.php** y agregar el código en el método **run**

```php
$faker = \Faker\Factory::create(); // crear instancia de generador de datos de prueba
$articles = App\Article::all(); // retorna todos los artículos de la base de datos.
foreach ($articles as $a) { // iterar sobre cada artículo
    $comentarios=[]; // un array vacío de comentarios
    $cantidad = rand(1,10); // entre 1 y 10 comentarios aleatorios
    for ($i=0; $i < $cantidad ; $i++) { //iterar hasta llegar a la cantidad de comentarios
        $comentarios[]=['comentario' => $faker->sentence]; // un nuevo comentario de prueba
    } // for ($i=0; $i < $cantidad ; $i++)
    $a->comments()->createMany($comentarios); //guardar todos los comentarios en el artículo actual
} //foreach ($articles as $a)
```

##### Ejecutar sólo el seeder de comentarios

```shell
php artisan db:seed --class=CommentSeeder
```

#### Agregar a la API REST

* Permitir agregar nuevos Comentarios a un Artículo
* Permitir retornar todos los comentarios asociados a un Artículo

##### Modificar el Controlador ArticleController

```php
    /**
     * almacena un nuevo comentario en el artículo seleccionado
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \App\Article  $article
     * @return \Illuminate\Http\Response
     */
    public function addComment(Request $request, Article $article){
        $article
                ->comments()
                ->create( $request->all() );
        return $article;
    }

    /**
     * retorna todos los comentarios asociados al artículo seleccionado
     *
     * @param  \App\Article  $article
     * @return \Illuminate\Http\Response
     */
    public function comments(Article $article){
        return $article->comments;
    }
```

##### Modificar el archivo de rutas

agregar las rutas en el archivo **routes/api.php**

```php
// POST /api/articles/5/comments
Route::post('articles/{article}/comments','API\ArticleController@addComment')->name('articles.addComment');

// GET /api/articles/5/comments
Route::get('articles/{article}/comments','API\ArticleController@comments')->name('articles.comments');
```

### Subir los cambios a producción

```shell
git add .  # agregar al seguimiento a todos los archivos
git commit -m 'parte 2 completa' # confirmar los cambios con un comentario
git push heroku master # publicar en heroku
heroku run php artisan migrate # correr la migración en la base de producción
heroku open # abrir la web app en el navegador web
```
