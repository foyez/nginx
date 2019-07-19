# NGINX

## Build nginx from source & add modules
```bash
$ wget http://nginx.org/download/nginx-1.17.1.tar.gz
$ tar -zxvf nginx-1.17.1.tar.gx
$ cd nginx-1.17.1
$ ./configure
$ apt-get install libpcre3 libpcre3-dev zlib1g zlib1g-dev libssl-dev
$ ./configure
$ ./configure --help
$ ./configure --sbin-path=/usr/bin/nginx --conf-path=/etc/nginx/nginx.conf --error-log-path=/var/log/nginx/error.log --http-log-path=/var/log/nginx/access.log --with-pcre --pid-path=/var/run/nginx.pid --with-http_ssl_module
$ make # compile
$ make install # install compile source
$ ls -l /etc/nginx/ # checking configuration files
$ nginx -v # check version
$ nginx # run nginx
$ nginx -h # showing nginx commands
$ ps aux | grep nginx # check the running process
$ ip a # check for ip address in inet
```

## Adding an NGINX service
```bash
$ touch /lib/systemd/system/nginx.service #create systemd path
$ nano /lib/systemd/system/nginx.service
# write out these lines
[Unit]
Description=The NGINX HTTP and reverse proxy server
After=syslog.target network.target remote-fs.target nss-lookup.target

[Service]
Type=forking
PIDFile=/var/run/nginx.pid
ExecStartPre=/usr/bin/nginx -t
ExecStart=/usr/bin/nginx
ExecReload=/usr/bin/nginx -s reload
ExecStop=/bin/kill -s QUIT $MAINPID
PrivateTmp=true

[Install]
WantedBy=multi-user.target

$ systemctl start nginx # start systemd
$ systemctl status nginx # sytemd status
$ systemctl stop nginx # stop systemd
$ systemctl enable nginx # startup on reboot
$ reboot # rebooting server
```

## NGINX Configuration (nginx.conf)
2 main configuration terms
1. Context
2. Directive

* Example:
```nginx
server { # context
  listen 80; # directive - server_name mydomain.com;
}
```

## Creating a Virtual Host

```bash
$ ls -l /sites/demo # keeping files in this directory
$ ls -l /etc/nginx/ # located configuration files
$ systemctl reload nginx # reload command over the restart command to prevent any downtime; if error occurs reload will fail but not stop nginx with the current configuration
$ systemctl restart nginx # first stop nginx and then if new configuration contain any errors refuse to start back
$ nginx -t # verify nginx
$ curl -I http://127.0.0.1/style.css # check mime type
```

## Location Blocks

Priority
1. Exact Match = URI
2. Preferential Prefix Match ^~ URI
3. REGEX Match ~* URI
4. Prefix Match URI

```nginx
server {
  location URI {
    ...handle response
  }
}

# Prefix match
location /greet {
  return 200 'Hello from NGINX "/greet" location.';
}

# Exact match
location = /greet {
  return 200 'Hello from NGINX "/greet" location. - Exact Match';
}

# REGEX has higher priority than prefix match
# REGEX match - case sensitive
location ~ /greet[0-9] {
  return 200 'Hello from NGINX "/greet" location. - REGEX Match';
}

# REGEX match - case insensitive
location ~* /greet[0-9] {
  return 200 'Hello from NGINX "/greet" location. - REGEX Match';
}

# Preferential Prefix > REGEX
# Preferential Prefix match
location ^~ /greet {
  return 200 'Hello from NGINX "/greet" location.';
}
```