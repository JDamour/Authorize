
## Step 1: Laravel 5.6 Installation

    composer create-project --prefer-dist laravel/laravel blog

## Step 2: Spatie Package Installation

    composer require spatie/laravel-permission
Now open config/app.php file and add service provider and aliase.    
config/app.php
        
        'providers' => [
    
    	....
    
    	Spatie\Permission\PermissionServiceProvider::class,
    
         ],

We can also custom changes on Spatie package, so if you also want to changes then you can fire bellow command and get config file in config/permission.php.

    php artisan vendor:publish --provider="Spatie\Permission\PermissionServiceProvider" --tag="config"                 

    php artisan migrate
    
Get install laravelcollective/html for Form class 

    "require": {
    "laravelcollective/html": "~5.0"
    
    },
    or use 
    composer require laravelcollective/html
after fire composer update command After Update composer, open config/app.php and add this line to the ‘providers’ array:

    'Collective\Html\HtmlServiceProvider',
Next, add these lines to the ‘aliases’ array:

    'FORM' => 'Collective\Html\FormFacade',
    'HTML' => 'Collective\Html\HtmlFacade',

## Step 3: Create Table using Migrations

In this step we have to create three migrations for as listed bellow tables:

1. users

2. products

3. roles

4. permissions

5. model_has_permissions

6. model_has_roles

7. role_has_permissions

         php artisan vendor:publish --provider="Spatie\Permission\PermissionServiceProvider" --tag="migrations"
    
         php artisan make:migration create_products_table
## Step 4: Create Table Models
        php artisan make:model Product
1. app/User.php
2. app/Product.php
 
## Step 5: Add Middleware
Spatie package provide it's in-built middleware that way we can use it simply and that is display as bellow:
1. role
2. permission

So, we have to add middleware in Kernel.php file this way :

app/Http/Kernel.php

            protected $routeMiddleware = [
        	....
        	'role' => \Spatie\Permission\Middlewares\RoleMiddleware::class,
        	'permission' => \Spatie\Permission\Middlewares\PermissionMiddleware::class,
        ]
        ....
## Step 6: Create Seeder For Permissions

In this step we will create seeder for permissions, Right now we have fixed permission so we create using seeder as listed bellow, but if you can add more permission as you want:

1.role-list

2.role-create

3.role-edit

4.role-delete

5.product-list

6.product-create

7.product-edit

8.product-delete

Make migrate cmd 

Before modify AppServiceProvider.php

    use Illuminate\Support\Facades\Schema;
    
    public function boot()
    {
        Schema::defaultStringLength(191);
    }

So, first create seeder using bellow command:

    php artisan make:seeder PermissionTableSeeder
Don't forget to import class
    
    use Spatie\Permission\Models\Permission;
    
After this we have to run bellow command for run PermissionTableSeeder seeder:

    php artisan db:seed --class=PermissionTableSeeder

## Step 7: Create Authentication if it needed
   
    php artisan make:auth
    
## Step 8: Create Routes
   
routes/web.php

    Route::group(['middleware' => ['auth']], function() {
        Route::resource('roles','RoleController');
        Route::resource('users','UserController');
        Route::resource('products','ProductController');
    });
## Step 9: Add Controllers

app/Http/Controllers/UserController.php
app/Http/Controllers/ProductController.php
app/Http/Controllers/RoleController.php

## Step 10: Add Blade Files
   
resources/views/layouts/app.blade.php
Remaining is to work on blades (Product,roles,users).
## Step 11: Handle Exertion Error
Now, in this step we will handle exertion. if you don't have a permission and try to access that page using browser url then you can give message as like bellow:

add/Exceptions/Handler.php

    ......
    public function render($request, Exception $exception)
    {
        if ($exception instanceof \Spatie\Permission\Exceptions\UnauthorizedException) {
            return response()->json(['User have not permission for this page access.']);
        }
     
        return parent::render($request, $exception);
    }
    ....

     