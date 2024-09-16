---
title: "Laravel: basics"
weight: 2
author: Arne Duyver
draft: false
---

# Laravel
## Why Laravel?
- framework -> templates
  - faster
  - libraries
  - MVC model
  - very popular option -> a lot of information
- more secure -> because php is datasensitive
  - built-in security features
  - automatic integration with database

## Install Laravel
##### Edit php.ini file
<br>**line 962**
uncomment (delete semicolon in fromt) extention=zip
<br>**line 930**
uncomment extention=fileinfo
<br>**line 942**
uncomment extention=openssl
<br>**line 944**
uncomment extention=pdo_mysql

##### Add php.exe to path variables (new system variable)
name: PHP <br>
location: C:\xampp\php\php.exe

##### Add dependency manager: [Composer](https://getcomposer.org/download/)
download .exe <br>
add to path: C:\ProgramData\ComposerSetup\bin

##### Install Laravel using Composer
`$ composer global require laravel/installer`

##### Reinstall XAMPP and move project into Dashboard -> Laravel
first delete all files inside the dashboard directory

##### Create laravel project in your project folder
`$ laravel new <projectName>` <br>
starterkit -> none <br>
Pest -> 0 (testing framework, PHPUnit is older) <br>
Git -> no 

db -> mysql (via XAMPP) <br>
default database migrations -> yes (make sure MySQL server is turned on in XAMPP) 

##### Front page is in public/index.php

##### Setup database
inside phpmyadmin -> new database (give same name as laravel project '-' becomes '_').<br>
OR set name in `.env` file

(`$ php artisan migrate`)

creates tables that integrate with laravel !

##### (Start webserver without apache: inside project folder)
`$ php artisan serve`

##### Intro into Laravel TodoList
resources/views/welcome.blade.php <br>
routes/web.php <br>
public/index.php <br>

## Create object and db migration
`$ php artisan make:model <modelName> -m` <br>
`$ php artisan migrate` <br>
check migration in `/database/migrations/` <br>

update migration so it interacts with table correctly (set correct model properties) <br>
model will be in `app/model`
