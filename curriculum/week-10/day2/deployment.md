# Deployment

## Topics Covered / Goals
- Deployment
  - run your app from a real server on the actual internet (AWS)
  - learn differences between localhost and a live site (gunicorn+nginx vs `python manage.py runserver`)
- DNS
  - understand that DNS is a network of databases, used to translate IP addresses into domain names
  - understand basic record types (A, CNAME)
- HTTPS
  - get a green lock in the url bar so your website looks trustworthy
  - understand what SSL/TLS and HTTPS are, and the types of attacks it prevents against
  - access 'powerful features' for the front-end that are only available in secure contexts, such as Geolocation, DeviceOrientation/DeviceMotion, getUserMedia, and WebMIDI

## Lesson

> We'll need to make a few changes to our django app to prepare it for deployment. 
- Set debug=False in settings.py.
- Set ALLOWED_HOSTS = ['*'] in settings.py
- Set CSRF_TRUSTED_ORIGINS = ['https://regularsizewebsite.com']

- install gunicorn: `pip install gunicorn; pip freeze > requirements.txt`


### AWS EC2
> Amazon Web Services (AWS) has many services that can be used to build a web application, or other forms of digital infrastructure. We'll be deploying our applications using AWS EC2 (elastic compute cloud), which is the most basic AWS service, upon which many others are based. It's just a basic linux server that we can connect to with SSH and install our application.
> When you sign up initially, you get free access to many services for your first year. After that, you pay normal AWS rates, which are very competitive compared to other cloud computing providers. 
> After logging in, click on the search bar at the top and type in "ec2". Click through to the EC2 dashboard and click "Launch Instance". Next we'll choose an operating system. There are many options, but we'll be using Ubuntu because it's a very popular choice, and it's probably more similar to our dev environment than the other options. 
> After choosing the operating system, we have to choose the "Instance Type", which refers to the number of CPUs, amount of memory, and network speed for this instance. If we're sticking to the free tier, `t2.micro` is the only option, so this is an easy choice. 
> When we review our instance launch, we must choose an SSH key pair to use for logging in to this instance. We can also create them right here, since we haven't done this before. Download the private key to your local machine, and MAKE SURE YOU DON'T LOSE IT.
> We need to create a security group for our instance, which is a set of firewall rules. By default, no web traffic is allowed in or out of our instance! Check the boxes to enable traffic on port 22 (SSH), 80 (HTTP), and 443 (HTTPS).
> The last thing we need to do before we log into our instance is find its IP address. You can find this from the list of instances in your EC2 dashboard. 

> Now, we can log in to our server with SSH. 
```bash
ssh -i ~/.ssh/my_private_key.pem ubuntu@<ip_address>
```
> You might have gotten an error about the permissions on your private SSH key. If so, we might need to change the permissions on our private key to make it more secure.

```bash
sudo chmod 600 ~/.ssh/my_private_key.pem
```

> After we successfully log into the server, we'll need to clone our project repository. Conveniently, git is preinstalled on the server.
```bash
git clone <repo_url>
```

Unfortunately, many other tools that we need are not installed yet. 

```bash
sudo apt update
sudo apt install python3-pip
sudo apt install postgresql
sudo -u postgres createuser --superuser $USER
sudo apt install libpq-dev
sudo apt install nginx-core
pip install -r requirements.txt
createdb <database_name>
python3 manage.py migrate

sudo apt install npm
sudo npm install -g n
sudo n stable

npm install
npm run build
```


> Django's built-in webserver is not fit to be a production web server on the public internet. We'll put another web server in front of it, called `nginx` (pronounced engine ex). After installing nginx, we'll need to modify a couple of config files.

> The main, global config file for nginx is located at `/etc/nginx/nginx.conf`. The first line of this file specifies the user that nginx runs as. By default, it's `www-data`, but we need to change it to the owner of the application files, `ubuntu`. You could instead change the owner of the application files to `www-data` using the command `chown`, if you prefer that. What matters is that they match. 

> There is a second config file, located in `/etc/nginx/sites-enabled/default`, which contains information specific to the application we want to run. In the config below, nginx will listen on port 80, and then pass those requests to port 9000. Also, any requests that start with `/static/` will be given files from `/build/static` in response. 

```
server {
  listen 80 default_server;
  listen [::]:80 default_server;
  # listen 443 ssl default_server;
  # listen [::]:443 ssl default_server;
  index index.html index.htm index.nginx-debian.html;
  server_name _;

  location / {
    proxy_pass http://0.0.0.0:9000;
  }
  location /static/ {
    alias /home/ubuntu/deployment-demo-app/news_project/static/;
  }
}
```
> nginx can listen for requests from the actual public internet, but it doesn't know specifically how to talk with a python application. We're going to use another tool called gunicorn that can receive requests from nginx at port 9000, and then pass them off to django.  We installed it earlier, so now let's try running the application with gunicorn: `gunicorn news_project.wsgi --bind 0.0.0.0:9000 --daemon`

### AWS Route53
> To get a domain name for our website, we'll need to use another AWS service, Route53, which is used for DNS and routing settings. After registering a domain name, click 'Manage DNS settings' for this domain. DNS is the Domain Name System, a network of servers that are used to convert domain names into IP addresses, so that clients can find the location of servers that they're looking for. We need to create an A Record, which simply maps a domain name to an IP address. The value for the record should be the IP address of our EC2 instance. All other fields can be left at their default settings. 

### SSL/TLS
> The last thing we need when we deploy our website is an SSL certificate, so we can serve traffic over HTTPS. Using HTTPS encrypts your HTTP traffic, which prevents hackers from viewing or modifying the pages your users visit or the forms they submit using a Man-in-the-middle attack (MitM). Using HTTPS also enables certain "powerful features" in the browser, such as hardware access (microphone, camera, accelerometer, GPS, MIDI devices) or notifications. 
> Thankfully, we can get these for free using [certbot](https://certbot.eff.org/instructions). Just select your server and OS (nginx on Ubuntu) and follow the instructions. Certbot will generate an SSL certificate for you, and then automatically change your nginx config file so that your app serves HTTPS traffic on port 443 using that certificate.

```bash
sudo snap install --classic certbot
sudo ln -s /snap/bin/certbot /usr/bin/certbot
sudo certbot --nginx
```

> After you restart the nginx server with `sudo service nginx restart`, your full stack news site should be publicly accessible over HTTPS.

## External Resources
- [certbot](https://certbot.eff.org/instructions)

## Assignments
- [Rock, Paper, Scissors](https://github.com/sierraplatoon/app-rock-paper-scissors)




