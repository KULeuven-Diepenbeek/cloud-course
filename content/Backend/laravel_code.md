---
title: "Laravel: code and commands"
weight: 3
author: Arne Duyver
draft: false
---

## Important commandline commands
```bash
# Rebuild project
composer update --no-scripts

# Create new Laravel project
laravel new <projectName>

# Migrate creation tables
php artisan migrate

# Start project op poort 8000
php artisan serve

# Create new Laravel model with options
php artisan make:model
# Create just a new Laravel model
php artisan make:model <modelName>

# Create new Laravel migration 
php artisan make:migration create_<modelName>withS_table

# Create new Laravel controller
php artisan make:controller <controllerName>

# Create new Laravel seeder
php artisan make:seeder <seederName>
# Seed db 
php artisan db:seed --class=<SeederClassName>

# Generate a factory for a model
php artisan make:factory <FactoryName> --model=<ModelName>
```

## Connect to supabase

Enable/uncomment extentions via `php.ini` file:
- pdo_pgsql.so
- pgsql.so

Set database default in `confid->database.php`:
- `'default' => env('DB_CONNECTION', 'pgsql'),`

Edit `.env` file:
```yml
DB_CONNECTION=pgsql
DB_URL=<supabase->project->configuration->Database->connectionString->URI>
DB_PASSWORD=supabasePassword
```

## [routes/web.php](https://laravel.com/docs/11.x/routing)
Hier worden alle endpoints van je websites gedeclareerd (Meer info rond: [requests](https://laravel.com/docs/11.x/requests), [responses](https://laravel.com/docs/11.x/responses)):
```php
// Schrijf de functie die moet uitgevoerd worden rechtstreeks in de `get`-methode 
// get request naar endpoint '/'
Route::get('/', function () {
    // Do stuff
    //...
    // Geef een view terug met eventueel extra data die kan gebruikt worden in de blade template
    return view('home', ['posts' => $posts]);
});

// Of je kan een bepaald endpoint doorgeven naar een controller klasse
// Post request
Route::post('/postendpoint', [ExampleController::class, 'methodInsideControllerClass']);
// Get request
Route::get(...);
// Put request
Route::put(...);
// Update request
Route::update(...);
// Delete request
Route::delete(...);
```

## [Controller](https://laravel.com/docs/11.x/controllers) class example: app/Http/Controllers
Bundel wat er moet gebeuren wanneer verschillende endpoints gebruikt worden die te maken hebben met bepaalde modellen:

```php
<?php

namespace App\Http\Controllers;

use App\Models\User;
use Illuminate\Http\Request;
use Illuminate\Validation\Rule;

class ExampleController extends Controller
{
    public function methodInsideControllerClass(){
      // do stuff
      // Return een blade view
      return view('home');
    }
    
    public function redirectExample(Request $request){
        // Get the values inside a request and validate them
        $incomingFields = $request->validate([
            'name' => ['required', Rule::unique('users','name')],
            'email' => ['required'],
            'password' => ['required']
        ]);

        // show more info on a php object
        //dd($request)
        
        // Create a php obeject
        $example = Example::create($incomingFields);

        //Login to session as a user
        auth()->login($user);

        //Logout
        //auth()->logout();

        // Do een redirect naar een ander endpoint
        return redirect("/");
    }

    public function getAllEntriesOfModelInDb(){
        // Retrieve all users from the database
        $examples = Example::all();

        // Return the users data (for example, as JSON)
        return response()->json($examples);
    }
}
```

## Blade views en syntax

[Blade](https://laravel.com/docs/11.x/blade) en [views](https://laravel.com/docs/11.x/views) worden dynamisch gemaakt via een aantal specifieke code syntaxen voor `if`-`else` statements, `for`-lussen, importeren van andere views ...

```html
<!-- template.blade.php -->
<h1>{{ phpVariableExample }}</h1>
@include('otherviewtoinclude')
<div>
@yield('contentToBeIncluded')
</div>


<!-- actualView.blade.php -->
@extends('template')
@section('body')
<h3>Html to be inserted</h3>
@endsection

<!-- forifelseExample.blade.php -->
<h1>For loop example:</h1>
@for ($i = 0; $i < 10; $i++)
    The current value is {{ $i }}
@endfor
 
@foreach ($users as $user)
    <p>This is user {{ $user->id }}</p>
@endforeach

@while (true)
    <p>I'm looping forever.</p>
@endwhile

<h1>If Else example:</h1>
@if (count($records) === 1)
    I have one record!
@elseif (count($records) > 1)
    I have multiple records!
@else
    I don't have any records!
@endif
```

Avoid [Cross-site request forgeries](https://laravel.com/docs/11.x/csrf) errors in forms met `@csrf`
```php
<form action="/image" method="POST" enctype="multipart/form-data">
    //IMPORTANT
    @csrf
    <input type="file" name="image" accept="image/*">
    <button type="submit">Upload</button>
  </form>
```

## Models, migrationtables en factories
Bij het aanmaken van modellen kan je rechtstreeks een link maken met de database aan de hand van [migration tabellen](https://laravel.com/docs/11.x/migrations).
Bovendien kan je een factory gebruiken om dummy data aan te brengen in de database via de commandline. Hieronder een voorbeeld:
#### app/Models/Test.php
```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class Test extends Model
{
    // To be able to use a factory
    use HasFactory;

    /**
     * The attributes that are mass assignable.
     *  Namen komen overeen met de kolomnamen in de database
     * @var array<int, string>
     */
    protected $fillable = [
        'testname',
        'testemail',
        'testnumber',
        'testpassword',
    ];
}
```
#### database/migrations/2024_05_06_084113_create_tests_table.php
```php
<?php

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
        Schema::create('tests', function (Blueprint $table) {
            $table->id();
            $table->timestamps();
            $table->string('testname');
            $table->string('testemail');
            $table->integer('testnumber');
            $table->string('testpassword');
        });
    }

    /**
     * Reverse the migrations.
     */
    public function down(): void
    {
        Schema::dropIfExists('tests');
    }
};
```

#### database/factories/TestFactory.php
```php
<?php

namespace Database\Factories;

use Illuminate\Database\Eloquent\Factories\Factory;

/**
 * @extends \Illuminate\Database\Eloquent\Factories\Factory<\App\Models\Test>
 */
class TestFactory extends Factory
{
    /**
     * Define the model's default state.
     *
     * @return array<string, mixed>
     */
    public function definition(): array
    {
        return [
            // 'testname' => $this->faker->name,
            'testname' => $this->faker->randomElement(['Mark', 'Tom', 'Kevin']),
            'testemail' => $this->faker->numberBetween(1000, 9999),
            'testnumber' => $this->faker->numberBetween(1000, 9999),
            'testpassword' => bcrypt('password'),
        ];
    }
}
```
## Seeder
Om effectief dummy data in de database te steken gebruiken we seeders:
`$ php artisan db:seed --class=DatabaseSeeder`

#### database/seeders/DatabaseSeeder.php
```php
<?php

namespace Database\Seeders;

use App\Models\Test;
// use Illuminate\Database\Console\Seeds\WithoutModelEvents;
use App\Models\User;
use Illuminate\Database\Seeder;

class DatabaseSeeder extends Seeder
{
    /**
     * Seed the application's database.
     */
    public function run(): void
    {
        // Clear test table in database
        Test::truncate();
        // Call the TestSeeder
        $this->call(TestSeeder::class);
        // Generate 10 test records using the factory
        Test::factory()->count(10)->create();
    }
}
```

#### database/seeders/TestSeeder.php
```php
<?php

namespace Database\Seeders;

use Illuminate\Database\Seeder;
use Illuminate\Support\Facades\DB;
use Illuminate\Database\Console\Seeds\WithoutModelEvents;

class TestSeeder extends Seeder
{
    /**
     * Run the database seeds.
     */
    public function run(): void
    {
        DB::table('tests')->insert([
            'testname' => 'Test Name ',
            'testemail' => 'test@example.com',
            'testnumber' => rand(1000, 9999),
            'testpassword' => bcrypt('password'),
            'created_at' => now(),
            'updated_at' => now(),
        ]);
    }
}
```

## De public files
Waar moet je de files plaatsen die beschikbaar moeten zijn aan de client-side?<br>
-> in de `public` folder

```
public
|-----> assets
      |-------> styles.css
      |-------> script.js
      |-------> example.jpg
```
Inside blade views:
```php
<!DOCTYPE html>
<html lang="en">
<head>
  <title>Public Example</title>
  <link rel="stylesheet" type="text/css" href="{{ url('/assets/styles.css') }}" />
</head>
<body>
  <h1>Example</h1>
  <img src="{{url('/assets/example.jpg')}}" alt="image" width="200px"/>
  <script src="{{url('/assets/script.js')}}" type="text/javascript"></script>
</body>
</html>
```

## [Laravel sessions](https://laravel.com/docs/11.x/session)
In de `config/sessions.php` kan je bepalen hoe sessionvariabelen opgeslagen moeten worden en bepaalde instellingen voor sessies wijzigen. Je kan bijvoorbeeld instellen hoe lang een sessie kan duren.
```php
Session::put('variableName', $value); // To save variable in a session
Session::get('variableName'); // To get a variable in a session
```

## [Error Handling](https://laravel.com/docs/11.x/errors)
zie link

## [Logging](https://laravel.com/docs/11.x/logging)
```php
dd($variable);
```