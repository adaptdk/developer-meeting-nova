**Laravel Nova - Adapt Developer intro**

- What is Laravel Nova ?
- Why is it not free ?
- Why do we even care about it ? Awesomeness X factor reaching levels of unprecented supremeness



***Step 1***

Select your favorite Vagrant Homestead / Local setup / Valet / LaraDock

Installing the Laravel framework

`composer create-project --prefer-dist laravel/laravel hustlingcars`

Setup your .env file with correct DB settings.

**Installing Nova**

Add the path

```
"repositories": [
    {
        "type": "composer",
        "url": "https://nova.laravel.com"
    }
],
```

Add it to your newly created Laravel composer.json file

```
"require": {
    "php": "^7.1.3",
    "fideloper/proxy": "^4.0",
    "laravel/framework": "5.8.*",
    "laravel/nova": "~2.0"
    },
```

Now run 

`composer update`

To finish the initial setup run the Nova Installer and migrate the DB.

`php artisan nova:install`

`php artisan migrate`

The last step is to add an user (if you are starting from scratch), so you will be able to login.

`php artisan nova:user`





