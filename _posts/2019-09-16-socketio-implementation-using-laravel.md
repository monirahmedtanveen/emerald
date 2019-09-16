---
published: true
---
### Prequisite

For successfull implementation of socketio some backend application need to be run. For this some dependencies need to be installed in your machine. Here are the list of dependencies and their installation process given.

- **Redis Server**
- **NodeJs & npm**

##### Redis Server Installation

**For Windows Environment,** go to this link :- [redis server](https://github.com/microsoftarchive/redis/releases) and download Redis-x64-3.0.504.zip file. After download unzip it and run the redis-server.exe.

**For Linux Environment,** command to install redis server :-

Update the apt-get packages :-
```php
"sudo apt-get update"
```

Next run below command from the terminal to install Redis on your machine :-
```php
"sudo apt-get install redis-server"
```

Next is to enable Redis to start on system boot. Also restart Redis service once.
```php
"sudo systemctl enable redis-server.service"
```

**Install Redis PHP Extension :**  if you need to use Redis from PHP application, you also need to install Redis PHP extension on your Ubuntu system. In our case it is mandatory. Run below command to install:
```php
"sudo apt-get install php-redis"
```

**Test Connection to Redis Server :-**

type below command and see the output,
```php
command :- "redis-cli"
output :- 127.0.0.1:6379>
command :- "ping"
output :- PONG (It output PONG if connection successful)
```