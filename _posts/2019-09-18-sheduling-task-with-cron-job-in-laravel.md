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
