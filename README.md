**Laravel Nova - Adapt Developer intro**

- What is Laravel Nova ?
- Why is it not free ?
- Why do we even care about it ? Awesomeness X factor reaching levels of unprecented supremeness



***Step 1***

Select your favorite Vagrant Homestead / Local setup / Valet / LaraDock

Installing the Laravel framework

`composer create-project --prefer-dist laravel/laravel hustlingcars`

Next change directory into your fresh new project

`cd hustlingcars`

Setup your .env file with correct DB settings. You can select a MySQL or a Postgres. We will not judge you in any case.

***Homestead users***

If you are using Homestead, you can just add public folder along with a domain the DB name to your Homestead and provision the box. Add needed part to your homestead.yml file and reload your box using:

`vagrant reload --provision`


**Installing Nova**

Add the repositories. 

`composer repositories.nova composer https://nova.laravel.com`

Authenticate yourself against the Laravel Nova repository. Use the credentials you have used when you bought Laravel Nova at https://nova.laravel.com

`composer config http-basic.nova.laravel.com "NOVA_USERNAME" "NOVA_PASSWORD"`


Add it to your newly created Laravel composer.json file

`composer require "laravel/nova:~2.0"`

To finish the initial setup run the Nova Installer and migrate the DB.

`php artisan nova:install`

`php artisan migrate`

The last step is to add an user (if you are starting from scratch), so you will be able to login.

`php artisan nova:user`


Step 2


