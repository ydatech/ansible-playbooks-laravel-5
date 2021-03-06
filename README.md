###Ansible Playbooks for Laravel 5
#####Minimalistic and performant setup for your apps
---

##### Note

This does not include mysql-server as I use RDS but you can easily update `provision.yml` to include a mysql installation using `ansible-galaxy`. Also, SSL is enabled, but you can disable it by commenting out a few lines. Also, in theory, this setup should work just fine for **Symfony, Yii, CodeIgniter or any PHP framework**.

##### Motive

Taylor Otwell has done a great job with Laravel Forge, but provisioning boxes yourself is my personal preference. As I have embarked upon this journey of learning DevOps, I find that Ansible gives you a very clean API to control how to build a production level box. 

The primary advantage of building your box is this: you learn how things work and you get to ensure only the necessary components for your application are provisioned with idempotence. You can add complexity as you grow, which is better than starting with a complex setup.

_I hope you will find this repository useful and I recommend you star it as I will update it with more improvements._

##### Requirements 

* python 2.7 must be installed on your server [A]
* ansible must be installed from where you are executing playbooks (watch video enclosed below)
* linux instance (e.g. _ubuntu/images/hvm-ssd/ubuntu-xenial-16.04-amd64-server-20160610 (ami-0ae77879)_)

##### Ansible

Ansible is a great tool for provisioning servers using an agentless form. So you can do the following:

1. Use a vagrant box on your local machine and control your fleet of servers.
2. Use a t2.micro instance on Amazon and use it to control your fleet of servers.

I would recommend [this](https://serversforhackers.com/video/ansible-installation-and-basics) guide to install Ansible.

##### Running

The first thing you should do is update your domain name in `/nginx/vars/main.yml`.

Then run the provisioner using the following command from the `ansible-playbooks-laravel-5/` directory:

`ansible-playbook --private-key=~/.ssh/your-web-server.pem provision.yml`

This installs the following software on your standard Linux box.

* php7 with batteries
* nginx
* git
* composer
* memcached
* htop
* unzip

Well done, you've just done a hell lot of work in 10 seconds. Now, download your app using:

`sudo git clone your-github-repository-url.git .` The `.` in the end is important.

Then, do `sudo touch .env` and add in your production environment variables.

Next, run `sudo composer install` from the inside of webroot which would be at `/var/www/domain.com/`. 

Finally, enter these two commands to establish the correct permissions on your `cache` and `storage` folders. 

```
sudo chgrp -R www-data storage bootstrap/cache
sudo chmod -R ug+rwx storage bootstrap/cache
```

You should now be able to access your app at `domain.com`.


##### Brief

Ideally suitable for Laravel setups, this Ansible provision recipe does a lot more than you might think. The configurations includes the industry standard optimizations so you can run a high traffic site out of the box. 

The php7.0 'batteries included' build by Ondrej Sury comes with all the necessary extensions to satisfy major framework requirements. See the list below to marvel at the range of goodies.


* php7.0-common
* php7.0-cli
* php7.0-intl
* php7.0-curl
* php7.0-cgi
* php7.0-fpm
* php7.0-mysql
* php7.0-gd
* php7.0-mbstring
* php7.0-mcrypt
* php7.0-memcached
* php7.0-apcu [B]
* php7.0-xml

In addition to the facilities for php above, there's nginx, with which, I am supplying optimizations such as ready-to-go gzip compression, advanced nginx setup and more.

You can peek into `/roles/nginx/files/h5bp/directive-only` and observe the 3 custom `.conf` files I have added: `gzip-compression.conf`, `nginx-optimization.conf` and `http-optimization.conf`. You can read the comments on top of each configuration line to learn.

Furthermore, once you provision, you will have `htop` support on your server. Invoking `htop` on the command line lets you monitor system usage. A nice to have tool for DevOps. 

##### Apendices

###### A | _You can do this easily by running `sudo apt-get update` and then `sudo apt-get install python`_.

###### B | _APCU is included to make Opcode caching even better. Opcode comes with PHP 7 built-in by default_.
