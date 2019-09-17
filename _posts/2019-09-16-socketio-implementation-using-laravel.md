---
published: true
---
### Prequisite

For successfull implementation of socketio some backend application need to be run. For this some dependencies need to be installed in your machine. Here are the list of dependencies and their installation process given.

- **Redis Server**
- **NodeJs & npm**

##### Redis Server Installation

**For Windows Environment,** go to this link :- [redis server](https://github.com/microsoftarchive/redis/releases) and download _**Redis-x64-3.0.504.zip**_ file. After download unzip it and run the _**redis-server.exe**_.

**For Linux Environment,** command to install redis server :-

Update the apt-get packages :-
```php
sudo apt-get update
```

Next run below command from the terminal to install Redis on your machine :-
```php
sudo apt-get install redis-server
```

Next is to enable Redis to start on system boot. Also restart Redis service once.
```php
sudo systemctl enable redis-server.service
```

**Install Redis PHP Extension :**  if you need to use Redis from PHP application, you also need to install Redis PHP extension on your Ubuntu system. In our case it is mandatory. Run below command to install:
```php
sudo apt-get install php-redis
```

**Test Connection to Redis Server :-**

type below command and see the output,
```php
command :- "redis-cli"
output :- 127.0.0.1:6379>
command :- "ping"
output :- PONG (It output PONG if connection successful)
```

##### NodeJs & npm

Command to install nodeJs & npm on linux environment :
```php
sudo apt-get install nodejs
sudo apt-get install npm
```

For checking the version of nodeJs and npm run the following commands in terminal:
```php
nodejs -v
npm -v
```

##### Laravel Project Dependencies

Go insode of your laravel project directory and install the following dependencies :

_**socket.io**_
```php
npm install socket.io --save
```

To check, if installed, type in:
```php
npm list socket.io
```

_**ioredis**_
```php
npm install ioredis --save
```

To check, if installed, type in:
```php
npm list ioredis
```

Next install predis package of laravel
```php
composer require predis/predis
```

##### Setup Broadcasting Driver

Go insode of your laravel project directory and open _**.env**_ file and setup the broadcasting driver.
```php
BROADCAST_DRIVER=redis
```

If you are using laravel 6.*, then you need to set the REDIS_CLIENT to predis in config/database.php. By default its set to phpredis.
```php
'client' => env('REDIS_CLIENT', 'predis'),
```

##### Creating Larevel Event For Broadcasting Any Event

Inside of your laravel project run the following command in terminal to create an event called UserBroadcast(it can be anything).
```php
php artisan make:event UserBroadcast
```
The event class will be placed into app/Events folder. After creating the event you have to implement ShouldBroadcastNow in UserBroadcast.
```php
class UserBroadcast implements ShouldBroadcastNow
```
Below is the full UserBroadcast event.
```php
<?php

namespace App\Events;

use App\User;
use Illuminate\Broadcasting\Channel;
use Illuminate\Queue\SerializesModels;
use Illuminate\Broadcasting\PrivateChannel;
use Illuminate\Broadcasting\PresenceChannel;
use Illuminate\Foundation\Events\Dispatchable;
use Illuminate\Broadcasting\InteractsWithSockets;
use Illuminate\Contracts\Broadcasting\ShouldBroadcastNow;

class UserBroadcast implements ShouldBroadcastNow
{
    use Dispatchable, InteractsWithSockets, SerializesModels;

    public $user;

    /**
     * Create a new event instance.
     *
     * @return void
     */
    public function __construct(User $user)
    {
        $this->user = $user;
    }

    /**
     * Get the channels the event should broadcast on.
     *
     * @return \Illuminate\Broadcasting\Channel|array
     */
    public function broadcastOn()
    {
        return new Channel('user-channel');
    }
}
```
In this event I created a channel called user-channel in which data will be broadcasted.

##### Creating Controller Class and Api Route to Fire the UserBroadcast Event

Run the following command in terminal to create a controller called UserController(it can be anything).
```php
php artisan make:controller Api/V100/UserController
```
Here is the full UserController
```php
<?php

namespace App\Http\Controllers\Api\V100\user;

use App\User;
use Illuminate\Http\Request;
use App\Events\UserBroadcast;
use App\Http\Controllers\Controller;

class UserController extends Controller
{
    /**
     * Store a newly created resource in storage.
     *
     * @param  \Illuminate\Http\Request  $request
     * @return \Illuminate\Http\Response
     */
    public function store(Request $request)
    {
        $rules = [
            'name' => 'required',
            'email' => 'required|email',
        ];
        $this->validate($request, $rules);

        $user = User::create([
            'name' => $request->name,
            'email' => $request->email,
            'password' => bcrypt('qwerty'),
        ]);

        /** Created User Broadcast Event */
        try {
            event(new UserBroadcast($user));
        } catch (\Exception $e) {
            /** Do Nothing */
        }

        return ["data" => $user];
    }
}
```

Next, open the routes/api.php file and create a route.
```php
Route::resource("users", "user\UserController");
```
After that a change need to be made in app\Providers\RouteServiceProvider class. Here is the full RouteServiceProvider class.
```php
<?php

namespace App\Providers;

use Illuminate\Foundation\Support\Providers\RouteServiceProvider as ServiceProvider;
use Illuminate\Support\Facades\Route;

class RouteServiceProvider extends ServiceProvider
{
    /**
     * This namespace is applied to your controller routes.
     *
     * In addition, it is set as the URL generator's root namespace.
     *
     * @var string
     */
    protected $namespace = 'App\Http\Controllers';
    protected $namespace_v1 = 'App\Http\Controllers\Api\V100';

    /**
     * Define your route model bindings, pattern filters, etc.
     *
     * @return void
     */
    public function boot()
    {
        //

        parent::boot();
    }

    /**
     * Define the routes for the application.
     *
     * @return void
     */
    public function map()
    {
        $this->mapApiRoutes();

        $this->mapWebRoutes();

        //
    }

    /**
     * Define the "web" routes for the application.
     *
     * These routes all receive session state, CSRF protection, etc.
     *
     * @return void
     */
    protected function mapWebRoutes()
    {
        Route::middleware('web')
             ->namespace($this->namespace)
             ->group(base_path('routes/web.php'));
    }

    /**
     * Define the "api" routes for the application.
     *
     * These routes are typically stateless.
     *
     * @return void
     */
    protected function mapApiRoutes()
    {
        Route::prefix('api/v1.0.0')
             ->middleware('api')
             ->namespace($this->namespace_v1)
             ->group(base_path('routes/api.php'));
    }
}
```