## Fancy directory listing with [flask](http://flask.pocoo.org/) for web servers

web servers allow to serve the content of folders, but generally in a pretty poor way :

![ngnix default directory listing](https://github.com/patatetom/fancypy/blob/master/default.png "ngnix default directory listing")

with fancypy, you can simply and easily redefine the layout :

![fancypy directory listing](https://github.com/patatetom/fancypy/blob/master/fancypy.png "fancypy directory listing")

---


## Installation

*(in this example of installation, fancypy is behind the nginx web server : adjust the procedure to your case)*

* install [flask](http://flask.pocoo.org/docs/0.11/installation/)
* create a new user named «fancypy» :
```
# useradd -m -c "Fancy directory listing with Flask" -g nginx -N fancypy
```
* copy `fancypy` and `fancypy.html` in the fancypy home directory :
```
# wget -P /home/fancypy/ https://raw.githubusercontent.com/patatetom/fancypy/master/fancypy
# wget -P /home/fancypy/ https://raw.githubusercontent.com/patatetom/fancypy/master/fancypy.html
```
* change/check the code of the fancypy python script ;-)
* make the fancypy python script executable :
```
# chmod +x /home/fancypy/fancypy
```
* install fancypy service with systemd :
```
# echo "[Unit]
Description=Fancy directory listing with flask
After=network.target nginx.service
[Service]
ExecStart=/home/fancypy/fancypy &
User=fancypy
[Install]
WantedBy=multi-user.target
" > /etc/systemd/system/fancypy.service
# systemctl enable fancypy
...
# systemctl start fancypy
```
* check fancypy service :
```
# systemctl status fancypy
...
# wget -q -O /dev/stdout http://localhost:5000
:-(
```

---


## Configuration

*(in this example of configuration, fancypy is behind the nginx web server : adjust the procedure to your case)*

the `/clam/` directory was served by ngnix with this simple configuration :

```
# cat /etc/nginx/nginx.conf
...
location /clam/ {
 autoindex on;
 autoindex_localtime on;
 autoindex_exact_size off;
}
...
```

once fancypy installed, the directory can be served with this simple configuration :

```
# cat /etc/nginx/nginx.conf
...
location /clam/ {
 proxy_pass http://127.0.0.1:5000/?root=$document_root&uri=$request_uri;
}
 ...
```

if directory contains subdirectories, the configuration may be :

```
# cat /etc/nginx/nginx.conf
...
location ~ ^/(clam|clam/.*)/$ {
 proxy_pass http://127.0.0.1:5000/?root=$document_root&uri=$request_uri;
}
...
```

---


## Template

fancypy will search for the template `fancypy.html` first in the «root+uri» directory and finally fallback in the fancypy home directory. the template will be automaticly removed from listing.

*(here «root» is `/var/www/` and «uri» is `/clam/`)*

```html
# cat /var/www/clam/fancypy.html 
<!DOCTYPE html>
<head>
<meta charset="utf-8"/>
<meta http-equiv="X-UA-Compatible" content="IE=edge"/>
<meta name="viewport" content="width=device-width, initial-scale=1"/>
<link rel="icon" href="/img/favicon.ico"/>
<title>ClamAV</title>
<link href="/css/bootstrap.min.css" rel="stylesheet"/>
<link href="/css/bootstrap-theme.min.css" rel="stylesheet"/>
<link href="/css/custom.css" rel="stylesheet"/>
</head>
<body role="document">
<div class="container theme-showcase" role="main">
<h1>ClamAV Virus Databases</h1>
<p>These files are needed to run the antivirus ClamAV</p>
<div class="alert alert-warning" role="alert">
<strong>Thank you to download large resources </strong>(+100Mo)<strong> outside working hours.</strong>
</div>
{{listing|safe}}
</div>
<script src="/js/jquery.min.js"></script>
<script src="/js/bootstrap.min.js"></script>
<script src="/js/docs.min.js"></script>
</body>
</html>
```
the most important part of the template is `{{listing|safe}}` : this is where fancypy will place directory listing.
