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

You can learn more about Cron job on <a href="https://en.wikipedia.org/wiki/Cron" target="_blank"></a>Wikipedia. However, Laravel Cron Job Scheduling makes the whole process very easy.

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

        /** Diffrent Scheduling Options */
        // ->cron('* * * * * *'); 	                /** Run the task on a custom Cron schedule */
        // ->everyFiveMinutes();                    /** Run the task every five minutes */
        // ->everyTenMinutes();                     /** Run the task every ten minutes */
        // ->everyFifteenMinutes();                 /** Run the task every fifteen minutes */
        // ->everyThirtyMinutes();                  /** Run the task every thirty minutes */
        // ->hourly();                              /** Run the task every hour */
        // ->hourlyAt(17);                          /** Run the task every hour at 17 mins past the hour */
        // ->daily();                               /** Run the task every day at midnight */
        // ->dailyAt('13:00');                      /** Run the task every day at 13:00 */
        // ->twiceDaily(1, 13);                     /** Run the task daily at 1:00 & 13:00 */
        // ->weekly();                              /** Run the task every week */
        // ->weeklyOn(1, '8:00');                   /** Run the task every week on Tuesday at 8:00 */
        // ->monthly();                             /** Run the task every month */
        // ->monthlyOn(4, '15:00');                 /** Run the task every month on the 4th at 15:00 */
        // ->quarterly();                           /** Run the task every quarter */
        // ->yearly();                              /** Run the task every year */
        // ->timezone("America/New_York");          /** Set the timezone */

}
```