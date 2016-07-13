# Introduction

This is minimal PHP development environment with xdebug and phpmyadmin which are not present in Laradock at the moment of creating this.

At this time Laradock is the most complete Docker enviroment for Laravel. However as it has support for rarely used containers like Neo4j with intention of covering them all, it doesn't have some basic tools required for development installed and configured like debugger or database admin. So I felt free to add them.

Although there is not much code, this will possibly save you certain amount of time for troubleshooting as this can get tricky. 


# Xdebug

### Brief explanation for [xdebug.ini](php-fpm/xdebug.ini) settings:

* we have to enable remote debugging for containers since they are in separate virtual machine with `xdebug.remote_enable=1`
* default xdebug port is 9000 and it is used by php-fpm so we have to change it `xdebug.remote_port=9001`
* we use connect back, no need to specify IP address `xdebug.remote_connect_back=1`
    * if you want to specify IP set this to `0` and uncomment this with IP of your host seen from your container `;xdebug.remote_host=172.18.0.1`
* IDE key for Netbeans `xdebug.idekey="netbeans-xdebug"`
* enable logging for easier debugging of the debugger, you can comment it out later `xdebug.remote_log="/tmp/xdebug_log/xdebug.log"`


### In [docker-compose.yml](docker-compose.yml) we just have to:

* bind port 9001 to the host `- "9001:9001"`
* mount folder to access logs from host `- ./logs/php-fpm/:/tmp/xdebug_log`
 

### Setting paths mapping in you IDE

As this is remote debugging and IDE and debugger are on separate machines you'll have to supply IP, port and filesystem paths mappings to your IDE.

#### IP

* you can use loopback `127.0.0.1` or direct VM address `192.168.99.100`. Use `netstat` toot for debugging.

#### Port

Your IDE listens on 2 different ports for debugger connections and those have to be different. You have 2 options:

* specify `9001` as default port and leave out remote IP (it will use default 127.0.0.1)
* specify `9001` as remote port, and `192.168.99.100` as remote IP, then you'll have to change default port to something different like `9002`

(Don't forget to set back your default (local) port when you wish to debug your local projects again.)

#### Paths mappings

Brief explanation: The point is you map **_folders_** and you map **_absolute filesystem paths_** on _server_ and _local_. Browser url is **_irrelevant_**, it does not matter how you run your code. In IDE enable `stop debugger on first line` for easier troubleshouting.

For example to map Laravel entry point use:
```
 server path: /var/www/laravel_project/public - local path: C:\Users\username\Desktop\laravel_project\public
```
To debug routes use:
```
server path: /var/www/laravel_project/app/Http - local path: C:\Users\username\Desktop\laravel_project\app\Http
```

Example settings in Netbeans:

![Screenshot 1](http://image.prntscr.com/image/70425c4ffe494e5d8c34dd0129c6919e.png)

# PhpMyAmin

I added new container which uses [official phpmyadmin image](https://github.com/phpmyadmin/docker) which you can see in [docker-compose.yml](docker-compose.yml). We use `links: - mysql` to expose environment variables from mysql container to phpmyadmin container. Then we set 
```
environment:
   MYSQL_USER: root
   MYSQL_PASSWORD: root
   PMA_HOST: mysql
```
as those are defaults in used official mysql image. For other mysql containers you can see them with `docker inspect`.

Port is changed to be different from your local phpmyadmin `"8080:80"`. You can access admin panel from `http://192.168.99.100:8080`. You can replace IP with domain by setting `192.168.99.100 myappdomain.app` in hosts file.


# Contribute

I plan to add [pgAdmin](https://www.pgadmin.org/) since I started deploying to Heroku.

# Licence

Use this as you wish.




