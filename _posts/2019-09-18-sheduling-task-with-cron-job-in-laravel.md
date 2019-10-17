---
published: true
---
##### Introduction

Sometimes applications require some task to be run periodically on the server. It can be sending push notification, trashing unwanted data from database, creating backups etc. This can be done easily using cron job in laravel. This project has the code to send email using scheduler of laravel.

##### Cron

Cron is a time-based task scheduler in Unix/Linux operating systems. It executes shell commands at a pre-specified time period. Cron uses a configuration file known as Crontab to handle the task scheduling process.

Crontab contains all the Cron jobs related to a specific task. Cron jobs are composed of two parts, the Cron expression, and a shell command that needs to be run.

```php
* * * * * command/to/run
```

In the Cron expression above (* * * * *), each field is an option for determining the task schedule frequency. These options represent minute, hour, day of the month, month and day of the week in the given order. Asterisk symbol means all possible values. So, the above command will run every minute.

The Cron job below will be executed at 6:20 on 10th of every month.

```php
20 6 10 * * command/to/run
```

You can learn more about Cron job on <a href="https://en.wikipedia.org/wiki/Cron" target="_blank">Wikipedia</a>. However, Laravel Cron Job Scheduling makes the whole process very easy.

##### Creating New Laravel Project

Create a fresh laravel by running the following command on the terminal

```php
composer create-project --prefer-dist laravel/laravel cron-job
```

##### Create New Artisan Command

Go to your laravel projects root directory and run the folowing command to create an artisan command.

```php
php artisan make:command SendEmailToUser
```

It will creates a class named SendEmailToUser in app\Console\Commands directory. Now edit the SendEmailToUser to create an artisan command.

```php
/**
* The name and signature of the console command.
*
* @var string
*/
protected $signature = 'send:email';

/**
* The console command description.
*
* @var string
*/
protected $description = 'Sending a notification email about task to all users';
```

Now you have to register the command in app\Console\Kernel.php file.

```php
/**
* The Artisan commands provided by your application.
*
* @var array
*/
protected $commands = [
   ...
   'App\Console\Commands\SendEmailToUser',
];
```

After that define the command schedule in schedule function of app\Console\Kernel.php file.

```php
protected function schedule(Schedule $schedule)
{
    $schedule->command('send:email')
        ->everyMinute();    /** Run the task every minute */
}
```

Now, if you run the php artisan list command in the terminal, you will see your command has been registered. You will be able to see the command name with the signature and description.

![cron_1_1](https://raw.githubusercontent.com/monirahmedtanveen/monir.log/master/_posts/cron_1_1.png)

As the command is registered, I have wrriten my functionality in handle function of app\Console\Commands\SendEmailToUser class. If I run the send:email command, it will execute all the staments written in handle function of SendEmailToUser class. This is how the SendEmailToUser.php file looks with the handle method and all other changes in place:

```php
<?php

namespace App\Console\Commands;

use App\Mail\SendUserNotification;
use Illuminate\Console\Command;
use Illuminate\Support\Facades\Mail;
use App\User;

class SendEmailToUser extends Command
{
    /**
     * The name and signature of the console command.
     *
     * @var string
     */
    protected $signature = 'send:email';

    /**
     * The console command description.
     *
     * @var string
     */
    protected $description = 'Sending a notification email about task to all users';

    /**
     * Create a new command instance.
     *
     * @return void
     */
    public function __construct()
    {
        parent::__construct();
    }

    /**
     * Execute the console command.
     *
     * @return mixed
     */
    public function handle()
    {
        /** Sending Email */
        $users = User::limit(5)->get();

        foreach ($users as $user) {
            retry(5, function () use ($user) {
                Mail::to($user)->send(new SendUserNotification($user));
            }, 100);
        }

        $this->info('Notification Email Sent to All Users');
    }
}
```

If you run the following command

```php
php artisan send:email
```

You will see the email has been sent to all user and the output shows in the terminal.

