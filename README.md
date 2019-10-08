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

#### Homestead users

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

#### Car

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


### Migration setup for Seller

A seller can work shifts in different Dealership locations. First we just setup our plain seller model by adding a name to our migration file in the `Schema::create` part.

`$table->string('name'); `
 
 Your Seller model should look like this: 
 
```
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateSellersTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('sellers', function (Blueprint $table) {
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
        Schema::dropIfExists('sellers');
    }
}
```

### Dealership to Seller - Many to many


A seller can be attached to many dealership locations as well as a dealshipership can have many sellers. 

#### Many-to-many relationship migration

When you create a model you use a singlar term, e.g. **Brand**. If you look into you migration files all the tables are created with plural name, in the case **brands**

#### Adding a pivot table

To handle many-to-many relationship we use a pivot table. To create such a table we need to create the table ourselves using artisan: 

`php artisan make:migration create_dealership_seller_table --create=dealership_seller`

To get the full force of pivot tables in Laravel we use the singular form of each model with a underscore inbetween. The model table must be ordered in an **alphabetical order**, so *dealership* comes before *seller*.

To the newly created migration file you add the following two columns : 

```
$table->unsignedBigInteger('dealership_id');
$table->unsignedBigInteger('seller_id');
```

Your migration file should look like this : 

```
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateDealershipSellerTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('dealership_seller', function (Blueprint $table) {
            $table->bigIncrements('id');
            $table->unsignedBigInteger('dealership_id');
            $table->unsignedBigInteger('seller_id');
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
        Schema::dropIfExists('dealership_seller');
    }
}
```

### Migration setup for Customer

We make a very simple customer model, so we just need a name

`$table->string('name'); `

Your migration file will look like this : 

```
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateCustomersTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('customers', function (Blueprint $table) {
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
        Schema::dropIfExists('customers');
    }
}

```

### Migration setup for Order
 
We want the following info for an order

- Who bought the car 
- Which car
- Who sold the car
- Which dealership got the order
- How long can the customer bitch about the car
 
So we add the following fields for an order : 

```
$table->unsignedBigInteger('customer_id');
$table->unsignedBigInteger('car_id');
$table->unsignedBigInteger('seller_id');
$table->unsignedBigInteger('dealership_id');
$table->dateTime('warranty_expires');
```

Your migration file for an order will look like this

```
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateOrdersTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('orders', function (Blueprint $table) {
            $table->bigIncrements('id');
            $table->unsignedBigInteger('customer_id');
            $table->unsignedBigInteger('car_id');
            $table->unsignedBigInteger('seller_id');
            $table->unsignedBigInteger('dealership_id');
            $table->dateTime('warranty_expires');
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
        Schema::dropIfExists('orders');
    }
}
```

### Migrating

Migrating is done by calling the artisan command like this

`artisan migrate`

This will create the tables in your database. It will also writes which migration files has been added in the *migration*-table.

If something goes wrong or you have forgot some of the fields you can do a rollback. 

`artisan migrate:rollback`

#### More info on fresh, refresh and multiple rollbacks. 

For additional info on how you run migrations, do a full refresh and related info read more at [Offical Laravel Documentation](https://laravel.com/docs/6.x/migrations#rolling-back-migrations)



## Step 3

### Laravel models explained

When you create models in Laravel using Artisan make:model they are stored in the `app` folder. 

Your models are born quite empty like this : 

```
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Brand extends Model
{
	// 
}
```

### Make mass-assignment possible

The framework guards attributes on your Models, so you need to tell which attributes are available for changing / setting data on.

You have the following options for this:

* `protected $guarded = []` tells explicitely which attributes are guarded making all other attributes changeable.
* `protected $fillable = []` tells which attribute can be changed. All others are guarded.


### Adding one-to-many relationship

A brand can be possible for many different cars, so we define this as a one-to-many relationship. On the **Brand** model we add the following function. 

**NOTE:** The function *cars()* is in plural as a brand can have multiple cars. 

```
public function cars() {
	return $this->hasMany(Car::class);
}
```

#### Brand model

```
<?php
namespace App;

use Illuminate\Database\Eloquent\Model;

class Brand extends Model
{
    /**
     *
     * @var array
     */
    protected $fillable = [
        'name',
    ];

    /**
     * @return \Illuminate\Database\Eloquent\Relations\BelongsTo
     */
    public function cars()
    {
        return $this->hasMany(Car::class);
    }
}
```

#### Color model

The Color model does not differ from a Brand (except from the class name), so it looks like this: 

```
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Color extends Model
{
    protected $fillable = [
        'name'
    ];

    /**
     * @return \Illuminate\Database\Eloquent\Relations\BelongsTo
     */
    public function cars() {
        return $this->hasMany(Car::class);
    }
}
```

### Car 

You only need to add two field to `$fillable` as the rest will be added through the relationships.

```
protected $fillable = [
    'model_type',
    'description'
];
```

#### Car BelongsTo relationship

We add BelongsTo for the following : Brand, Color, Dealership and Order. You will add 4 functions  that looks like the following. Just change the function name and class name.


```
public function brand()
{
    return $this->belongsTo(Brand::class);
}
``` 

### Car model 

```
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Car extends Model
{
    protected $fillable = [
        'model_type',
        'description'
    ];


    /**
     * @return \Illuminate\Database\Eloquent\Relations\HasOne
     */
    public function brand()
    {
        return $this->belongsTo(Brand::class);
    }

    /**
     * @return \Illuminate\Database\Eloquent\Relations\HasOne
     */
    public function color()
    {
        return $this->belongsTo(Color::class);
    }

    /**
     * @return \Illuminate\Database\Eloquent\Relations\HasOne
     */
    public function dealership()
    {
        return $this->belongsTo(Dealership::class);
    }

    public function order()
    {
        return $this->belongsTo(Order::class);
    }
}
```

### Adding a many-to-many relationship

A dealership will have many different sellers, but seller can also be selling car from one dealership on mondays and another on fridays, so we need to define a many-to-many relationship on our model. 

**NOTE:** You already created a table for this earlier called `dealership_seller` which will hold *dealership_ids* and *seller_ids*.


### Many-to-many relationship in code

#### Dealership

On the Dealership model you add the following 

```
public function sellers() {
    return $this->belongsToMany(Seller::class);
}
```

##### Add orders relationship

You would also like to have a relationship where a Dealership can have a relationship to Orders, so you can get the orders that a single Dealership has. 

Add the following: 

```
public function orders() {
	return $this->hasMany(Order::class);
}
```

#### Dealership model result

```
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Dealership extends Model
{
    /**
     * The attributes that are mass assignable.
     *
     * @var array
     */
    protected $fillable = [
        'internal_name',
        'street_name',
        'city'
    ];

    public function sellers() {
        return $this->belongsToMany(Seller::class);
    }

    public function orders() {
        return $this->hasMany(Order::class);
    }
}
```


#### Seller

You basically add the same on the Seller model, just pointing toward the Dealership.

```
public function dealership() {
    return $this->belongsTo(Dealership::class);
}
```

#### Seller model result

```
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Seller extends Model
{
    /**
     * @var array
     */
    protected $fillable = [
        'name',
    ];

    public function dealerships() {
        return $this->belongsToMany(Dealership::class);
    }
}
```

### Customer

Our customer is a very simple model, so we add *name* to `$fillable` and add **Order** as a relationship. Your model will look like this: 

```
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Customer extends Model
{
    /**
     * @var array
     */
    protected $fillable = [
        'name'
    ];

    public function orders() {
        return $this->hasMany(Order::class);
    }
}
```

### Order

On **Order** we only got the `warranty_expires` field and belongTo relationship for Car, Customer, Dealership and Seller.

Your model will look like this: 

```
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Order extends Model
{
    /**
     * @var array
     */
    protected $fillable = [
        'warranty_expires',
    ];

    public function customer() {
        return $this->belongsTo(Customer::class);
    }

    public function car() {
        return $this->belongsTo(Car::class);
    }

    public function seller() {
        return $this->belongsTo(Seller::class);
    }

    public function dealership() {
        return $this->belongsTo(Dealership::class);
    }
}
```
## Step 4


 
### Factories and seeds

A factory and seeding is an easy way to mock a lot of test data into your model. Factories are designed to handle a single model (without or with relationships) and seeders take care of using the factories or other data to actually do the mocking.

#### Creating factories and table seeders

As with much else we use the command line for this. 

For factories 

`artisan make:factory ModelnameFactory` 

For seeder

`artisan make:seeder ModelnameTableSeeder` 

This creates files in the folders `database/factories/` and `database/seeds/`.

### Brand seeder

Create the seeder : `artisan make:seeder BrandsTableSeeder`

Since we know the data we want to use, we can do it by only using the seeder. The BrandsTableSeeder will look like this: 

```
<?php

use App\Brand;
use Illuminate\Database\Seeder;

class BrandsTableSeeder extends Seeder
{
    /**
     * Run the database seeds.
     *
     * @return void
     */
    public function run()
    {
        $brandNames = [
            'Alfa Romeo', 'Aston Martin', 'Audi', 'Bentley', 'BMW', 'Bugatti', 'Chrysler', 'Citroen', 'Ferrari',
            'Fiat', 'Honda', 'Hyundai', 'Kia', 'Lamborghini', 'Lancia', 'Land Rover', 'Maserati', 'McLaren',
            'Mercedes-Benz', 'Mini', 'Mitsubishi', 'Opel', 'Peugeot', 'Porsche', 'Renault', 'Rolls-Royce', 'Suzuki',
            'Toyota', 'Volkswagen'
        ];

        // If you run this multiple times, we truncate it first.
        Brand::truncate();

        foreach ($brandNames as $brandName) {
            Brand::create([
                'name' => $brandName
            ]);
        }
    }
}
```

### Color seeder

Create the seeder : `artisan make:seeder ColorsTableSeeder`

Pretty much the same as with the BrandsTableSeeder. Looks like this: 

```
<?php

use App\Color;
use Illuminate\Database\Seeder;

class ColorsTableSeeder extends Seeder
{
    /**
     * Run the database seeds.
     *
     * @return void
     */
    public function run()
    {

        $colors = [
            'Unclean Oven Black', 'Given Up Grey', 'Mold Green', 'Poop Brown', 'Bad Rash Red',
            'Trump Orange','Black Eye Violet', 'Morning Pee Yellow', 'Blue Waffle Blue', 
            'Coffee Stain White',
        ];

        // If you run this multiple times, we truncate it first.
        Color::truncate();

        foreach ($colors as $color) {
            Color::create([
                'name' => $color
            ]);
        }
    }
}
```

### Dealership factory and seeder

#### Factory

First we create the factory 

`artisan make:factory DealershipFactory`

We want to add data into following attributes : *internal_name*, *street_name* and *city*. 

The factories you create using *artisan* use the faker package. For more info [read about The Faker package](https://github.com/fzaninotto/Faker)

Your factory will look like this :

```
<?php

/** @var \Illuminate\Database\Eloquent\Factory $factory */

use App\Dealership;
use Faker\Generator as Faker;

$factory->define(Dealership::class, function (Faker $faker) {

    return [
        'internal_name' => $faker->state . ' ' . $faker->cityPrefix,
        'street_name' => $faker->streetAddress,
        'city' => $faker->city
    ];
});
```

#### Seeder

Create the seeder for the dealership

`artisan make:seeder DealershipsTableSeeder`

Since all the heavy lifting is done in the factory, we just need to initate it.

```
<?php

use App\Dealership;
use Illuminate\Database\Seeder;

class DealershipsTableSeeder extends Seeder
{
    /**
     * Run the database seeds.
     *
     * @return void
     */
    public function run()
    {
        $count = 12;
        factory(Dealership::class, $count)->create();
    }
}

```


### Car factory and seeder






### Show tables with data



## Step 5

### Nova® Magic


### Nova resources

Creating Nova resources are pretty easy. Just use artisan for it:

`artisan nova:resource Brand`

This will create a file with the Brand resource as `/app/Nova/Brand.php` with a lot of boilerplate code. If you have setup your domain, so you can access the site using your browser go to : `http://yourdomain.test/nova`

Log in to Nova with your user and you will in the sidebar menu see the **Brand** menu item.


#### Registering resources

If you leave the files in the `/app/Nova/`-folder they will automatically be registered.


### Adding fields

In the `Brand.php` find the following fields() function that should look like this:

```
/**
 * Get the fields displayed by the resource.
 *
 * @param  \Illuminate\Http\Request  $request
 * @return array
 */
public function fields(Request $request)
{
    return [
        ID::make()->sortable(),
    ];
} 
```

We want to Add the name as a field, so we add the class in the use-statements: 

`use Laravel\Nova\Http\Requests\NovaRequest;`

And in the fields-function, below the `ID::make()->sortable(),` add a new line with the following statement: 

`Text::make('Brand name', 'name')->sortable()`

The first parameter of the make-function defines the name (or the DB-column if the second parameter is not used) and the second parameter defines the column name in the DB-table.


You will now have the possibility to make all CRUD operations through the Nova-interface with that column. Laravel Nova has quite a lot of field types already built in to it: [Laravel Nova field types](https://nova.laravel.com/docs/2.0/resources/fields.html#field-types)

### Create the rest of Nova resources

```
artisan nova:resource Color
 artisan nova:resource Customer
```

### Nova relations

## Step 6

### Admin interface tour

### Data usage: API / Frontend

### Additional packages




