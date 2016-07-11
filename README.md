# FancyPy

## Fancy directory listing with Flask for web servers

web servers allow to serve the content of folders, but generally in a pretty poor way :

![Ngnix default directory listing](https://github.com/patatetom/fancypy/blob/master/default.png "Ngnix default directory listing")

with FancyPy, you can simply and easily redefine the layout :

![FancyPy directory listing](https://github.com/patatetom/fancypy/blob/master/fancypy.png "FancyPy directory listing")

---


## Installation

*(in this example of installation, FancyPy is behind the Nginx web server : adjust the procedure to your case)*

* install [Flask](http://flask.pocoo.org/docs/0.11/installation/)
* create a new user named «fancypy» :
```
# useradd -m -c "Fancy directory listing with Flask" -g nginx -N fancypy
```
* copy «fancypy» and «fancypy.html» to fancypy home directory :
```
# wget -P /home/fancypy/ https://raw.githubusercontent.com/patatetom/fancypy/master/fancypy
# wget -P /home/fancypy/ https://raw.githubusercontent.com/patatetom/fancypy/master/fancypy.html
```
* check the code of fancypy python script ;-)
* make fancypy executable :
```
# chmod +x /home/fancypy/fancypy
```
* install fancypy service with systemd :
```
# echo "[Unit]
Description=Fancy directory listing with Flask
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
# wget http://localhost:5000 -O-
:-(
```

---


## Configuration

*(in this example of configuration, FancyPy is behind the Nginx web server : adjust the procedure to your case)*

---


## Template

FancyPy will search for the template «fancypy.html» first in the «root+uri» directory and finally in fancypy home directory.
it will be automaticly removed from listing.
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
