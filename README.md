# Introduction to Laravel

The following practical instructions are a quick start. You should refer to the Laravel website (https://laravel.com/) for comprehensive info on building Laravel applications or https://laracasts.com/series/laravel-from-scratch-2026 for a more in-depth introduction.


To keep things nice and clean, we will use a new separate codespace for learning Laravel.

To get started, click on the green button in the top-left on this repository that says 'Use this template' and then select 'Open in a codespace'.

A new Codespace should start

> If you are using Herd, you would take a different approach e.g. using the Laravel CLI to create an app for you. 

### Install dependencies

The Codespace already has Laravel installed. You just need to complete setting up and configuring Laravel before you can start work.

Have a look at _composer.json_ this lists all the dependencies we need to load. 

- In your Codespace, in the terminal enter:

```
composer install
```

- This will install all the libraries needed for Laravel to work.

### Configure the application using a .env file

- Create a new file name _.env_ in the root of the codespace.
- Copy the entire contents of _example.env_ into _.env_
- In _.env_
  - Change the `APP_URL` to be the following e.g.
  ```
  APP_URL=https://name-of-codespace-8000.app.github.dev/
  ```
  - You can get the 'name-of-codespace' from the URL in the browser. It will be two words and a random series of letters and numbers e.g. _special-potato-ppvp74vgxfrg6r_. So for this example I'd enter an APP_URL of:-
  ```
  APP_URL=https://special-potato-ppvp74vgxfrg6r-8000.app.github.dev/
  ```
  - Change the database settings to our usual settings:
  ```
  DB_CONNECTION=mysql
  DB_HOST=db
  DB_PORT=3306
  DB_DATABASE=webdev
  DB_USERNAME=student
  DB_PASSWORD=secret
  ```
- Save your changes to `.env`.

> It's a good idea to store configuration information such as database settings in a separate file that won't be publicly accessible. This is exactly what the _.env_ file is. Laravel will load settings from this file automatically.

- In the terminal enter the following:

```
php artisan key:generate
```

This will generate a key that is used to encode potentially sensitive data e.g. session data, cookies etc.

- Next, enter the following:

```
php artisan migrate
```

This will generate some basic database tables that Laravel needs to work. At this point you are all set-up and ready to start learning Laravel.

### Using Artisan

Artisan is a CLI (Command Line Interface) that comes with Laravel. It is used to run tasks and automate code generation. 

First we need Artisan to generate some database tables for us

- In the terminal enter

```
php artisan migrate
```

You should get some feedback telling you that three tables have been set-up. We don't need to worry about these tables at the moment.

Next start the Laravel app

```
php artisan serve
```

This instructs Artisan to start a web server, just we have done manually in previous weeks. 

We could manually `cd` into the _public_ folder and then run `php -S 0.0.0.0:8000`. Artisan simple provides a shortcut.

You should be able to view your Laravel app on port 8000 where you will see the 'Let's get started' message.

## Routes <a name="routes"></a>

The first thing to understand when working with Laravel is how routing works, how the URI in the browser maps to code that will be executed. Open _routes/web.php_. This is where the routes for an application are defined. Add the following route at the end of this file.

```php
Route::get('/films', function () {
    return "Display all the films.";
});
```

In the browser add _/films_, at the end of the URL and you should see the 'Display all the films.' message. Next, add a few more routes:-

```php

Route::get('/films/create', function () {
    return "Add a new film";
});

Route::get('/about', function () {
    return "About the amazing app";
});


```

- Check these work in a browser.

Although routing takes a slightly different approach (we are calling the static `get()` method of the `Route` class), and it doesn't rely on using a query string, this routes file is similar to the front controller we used previously.

This code is fine for testing how routes work, but really we want to call a controller from the routes file.

## Creating a Controller

We will use Artisan to create a controller for us. We are already running our web server in a terminal. We need a separate one to issue our Artisan commands.

- In Codespaces create a new terminal (there is a 'plus' sign in the terminal panel)


- Enter the following command:

```
php artisan make:controller FilmController
```

- If you look inside the _app/Http/Controllers_ folder you should see a _FilmController.php_ file.
- Add `index()` and `create()` methods in the controller i.e.

```php
namespace App\Http\Controllers;
use Illuminate\Http\Request;

class FilmController extends Controller
{
    public function index()
    {
        return "Display all the films (from the controller)";
    }

    public function create()
    {
        return "Add a new film (from the controller)";
    }
}
```

Change the routes file so that it calls methods of `FilmController` .

```php
use Illuminate\Support\Facades\Route;
use App\Http\Controllers\FilmController;

Route::get('/', function () {
    return view('welcome');
});

Route::get('/films', [FilmController::class, 'index']);
Route::get('/films/create', [FilmController::class, 'create']);

Route::get('/about', function () {
    return "About the amazing film app";
});
```

Test this works, you should find the messages are now coming from the controller and not the routes file.

> What is `::class`. It's a PHP feature that makes it easy to get hold of class names see https://stackoverflow.com/questions/30770148/what-is-class-in-php for more information.

## Views

In an MVC structure we don't want our controllers to generate output, instead we should be using views.

- In the _resources/views_ folder, add a new folder, call it _films_.
- Create a new file, name it _index.blade.php_, and save it in the _films_ folder (we must use the _.blade.php_ suffix).

  > In Laravel, views are written using the Blade templating language (https://laravel.com/docs/frontend#php-and-blade). Blade makes it easier to integrate PHP code into our view files.

- Paste in the following code into _index.blade.php_.

```HTML
<!DOCTYPE HTML>
<html>
<head>
<title>List the films</title>
<meta http-equiv="content-type" content="text/html;charset=utf-8">
<link href="{{asset('css/style.css')}}" type="text/css" rel="stylesheet">
</head>
<body>
<nav>
  <ul>
    <li><a href="/films">Home</a></li>
    <li><a href="/films/create">Add new film</a></li>
    <li><a href="/about">About</a></li>
</ul>
</nav>

<h1>Here's a list of films</h1>

<p>The films will eventually appear here.</p>

</body>
</html>
```

- Save this file.
- In _FilmController_ change the `index()` method so it loads this view.

```php
    public function index()
    {
        return view('films.index');
    }
```

- Check your view file is displayed when the user accesses the _films_ route in a browser.

### Adding some CSS

- Find the _public_ folder, inside this folder add a new folder and name it _css_.
- Create a new file _style.css_ and save it in this _css_ folder.
- Add the following simple rules to _style.css_.

```css
body {
  background-color: DarkSeaGreen;
  font-family: Arial, Helvetica, sans-serif;
}
button {
  text-transform: uppercase;
}
```

- Refresh the page it should now have some styling.
- Have a look in _index.blade.php_ at the link element.

```html
<link href="{{asset('css/style.css')}}" type="text/css" rel="stylesheet" />
```

This uses blade to load the CSS file. The double curly braces are used to call PHP code and echo it. `asset()` is a helper function in Laravel that is used to load assets.

We could do the same thing using plain PHP code e.g.

```html
<link
  href="<?php echo 'http://localhost/css/style.css'; ?>"
  type="text/css"
  rel="stylesheet"
/>
```

However, using blade makes the code cleaner, and the `asset()` helper function makes it easier to specify the location of our CSS file.

### Creating a Layout File

There are a number of different ways to do this, the most recent way is to use components. We will create a layout component. In the _views_ folder, add a new folder and name it _components_. Your _resources_ folder will then look like the following:-

- resources
  - views
    - films
      - index.blade.php
    - components

Create a new file, name it _layout.blade.php_ and save it in the _components_ folder.

Paste the following into _layout.blade.php_.

```html
<!DOCTYPE html>
<html>
  <head>
    <title>{{$title}}</title>
    <meta http-equiv="content-type" content="text/html;charset=utf-8" />
    <link href="{{asset('css/style.css')}}" type="text/css" rel="stylesheet" />
  </head>
  <body>
    <nav>
      <ul>
        <li><a href="/films">Home</a></li>
        <li><a href="/films/create">Add new film</a></li>
        <li><a href="/about">About</a></li>
      </ul>
    </nav>
    {{$slot}}
  </body>
</html>
```

This is like a master page or template that we will base all the other pages one. It is plain HTML but with two variables, `$title` which will display a custom page title and `$slot` which will display the specific content for the page.

Go back to _index.blade.php_ and change it to:-

```html
<x-layout title="List the films">
  <h1>Here's a list of films</h1>
  <p>The films will appear here.</p>
</x-layout>
```

- Any tags that start with `<x-` indicate we want to use a component. We then use the file name (layout) to specify the component we want to use.
- Anything between the opening and closing `</x-layout>` tags will be passed into `$slot`.
- We have also added a custom attribute _title_ which is used to specify a page title and will be passed to the `$title` variable in the layout file.
- Save the files and test the view still works.

### Adding the create View

- Add a create view

```html
<x-layout title="Add new film">
  <h1>Add a new film</h1>

  <form method="POST" action="/films">
    @csrf
    <div>
      <label for="title">Title:</label>
      <input type="text" id="title" name="title" />
    </div>
    <div>
      <label for="year">Year:</label>
      <input type="text" id="year" name="year" />
    </div>
    <div>
      <label for="duration">Duration:</label>
      <input type="text" id="duration" name="duration" />
    </div>
    <div>
      <button type="submit">Save the Film</button>
    </div>
  </form>
</x-layout>
```

- Save this as _create.blade.php_ in the _resources/views/films_ folder
- Back in `FilmController.php`, load this view from the `create()` method

```php
    public function create()
    {
        return view('films.create');
    }
```

The form won't work yet, but you should be able to access the create view.



## Databases

### Creating a Migration

Laravel allows us to define database schemas using PHP code. We call these migrations. This makes it very easy to undo changes to the structure of our database and tables, and to completely re-design and re-create our tables without having to import/export .sql files.


- In the terminal enter the following command:

```
php artisan make:migration create_films_table --create=films
```

Look in the _database/migrations_ folder, open the _create_films_table.php_ file. You should see that an _id_ column has been defined for us already.

Modify this file to add definitions for _title_, _year_ and _duration_ columns. Your _create_films_table.php_ file should then look like the following:

```php
use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    /**
     * Run the migrations.
     */
    public function up(): void
    {
        Schema::create('films', function (Blueprint $table) {
            $table->id();
            $table->string('title');
            $table->smallInteger('year');
            $table->smallInteger('duration');
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations.
     */
    public function down(): void
    {
        Schema::dropIfExists('films');
    }
};
```

Essentially this code creates a _films_ table for us and defines four columns. The timestamps column is optional but is included by default in every Laravel migration. To run the migration enter the following into the terminal/shell:

```
php artisan migrate
```

You should get confirmation that the table has been created.

### Seeding the Database

In addition to creating migrations we can also seed the database. Laravel will populate our database tables with some sample data. Again, we will use Artisan, this time to create a seeder.

- Enter the following command into the terminal.

```
php artisan make:seeder FilmSeeder
```

Have a look in the _database/seeders_ folder and open _FilmSeeder.php_.

Modify this file to specify some films for the database. Here's an example:

```php
namespace Database\Seeders;

use Illuminate\Database\Seeder;
use Illuminate\Support\Facades\DB;

class FilmSeeder extends Seeder
{
    public function run(): void
    {
        DB::table('films')->insert([
            ['title' => "Winter's Bone",      'year' => 2010, 'duration' => 100],
            ['title' => "Do The Right Thing", 'year' => 1989, 'duration' => 120],
            ['title' => "The Incredibles",    'year' => 2004, 'duration' => 115],
            ['title' => "The Godfather",      'year' => 1972, 'duration' => 177],
            ['title' => "Dangerous Minds",    'year' => 1995, 'duration' => 99],
            ['title' => "Spirited Away",      'year' => 2001, 'duration' => 124],
            ['title' => "Moonlight",          'year' => 2016, 'duration' => 111],
            ['title' => "Life of PI",         'year' => 2012, 'duration' => 127],
            ['title' => "Gravity",            'year' => 2013, 'duration' => 91],
            ['title' => "Arrival",            'year' => 2016, 'duration' => 116],
            ['title' => "Wonder Woman",       'year' => 2017, 'duration' => 141],
            ['title' => "Mean Girls",         'year' => 2004, 'duration' => 97],
            ['title' => "Inception",          'year' => 2010, 'duration' => 108],
            ['title' => "Donnie Darko",       'year' => 2001, 'duration' => 113],
            ['title' => "Get Out",            'year' => 2017, 'duration' => 117],
        ]);
    }
}


```


Next, we need to tell the _DatabaseSeeder_ class to run the _FilmSeeder_. From the _seeders_ folder open _DatabaseSeeder.php_.
Modify it so that it runs our _FilmSeeder_ i.e.

```php
namespace Database\Seeders;

use App\Models\User;
// use Illuminate\Database\Console\Seeds\WithoutModelEvents;
use Illuminate\Database\Seeder;

class DatabaseSeeder extends Seeder
{
    /**
     * Seed the application's database.
     */
    public function run(): void
    {
        $this->call([
            FilmSeeder::class,
        ]);
    }
}
```

- Back in the terminal/shell, run the following command

```
php artisan db:seed
```

- In Adminer check your _films_ table has some films in it.

You should find there are lots of additional tables that Laravel has set-up. We don't need to worry about these at the moment. 

> Sometimes we want to re-build the database e.g. we've got lots of nonsense data from testing, or we've changed the schema. If we use the `migrate:fresh` command we can drop all tables and re-run the migrations and seeding e.g. `php artisan migrate:fresh --seed`. If you get really stuck you can completely wipe the database so you can start again with `php artisan db:wipe`

## Models

Again, we will use Artisan to generate our model classes. In the terminal enter the following:

```
php artisan make:model Film
```

- Have a look in the _app/Models_ folder, you should find that a _Film_ class has been generated.
- The _Film_ model is empty but it extends a base class _Model_ that contains lots of methods for working with the database, Laravel uses something called Eloquent (https://laravel.com/docs/eloquent) for object-relational mapping, based on the Active Record pattern.

> **Convention over Configuration.** Laravel works on conventions. If we create a model class called _Film_, it will assume that this maps to a database table called 'films'. We don't have to do anything else to set up the object relational mapping.

- We can add our own methods to _Film_ e.g. add the following `getAge()` method:

```php
    public function getAge():int
    {
        return Date('Y') - $this->year;
    }
```

## Tying everything together - Displaying all the films

- Open _FilmController.php_.
- Add a use statement to import the Film class.

```php
use App\Models\Film;
```

- Modify the `index()` method to use the Film class.

```php
public function index()
{
    $films = Film::all();
    return view('films.index',['films' => $films]);
}
```

- We want a list of all the films, so we call the `all()` method. This method has been defined automatically for us by the Eloquent ORM.
- Also note that the `all()` method returns an [Eloquent Collection](https://laravel.com/framework/docs/eloquent-collections#main-content). This is like an array of film objects. Open _index.blade.php_ (from the _resources/views/films_ folder). Modify it so that it uses this collection of film objects:

```php
<x-layout title="List the films">
    <h1>Here's a list of films</h1>
    @foreach ($films as $film)
    <p>
        <a href="/films/{{$film->id}}">
            {{$film->title}}
        </a>
    </p>
    @endforeach
</x-layout>
```

- To echo out the value of a variable we use the double curly brackets `{{...}}`.
- Note the use of `@foreach`. These are part of the blade templating engine, and make it easy for us to include control structures in our views.
- In a browser, test this works. You should see a list of films.

## Tying everything together - Getting Create to Work

If you look in _create.blade.php_ you should see that the form will be submitted to a URI of _*/films*_ using the _POST_ method.

```html
<form method="POST" action="/films">
  @csrf
  <div>...</div>
</form>
```

The `@csrf` directive is needed every time we create a form. It is a security measure to protects us from cross-site request forgery. Read about it here - https://laravel.com/docs/csrf#main-content.

### Adding a new route

Back in _routes/web.php_ add another route for storing the new film.

```php
Route::post('/films', [FilmController::class, 'store']);
```

We can use the same route (_/films_) because we are using a different method (POST). Laravel can distinguish between the two.

### Adding a `store()` Method to the Controller

- Open _FilmController.php_.
- Add the following `store()` method.

```php
public function store(Request $request) 
{
    $film = new Film();
    $film->title = $request->title;
    $film->year = $request->year;
    $film->duration = $request->duration;
    $film->save();
    return redirect('/films');
}
```

The Laravel _request_ object allows us to access the form data (previously we would have used the `$_POST` array e.g. `$_POST['title']`). We use these values from _request_ to set properties on a film object, we then save it, and finally redirect to the home page.

## Tying everything together - Implementing Show

Add the following route to the _routes/web.php_ file

```php
Route::get('/films/{id}', [FilmController::class, 'show']);
```

Add the following `show()` method to the controller

```php
public function show(int $id)
{
    $film = Film::findOrFail($id);
    return view('films.show', ['film' => $film]);
}
```

- The _id_ value which is part of the route is passed as a parameter to the `show()` method.
- We can then use Eloquent to get a single film object using this _id_ value (`Film::findOrFail($id);).
- If `findOrFail` fails to find a film, it will show a page not found (404 error) automatically. 
- Finally we pass this film object to a _show_ view.

- Add the following _show.blade.php_ view to the _resources/views/films_ folder

```php
<x-layout title="Show the details for a film">
    <h1>{{$film->title}}</h1>
    <p>Year:{{$film->year}}</p>
    <p>Duration:{{$film->duration}}</p>

    <a href='/films/{{$film->id}}/edit'>
        <button>Edit</button>
    </a>

    <form method='POST' action='/films/{{$film->id}}'>
        @csrf
        @method('DELETE')
        <button type='submit'>Delete</button>
    </form>
</x-layout>
```

- Check this works, you should see the details displayed for the chosen film.
- To test your understanding, how can you also display the age of the film in this page?

## Tying everything together - Implementing Edit

- The _edit_ action is triggered from the _show_ view. Have a look at the _show_ view to make sure you understand how the edit URI is generated for the selected film.
- Set up a route to respond to this URI.

```php
Route::get('/films/{id}/edit', [FilmController::class, 'edit']);
```

- Next, add an `edit()` method to `FilmController`.

```php
    public function edit(int $id)
    {
        $film = Film::findOrFail($id);
        return view('films.edit', ['film' => $film]);
    }
```

- Next, create an edit view.

```php
<x-layout title="Edit a film">
    <h1>Edit the details for {{$film->title}}</h1>
    <form action="/films/{{$film->id}}" method="POST">
    @csrf
    @method('PATCH')
    <div>
        <label for="title">Title:</label>
        <!-- The text boxes are populated with values from the database ready for the user to edit -->
        <input type="text" id="title" name="title" value="{{$film->title}}">
    </div>
    <div>
        <label for="year">Year:</label>
        <input type="text" id="year" name="year" value="{{$film->year}}">
    </div>
    <div>
        <label for="duration">Duration:</label>
        <input type="text" id="duration" name="duration" value="{{$film->duration}}">
    </div>
    <div>
        <button type="submit">Save Changes</button>
    </div>
    </form>
</x-layout>
```

- Save this in the _resources/views/films_ folder as _edit.blade.php_.
- Test this works.
- Note that we specify a method of `PATCH`.

```php
@method('PATCH')
```

> Previously we have used GET and POST http methods to pass data. There are many more http methods e.g. PUT, PATCH, DELETE. However, browsers only support GET and POST. This line of code is used to tell Laravel we are wanting to perform a PATCH request (equivalent to update). See https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods/PATCH for more info

- The advantage of using `PATCH` is we can have cleaner URIs. We can use the same URL, e.g. _/films/3_, and depending on the method (GET, PATCH, DELETE) Laravel calls a different controller method..

## Testing your Understanding

### Implementing Update

The process is exactly the same as we did in previous weeks. The difference is you need to use Laravel to implement the update.

- Add a route to handle the update request. The route should call an `update()` method in `FilmController`.

```
Route::patch('/films/{id}', [FilmController::class, 'update']);
```

- Add an `update()` method in `FilmController`. This should:
  - You will need to add two parameters to the `update` method. The `$id` from the URL and the `$request` for the form data.
  
  ```php
    public function update(Request $request, int $id)
    {
        // add your code in here
    }
  ```
  - Use Eloquent to _find_ the correct film using the _id_ value. See how we used the static `findOrFail()` method when showing film details for a similar example or consult the documentation (https://laravel.com/docs/eloquent#retrieving-single-models) for advice.
  - Update the properties of this object using the values from the form and then call `save()` on the film object. Again, the documentation has examples (https://laravel.com/docs/eloquent#updates).
  - Redirect the user to the homepage.

### Implementing Delete

Have a look in _show.blade.php_ and inspect this page in a browser to understand the output that has been generated. The delete button submits the form to a route e.g. _/films/3_ using the _delete_ method.

- Add a route to handle this request. The route should call a `destroy()` method in `FilmController`.

```
Route::delete('/films/{id}', [FilmController::class, 'destroy']);
```

- Add a `destroy()` method in `FilmController`. This should:
  - Use Eloquent to _find_ the correct film using the _id_ value.
  - Call `delete()` on the film object. Again, the documentation has examples (https://laravel.com/docs/eloquent#deleting-models).
  - Redirect the user to the homepage.


## Improving the App
- If the user doesn't put 'film' on the end of the URL, the Laravel welcome page is shown. How can you route the user to the `index()` method of `FilmController` instead?
- Currently, the 'About' route simply return a simple string. How can we return a view instead? Have a look at https://laravel.com/framework/docs/routing#view-routes. 
- Currently, if a film can't be found, we use Laravel's default 404 page. Have a look at creating your own 404 page. See https://laravel.com/framework/docs/errors#custom-http-error-pages for some advice
- It would be good if there was some feedback to the user for the update and delete action. One way of doing this is to use 'flashed session data'. Have a look at https://laravel.com/framework/docs/responses#redirecting-with-flashed-session-data. See if you can notify users when they have been successful in updating/deleting a film. 

