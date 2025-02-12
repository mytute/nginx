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

## setup with docker-compose file   

without docker :  
* Main configuration: /etc/nginx/nginx.conf  
* Site-specific configurations: /etc/nginx/sites-available/ and /etc/nginx/sites-enabled/  
with docker :   
* Configuration files (e.g., nginx.conf) are typically stored in a directory on the host machine and mounted into the container using Docker volumes.
```bash
volumes:
  - ./nginx.conf:/etc/nginx/nginx.conf
```
* write configurations on main config file in "http" curly braces.   

> docker-compose.yml  
```bash
version: '3.9'

services:
  nginx:
    image: nginx:alpine
    container_name: nginx_container
    ports:
      - "80:80"      # Maps port 80 on the host to port 80 on the container
      - "443:443"    # Maps port 443 on the host to port 443 on the container
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf:ro  # Custom Nginx configuration # status or dynamic 
      - ./html:/usr/share/nginx/html:ro        # Serve static HTML files
      - ./logs:/var/log/nginx                  # Store logs on the host
      # - ./default.conf : /etc/nginx/default.conf:ro # only for static html file  
    restart: unless-stopped
```

> nginx/html
```bash
<h1>Welcome to Nginx running in Docker!</h1>   
```

> nginx/conf/default.conf (for static file only )
```bash
server {
    listen 80;
    server_name localhost;

    root /usr/share/nginx/html;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```
> nginx/conf/nginx.conf
```bash
events {}

http {
  # Domain 1 - HTTP
  server {
    listen 80;
    server_name domain1.com;

    location / {
      proxy_pass http://service1:port; # Replace with your backend service
      proxy_set_header Host $host;
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header X-Forwarded-Proto $scheme;
    }
  }

  # Domain 1 - HTTPS
  server {
    listen 443 ssl;
    server_name domain1.com;

    ssl_certificate /etc/nginx/certs/domain1/fullchain.pem;
    ssl_certificate_key /etc/nginx/certs/domain1/privkey.pem;

    location / {
      proxy_pass http://service1:port; # Replace with your backend service
      proxy_set_header Host $host;
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header X-Forwarded-Proto $scheme;
    }
  }

  # Domain 2 - HTTP
  server {
    listen 80;
    server_name domain2.com;

    location / {
      proxy_pass http://service2:port; # Replace with your backend service
      proxy_set_header Host $host;
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header X-Forwarded-Proto $scheme;
    }
  }
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
ln - Create Link (used to create links between files)   
-s - Symbolic Link (If the original file is updated, the changes are reflected in the linked file), Flexibility(Easily Enable or Disable Websites Without Deleting Configuration),     
linking make Better Organization (Easier to Manage Multiple Websites), Flexibility()
```bash
$ sudo ln -s /etc/nginx/sites-available/your_domain /etc/nginx/sites-enabled/
```
| Feature            | Without Symbolic Links (Direct Edits) | With `sites-available/` + `sites-enabled/` |
|--------------------|--------------------------------------|------------------------------------------|
| **Organization**   | All configurations mixed in `nginx.conf` | Each site has a separate file |
| **Flexibility**    | Hard to disable sites | Can enable/disable without deleting |
| **Testing & Rollback** | Risk of breaking live site | Test changes before enabling |
| **Safety**        | Risk of accidental deletion | Configuration remains protected |


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

## Set Up SSL with Let's Encrypt   
```bash
# install Certbot
$ sudo apt install certbot python3-certbot-nginx -y

# Generate an SSL certificate:
$ sudo certbot --nginx -d yourdomain.com -d www.yourdomain.com

# Auto-renew certificates:
$ sudo systemctl enable certbot.timer

```

## Rate Limit Requests to Prevent DDoS  
This helps prevent abuse, DDoS attacks, and excessive API usage.  
```bash
http {
    # Define a shared memory zone named 'api_limit' with a size of 10MB.
    # Each unique key (e.g., client IP) will store request counters here.
    # 'api_limit' is a variable for name of zone

    limit_req_zone $binary_remote_addr zone=api_limit:10m rate=10r/s;
    limit_req_status 429; # response if rate limite exceeded send back to client  

    server {
        listen 80; # same port app run
        server_name example.com;

        location /api/ {  # '/' , '/api/' which zone need rate limit 
            # Apply the rate limit from the 'api_limit' zone.
            # The burst allows occasional spikes of 20 requests.
            limit_req zone=api_limit burst=20 nodelay;

            proxy_pass http://backend_api;
        }
    }
}
```
if you limit_req zone=ip burst=12 delay=8; and rate=5r/s then   
Rate = 5 r/s → Up to 5 requests per second are processed immediately.   
Burst = 12 → Up to 12 additional requests can be queued instead of being rejected.   
Delay = 8 → The first 8 requests from the burst queue are processed immediately.   
The remaining 4 requests in the burst (12 - 8) are delayed so that the request rate does not exceed 5r/s.   
Once the queue is full (12 requests), additional requests are rejected with a 503 error.  

Restrict by IP address 
```bash
location /admin {
    allow 192.168.1.100;
    deny all;
}
```

## Enable HTTP Security Headers   
```bash
server {
    listen 80;

    # 1️⃣ Prevent Clickjacking
    add_header X-Frame-Options DENY;

    # 2️⃣ Prevent MIME-type sniffing
    add_header X-Content-Type-Options nosniff;

    # 3️⃣ Enable basic XSS protection
    add_header X-XSS-Protection "1; mode=block";

    # 4️⃣ Enforce HTTPS (Only applies if HTTPS is enabled)
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains; preload";

    # 5️⃣ Define a Content Security Policy (CSP) to prevent malicious script execution
    add_header Content-Security-Policy "default-src 'self'; script-src 'self' https://trusted-scripts.com";

    location / {
        proxy_pass http://your_backend_server;
    }
}

```

## Limit Allowed Request Methods    
Why? Prevents unwanted HTTP methods like PUT, DELETE, and TRACE.   
```bash
# Add this inside your server block:
if ($request_method !~ ^(GET|POST|HEAD)$) {
    return 444;
}
```

## Disable Server Tokens (Hide Version Info)   
Why? Attackers use version info for targeted exploits.
```bash
$ sudo nano /etc/nginx/nginx.conf
$ server_tokens off;
$ sudo systemctl restart nginx
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
