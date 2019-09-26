# Laravel Nova - Adapt Developer intro

- What is Laravel Nova ?
- Why is it not free ?
- Why do we even care about it ? Awesomeness X factor reaching levels of unprecented supremeness



## Step 1

Select your favorite Vagrant Homestead / Local setup / Valet / LaraDock

### Installing the Laravel framework

`composer create-project --prefer-dist laravel/laravel hustlingcars`

Next change directory into your fresh new project

`cd hustlingcars`

Setup your .env file with correct DB settings. You can select a MySQL or a Postgres. We will not judge you in any case.

###Homestead users

If you are using Homestead, you can just add public folder along with a domain the DB name to your Homestead and provision the box. Add needed part to your homestead.yml file and reload your box using:

`vagrant reload --provision`


## Installing Nova

### Add Nova through composer

Add the repositories. 

`composer config repositories.nova composer https://nova.laravel.com`

Authenticate yourself against the Laravel Nova repository. Use the credentials you have used when you bought Laravel Nova at https://nova.laravel.com

`composer config http-basic.nova.laravel.com "NOVA_USERNAME" "NOVA_PASSWORD"`

#### Add auth.json to your .gitignore
This will create an auth.json file in the same folder as composer.json with your info. You should not commit it to your code repository.


Add it to your newly created Laravel composer.json file

`composer require "laravel/nova:~2.0"`

To finish the initial setup run the Nova Installer and migrate the DB.

`php artisan nova:install`

`php artisan migrate`

The last step is to add an user (if you are starting from scratch), so you will be able to login.

`php artisan nova:user`


## Step 2

### Our model

Our new company will have the following models: 

- Brand (the brand of cars)
- Color (color of the car)
- Seller (our staff)
- Car (the cars we have for sale)
- Dealership (physical places where the cars are on display)
- Customer (the schmocks that will buy the used cars)
- Order (the final document for the customer)

### Setting up the models 


Now we can start setting up our model by using Laravels CLI tool - Artisan.

By add the `-m` it also creates migration-files for creating the database schema.

```
php artisan make:model Brand -m
php artisan make:model Color -m
php artisan make:model Car -m
php artisan make:model Dealership -m
php artisan make:model Seller -m
php artisan make:model Customer -m
php artisan make:model Order -m
```

This will create 7 models in your `/app` folder and the same number of migration files in `/database/migrations` folder. 

### Migration setup for Brand and Color

#### Brand

Brand and color are simple data tables that will be part of the description of the car. 

Add a name field for the brand in the migration file for the brand. 

Since most of the boilerplate code is done for you, only add the following line in between the `Schema::create` part: 

`$table->string('name');`

Your migration file should look like this:

```
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateBrandsTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('brands', function (Blueprint $table) {
            $table->bigIncrements('id');
            $table->string('name');
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::dropIfExists('brands');
    }
}
 
```

#### Color

Add the same column to Color migration file

`$table->string('name');`

Your color migration file should look like this: 

```
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateColorsTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('colors', function (Blueprint $table) {
            $table->bigIncrements('id');
            $table->string('name');
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::dropIfExists('colors');
    }
}
```

### Migration setup for Dealership

The car dealship is a physical place for the customers to see the cars for sale. There can be multiple points of sale. 

- internal_name (string)
- street_name (string)
- city (string)

Translated into code add this to your `Schema-create` part

```
$table->string('internal_name');
$table->string('street_name');
$table->string('city');
```

Your migration file will look like this : 

```
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateDealershipsTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('dealerships', function (Blueprint $table) {
            $table->bigIncrements('id');
            $table->string('internal_name');
            $table->string('street_name');
            $table->string('city');
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::dropIfExists('dealerships');
    }
}
```


### Migration setup for Car

####Car

Car model will contain relationships to Brand and Color. A car can be of a brand and have one color (for simplicity we selected that it only has a one color).

As it is a physical item, we need to place it in a Dealership.

We also need a model type, price and a description for it.

- brand_id
- color_id
- dealership_id
- model_type
- description
- sold

In your migration file add these fields: 

```
$table->unsignedBigInteger('brand_id');
$table->unsignedBigInteger('color_id');
$table->unsignedBigInteger('dealership_id');
$table->string('model_type');
$table->longText('description');
$table->boolean('sold')->default(false);
```

As the migrations are made with *bigIncrements* field, we also use * unsignedBigInteger* as foreign field types.

We also set up a boolean value to tell whether a car is sold or not. Default is set to false.

Your cars table will look like this :

```
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateCarsTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('cars', function (Blueprint $table) {
            $table->bigIncrements('id');
            $table->unsignedBigInteger('brand_id');
            $table->unsignedBigInteger('color_id');
            $table->unsignedBigInteger('dealership_id');
            $table->string('model_type');
            $table->longText('description');
            $table->boolean('sold')->default(false);
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::dropIfExists('cars');
    }
}
```

 
