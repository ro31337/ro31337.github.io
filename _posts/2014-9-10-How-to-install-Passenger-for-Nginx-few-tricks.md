---
layout: post
title: How to install Passenger for Nginx + few tricks
---

In this post I’ll cover how to install ‘passenger’ for nginx. What is passenger and why do you need it? Well, if you deploy your ruby applications straight to the cloud (heroku, aws, etc), you can skip this manual. But when it comes to running ruby apps on your own dedicated server (which I personally prefer) you need a bit of  magic. If you don’t have your own dedicated/VPS server, you’ll probably won’t find this article very useful.

Before following instructions in this manual, make sure you have RVM (Ruby Version Manager) installed (see my previous post). When RVM is installed, you’ll need some sort of server to run your apps on. You can always use `rails s -b 0.0.0.0` command, but default built-in rails server is not production-ready, very limited and works well for development purposes only. So here the passenger comes into play.

Passenger itself is little bit tricky. The trickiest part of using Passenger and Nginx is that you can’t update your nginx – you have to compile, build new version, install, and remove old one. New version should be installed into the new directory.

OK, now the question “how”. Start with installing passenger gem:

```
gem install passenger
```

And then

```
rvmsudo passenger-install-nginx-module
```

The command above will run interactive and friendly (greetings to Phusion team from Netherlands!) step-by-step guide where I just leave default values.

Now let’s check if everything’s installed:

```
cd /opt/nginx/
ls -l
```

Sample output:

```
drwxr-xr-x 2 root root 4096 May  4 23:33 conf
drwxr-xr-x 2 root root 4096 May  4 23:33 html
drwxr-xr-x 2 root root 4096 May  4 23:33 logs
drwxr-xr-x 2 root root 4096 May  4 23:33 sbin
```

One more thing I really recommend to do right now is to make a backup copy of config file:

```
cd /opt/nginx/conf/
cp nginx.conf nginx.conf.bak.1
```

If you have your old nginx installation running, make backups for your config files in /etc/nginx because now we’re going to uninstall previous version:

```
apt-get remove nginx nginx-full nginx-light nginx-naxsi nginx-common
```

Done? Great! Time to run nginx:

```
sudo /opt/nginx/sbin/nginx
```

Let’s try to get a page from localhost and show it on your screen to see if newly installed nginx is running:

```
wget -O - http://localhost
```

You should see something like “Welcome to nginx!” and some html code. Well, great! But how to stop it? Here is the trick:

```
sudo kill $(cat /opt/nginx/logs/nginx.pid)
```

Run wget command again and see that it has failed. Now we can run and stop nginx instances. What now? Everything works, but I’d love to get those `service nginx start`, `service nginx stop` commands back. Thanks to folks for posting [this github gist](https://gist.github.com/mwlang/3c7c3c832621d24051ae). Here are few commands you need to replace init.d script:

```
cd /tmp
wget https://gist.githubusercontent.com/mwlang/3c7c3c832621d24051ae/raw/c8825bf2e9c9243201e4e0e974626501592ce81e/nginx.sh
sudo cp nginx.sh /etc/init.d/nginx
sudo chmod +x /etc/init.d/nginx
sudo update-rc.d nginx defaults
```

Now we can use `service nginx start`, `service nginx stop`,  etc. commands. Check by yourself with wget-command above.

Now you can update `nginx.conf` with your previous configuration. When hosting ruby/rails apps, make sure you pointing them to public folder:

```
server {
   listen 80;
   server_name www.yourhost.com;
   root /somewhere/public;   # <--- be sure to point to 'public'!
   passenger_enabled on;
}
```

Don't get upset if it doesn't go very smooth. I wrote my first driver for Linux in 1998 and I don't remember days when such configurations were easy. There always will be outdated component that requires third-party library, but before you have to uninstall something and install something different. It is open source and sometimes it's kind of fun to do impossible.
