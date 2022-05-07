## Nginx

to install ningx    

```bash
$ sudo apt update
$ sudo apt install nginx
```

to check firewall

```bash
$ sudo ufw app list
```


## Ubuntu security

to check is security is enable or not

```bash
$ sudo ufw status
Status: inactive
```

to enable security.

```bash
$ sudo ufw enable
Firewall is active and enabled on system startup
```

to allow the ssh(port 22) by service name.

```bash
$ sudo ufw allow ssh
Rule added
Rule added (v6)
```

to allow the ssh port number(Alternatively for ssh).

```bash
$ sudo ufw allow 22
Rule added
Rule added (v6)
```

to allow the specific port number.

```bash
$ sudo ufw allow 80
Rule added
Rule added (v6)
```

to allow Nginx and check

```bash
$ sudo ufw allow 'Nginx HTTP'
$ sudo ufw status

status result:
To                         Action      From
--                         ------      ----
OpenSSH                    ALLOW       Anywhere                  
Nginx HTTP                 ALLOW       Anywhere                  
OpenSSH (v6)               ALLOW       Anywhere (v6)             
Nginx HTTP (v6)            ALLOW       Anywhere (v6)
```

## Managing the Nginx Process

to checking your Web Server

```bash
$ systemctl status nginx

● nginx.service - A high performance web server and a reverse proxy server
   Loaded: loaded (/lib/systemd/system/nginx.service; enabled; vendor preset: enabled)
   Active: active (running) since Fri 2020-04-20 16:08:19 UTC; 3 days ago
     Docs: man:nginx(8)
 Main PID: 2369 (nginx)
    Tasks: 2 (limit: 1153)
   Memory: 3.5M
   CGroup: /system.slice/nginx.service
           ├─2369 nginx: master process /usr/sbin/nginx -g daemon on; master_process on;
           └─2380 nginx: worker process
```

to  start/disable automatically when the server boots.
```bash
$ sudo systemctl enable nginx
$ sudo systemctl disable nginx
```

to stop web server
```bash
$ sudo systemctl stop nginx
```

to start web server
```bash
$ sudo systemctl start nginx
```

to restart web server
```bash
$ sudo systemctl restart nginx
```

to reload web server
```bash
$ sudo systemctl reload nginx
```


## Setting Up Server Blocks

to change ownership        
this will change user the operations.     
```bash
$ ls -l
$ sudo chown <ownerAccountName> -vR  <directory>
```


to make permission.

r - read
w - write
x - executable

to see file details
```bash
$ ls -l
-rw-ew-r-- 1 programmingtute programmingtute 22 Jul 23 18:55 file
```
-               type of the file > - normal file, D directory, C character special file, B binary special file
rw-             owner of the file
rw-             group of the file
r--             permission of the file
1               symbolic links of the file
programmingtute name of owner of the file
programmingtute name of group of the file
22              size of the file
Jul 23 18:55    date of created
file            name of the file

to see directory details
```bash
$ ls -ld
-rw-ew-r-- 1 programmingtute programmingtute 22 Jul 23 18:55 file
```

to see directory details
```bash
$ ls -ld
-rw-ew-r-- 1 programmingtute programmingtute 22 Jul 23 18:55 file
```

to set permission for Nginx directory.   
for remove permission just set "-" instead of "+"
```bash
$ sudo chmod -R 755 /var/www/your_domain # -R recursive 7-user 5-group 5-other

# Alternatively
$ sudo chmod u+rwx /var/www/<your_domain>
$ sudo chmod g+rx /var/www/<your_domain>
$ sudo chmod o+rx /var/www/<your_domain>
```

to add default html file.
```bash
$ vim /var/www/<your_domain>/html/index.html
```
and add following html file.(you can add angular html file here)   
```html
<html>
    <head>
        <title>Welcome to your_domain!</title>
    </head>
    <body>
        <h1>Success!  The your_domain server block is working!</h1>
    </body>
</html>
```

to config nginx file at /etc/nginx/sites-available/<your_domain>
default: Will respond to any requests on port 80 that do not match the other two blocks.    

```shell
server {
        listen 80;
        listen [::]:80;

        root /var/www/your_domain/html;
        index index.html index.htm index.nginx-debian.html;

        server_name your_domain www.your_domain;

        location / {
                try_files $uri $uri/ =404;
        }
}
```

to copy site available to enable
```bash
$ sudo ln -s /etc/nginx/sites-available/your_domain /etc/nginx/sites-enabled/
```

to avoid "bucket memory problem" when adding additional server names.
```bash
$ sudo vim /etc/nginx/nginx.conf

# remove comment on following file.
...
http {
    ...
    server_names_hash_bucket_size 64;
    ...
}
...
```


when change some config in nginx
```bash
$ sudo nginx -t # to test nginx script
$ sudo systemctl restart nginx # restart/reload nginx
```




## Additional info

to block an IP address or Subnet
```bash
$ sudo ufw deny from 203.0.113.100
$ sudo ufw deny from 203.0.113.0/24
```

to allow an IP address(this will block other all IP addresses)
```bash
$ sudo ufw allow from 203.0.113.101
sudo ufw allow from 203.0.113.4 to any port 22
```

to disable security.

```bash
$ sudo ufw disable
```

to get current server IP.

```bash
$ curl -4 icanhazip.com
http://your_server_ip
```


### Ubuntu user management

to show current user
```bash
$  whoami
```

to change the user(only for ucrrent shell)
```bash
$  su <user_name>
```

to check all user
```bash
$  cut -d: -f1 /etc/passwd
```

to add new user
```bash
$  sudo adduser <new_username>
or
$  sudo useradd <new_username>
```

to remove/delete user
```bash
$  sudo userdel <username>  # remove
$  sudo rm -r /home/<username> # remove user account directory
```

to modify user
```bash
$  usermod -l new_username old_username  # update username
$  sudo passwd username # to change user password
```

to add user for sudo group  
```bash
$  adduser <username> sudo
```

to open new shell on ubuntu  
```bash
[ctrl] + [alt] + [t]
```
