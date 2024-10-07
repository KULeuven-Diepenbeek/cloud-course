---
title: "REST api consumeren in laravel"
weight: 6
author: Arne Duyver
draft: false
---

Om een ​​Laravel-project te maken dat communiceert met de REST API met behulp van JavaScript in de frontend, moeten we volgende zaken doen:

1. Laravel project aanmaken
2. Gebruik JavaScript om de Flask API aan te roepen
3. Creëer de nodige Laravel controllers
4. Configureer routes

## Laravel project aanmaken
```bash
composer create-project laravel/laravel consumeApi
# change database type to 'mysql' in .env
cd /consumeApi
php artisan migrate
php artisan serve --host 0.0.0.0
```

## Laravel views en Javascript calls
We gebruiken JavaScript (met fetch) om de Flask REST API op te roepen.
Maak een nieuwe lay-out als deze niet bestaat en voeg vervolgens de benodigde HTML-structuur en een tijdelijke aanduiding voor de inhoud toe: `views/layouts/app.blade.php`
```php
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Laravel + Flask API Integration</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/bootstrap/5.1.3/css/bootstrap.min.css">
</head>
<body>
    <div class="container mt-5">
        @yield('content')
    </div>

    <script>
        const apiUrl = 'http://localhost:5000/api/users';  // URL of Flask API

        async function fetchUsers() {
            try {
                let response = await fetch(apiUrl+"?pwd=secret123");
                let data = await response.json();
                displayUsers(data);
            } catch (error) {
                console.error('Error fetching users:', error);
            }
        }

        function displayUsers(users) {
            let usersList = document.getElementById('users-list');
            usersList.innerHTML = '';

            users.forEach(user => {
                let listItem = `<li class="list-group-item">
                    ${user.name} - ${user.email}
                    <button class="btn btn-danger btn-sm float-end" onclick="deleteUser(${user.id})">Delete</button>
                </li>`;
                usersList.innerHTML += listItem;
            });
        }

        async function deleteUser(userId) {
            try {
                let response = await fetch(`${apiUrl}/${userId}`, {
                    method: 'DELETE'
                });
                if (response.ok) {
                    alert('User deleted successfully');
                    fetchUsers();  // Refresh list
                }
            } catch (error) {
                console.error('Error deleting user:', error);
            }
        }

        window.onload = fetchUsers;
    </script>

    @yield('scripts')
</body>
</html>
```

Maak een weergavebestand dat de gebruikers weergeeft en invoerformulieren biedt om nieuwe gebruikers toe te voegen: `views/users.blade.php`

```php
@extends('layouts.app')

@section('content')
    <h1>User List</h1>
    <ul class="list-group" id="users-list">
        <!-- Users will be populated here by JavaScript -->
    </ul>

    <hr>
    <h2>Add User</h2>
    <form id="add-user-form" onsubmit="addUser(event)">
        <div class="mb-3">
            <label for="name" class="form-label">Name</label>
            <input type="text" class="form-control" id="name" required>
        </div>
        <div class="mb-3">
            <label for="email" class="form-label">Email</label>
            <input type="email" class="form-control" id="email" required>
        </div>
        <button type="submit" class="btn btn-primary">Add User</button>
    </form>
@endsection

@section('scripts')
    <script>
        async function addUser(event) {
            event.preventDefault();
            const name = document.getElementById('name').value;
            const email = document.getElementById('email').value;

            try {
                let response = await fetch(apiUrl, {
                    method: 'POST',
                    headers: {
                        'Content-Type': 'application/json'
                    },
                    body: JSON.stringify({ name: name, email: email })
                });

                if (response.ok) {
                    alert('User added successfully');
                    document.getElementById('add-user-form').reset();
                    fetchUsers();  // Refresh user list
                } else {
                    alert('Error adding user');
                }
            } catch (error) {
                console.error('Error adding user:', error);
            }
        }
    </script>
@endsection
```

## Routes definiëren
Configureer vervolgens uw routes en controllers.

Definieer in `routes/web.php` een route om de users view te laden.

```php
use Illuminate\Support\Facades\Route;

Route::get('/users', function () {
    return view('users');
});
```

Open nu de Laravel-frontend (http://localhost:8000/users) en je zou de users view moeten zien, samen met het formulier om users toe te voegen. CRUD-bewerkingen zoals "Toevoegen" en "Verwijderen" worden uitgevoerd via JavaScript-calls naar de Flask API. 

Zorg ervoor dat je Flask applicatie aan het runnen is.


## Opdracht:
Voeg een geslacht toe aan de gebruikers en pas alle nodige code aan in de Flask applicatie en de Laravel applicatie.