# change hostname

 hostnamectl set-hostname disruptive.dev

# fix address
sudo nano /etc/hotsts


# fix hestia ssl cert
hostname
    disruptive.dev
v-change-sys-hostname disruptive.dev
v-add-letsencrypt-host
sudo certbot --nginx


# install hestia

wget https://raw.githubusercontent.com/hestiacp/hestiacp/release/install/hst-install.sh
bash hst-install.sh --force
bash hst-install.sh --interactive no --email sodawave@gmail.com --password lecfX9GUE7442ssf --hostname www.disruptive.dev -f 


sudo snap install core; sudo snap refresh core
sudo snap install --classic certbot
sudo certbot --nginx

cron cerbot


# upload ac7
scp ac7.local.zip root@178.62.70.68:~/ac7.zip


# mysql
admin_activecollab
admin_root
tQYzMCUgktcV1ofc

# https://disruptive.dev/phpmyadmin/


# fix ac thread stack
sudo nano /etc/mysql/my.cnf
https://activecollab.com/help/books/self-hosted-activecollab/upgrade-from-activecollab-4



# install node 19.2 & npm
cd ~
curl -sL https://deb.nodesource.com/setup_19.xsu -o /tmp/nodesource_setup.sh
sudo bash /tmp/nodesource_setup.sh
sudo apt-get install -y nodejs


# instal aptitude
sudo apt install aptitude


(crontab -l ; echo "* * * * * php '/home/admin/web/ac.disruptive.dev/public_html/tasks/cron_jobs/run_every_minute.php'") | sort - | uniq - | crontab -
(crontab -l ; echo "*/3 * * * * php '/home/admin/web/ac.disruptive.dev/public_html/tasks/cron_jobs/check_imap_every_3_minutes.php'") | sort - | uniq - | crontab -
(crontab -l ; echo "0 * * * * php '/home/admin/web/ac.disruptive.dev/public_html/tasks/cron_jobs/run_every_hour.php'") | sort - | uniq - | crontab - 

crontab -l


COMMON ACCOUNT SETTINGS
    Username: 	        ac@disruptive.dev
    Password: 	        Uno2tres
    Webmail: 	        http://webmail.disruptive.dev
    Hostname: 	        mail.disruptive.dev
IMAP SETTINGS
    Authentication: 	Normal Password
    SSL/TLS: 	        Port 993
    STARTTLS: 	        Port 143
    No encryption: 	    Port 143
POP3 SETTINGS
    Authentication: 	Normal Password
    SSL/TLS: 	        Port 995
    STARTTLS: 	        Port 110
    No encryption:     	Port 110
SMTP SETTINGS
    Authentication: 	Normal Password
    SSL/TLS: 	        Port 465
    STARTTLS: 	        Port 587
    No encryption: 	    Port 25 


# setup swap

sudo fallocate -l 1G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo cp /etc/fstab /etc/fstab.bak
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
sudo sysctl vm.swappiness=10
sudo nano /etc/sysctl.conf
    vm.swappiness=10

# downgrade nodejs to v18
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
aptitude purge nodejs
aptitude update
aptitude install nodejs
npm cache clean --force

# install pm2 
npm install pm2 -g

# install n8n 
npm install sqlite3 --save
npm install n8n -g

# config pm2 ecosystem
pm2 init 
nano /root/ecosystem.config.js

    https://docs.n8n.io/hosting/environment-variables/environment-variables/#deployment


```                                                            
module.exports = {
    apps : [
        {
            name   : "n8n",
            env: {
                N8N_BASIC_AUTH_ACTIVE:true,
                N8N_BASIC_AUTH_USER:"admin",
                N8N_BASIC_AUTH_PASSWORD:"uno2tres",
                VUE_APP_URL_BASE_API:"https://n8n.disruptive.dev",
                EXECUTIONS_TIMEOUT:3600,
                EXECUTIONS_TIMEOUT_MAX:7200,
                NODE_FUNCTION_ALLOW_BUILTIN:"*",
                NODE_FUNCTION_ALLOW_BUILTIN:"crypto",
                NODE_FUNCTION_ALLOW_BUILTIN:"crypto,fs",
                NODE_FUNCTION_ALLOW_EXTERNAL:"moment,lodash",
                WEBHOOK_URL:"https://n8n.disruptive.dev",
                N8N_METRICS:false, 
                N8N_METRICS_PREFIX:"n8n_",
                N8N_PROTOCOL: "https",
                WEBHOOK_TUNNEL_URL: "https://n8n.disruptive.dev/",
                N8N_HOST: "n8n.disruptive.dev"
            },
            error_file: "/dev/null"
        }
    ]
}

```


pm2 start n8n
pm2 start ecosystem.config.js --update-env
pm2 startup 
(pm2 startup upstart)

NODE_ENV=development pm2 start ecosystem.config.js --update-env



# create subdomain n8n 


# setup nginx
- create subdomain
nano /home/admin/conf/web/n8n.disruptive.dev/nginx.ssl.conf

``` 
server {
    listen      178.62.70.68:443 ssl http2;
    server_name n8n.disruptive.dev ;
    ssl_certificate      /home/admin/conf/web/n8n.disruptive.dev/ssl/n8n.disruptive.dev.pem;
    ssl_certificate_key  /home/admin/conf/web/n8n.disruptive.dev/ssl/n8n.disruptive.dev.key;
    ssl_stapling on;
    ssl_stapling_verify on;
    error_log  /var/log/apache2/domains/n8n.disruptive.dev.error.log error;

    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    #proxy_set_header Host $http_host;
    proxy_set_header X-NginX-Proxy true;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
    proxy_max_temp_file_size 0;
    #proxy_pass http://my_nodejs_upstream/;
    proxy_redirect off;
    proxy_read_timeout 240s;

    include /home/admin/conf/web/n8n.disruptive.dev/nginx.hsts.conf*;

    location / {
        proxy_pass      http://localhost:5678;
        location ~* ^.+\.(css|htm|html|js|json|xml|apng|avif|bmp|cur|gif|ico|jfif|jpg|jpeg|pjp|pjpeg|png|svg|tif|tiff|webp|aac|caf|flac|m4a|midi|mp3|ogg|opus|wav|3gp|av1|avi|m4v|mkv|mov|mpg|mpeg|mp4|mp4v|webm|otf|ttf|woff|woff2|doc|docx|odf|odp|ods|odt|pdf|ppt|pptx|rtf|txt|xls|xlsx|7z|bz2|gz|rar|tar|tgz|zip|apk|appx|bin|dmg|exe|img|iso|jar|msi|webmanifest)$ {
            root           /home/admin/web/n8n.disruptive.dev/public_html;
            access_log     /var/log/apache2/domains/n8n.disruptive.dev.log combined;
            access_log     /var/log/apache2/domains/n8n.disruptive.dev.bytes bytes;
            expires        max;
            try_files      $uri @fallback;
        }
    }

    location /error/ {
        alias   /home/admin/web/n8n.disruptive.dev/document_errors/;
    }
    
    location @fallback {
        proxy_pass      http://localhost:5678;
    }

    location ~ /\.(?!well-known\/|file) {
       deny all;
       return 404;
    }

    proxy_hide_header Upgrade;

    include /home/admin/conf/web/n8n.disruptive.dev/nginx.ssl.conf_*;
}

``` 
sudo systemctl restart nginx





<----------------------- v1 snapshot


# create subdomain red on Hestia


# setup nginx
- create subdomain
nano /home/admin/conf/web/red.disruptive.dev/nginx.ssl.conf

``` 
server {
    listen      178.62.70.68:443 ssl http2;
    server_name red.disruptive.dev ;
    ssl_certificate      /home/admin/conf/web/red.disruptive.dev/ssl/red.disruptive.dev.pem;
    ssl_certificate_key  /home/admin/conf/web/red.disruptive.dev/ssl/red.disruptive.dev.key;
    ssl_stapling on;
    ssl_stapling_verify on;
    error_log  /var/log/apache2/domains/red.disruptive.dev.error.log error;

    include /home/admin/conf/web/red.disruptive.dev/nginx.hsts.conf*;

    location / {
        proxy_pass      http://127.0.0.1:1880;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }

    location /error/ {
        alias   /home/admin/web/red.disruptive.dev/document_errors/;
    }

    location ~ /\.(?!well-known\/|file) {
       deny all;
       return 404;
    }

    include /home/admin/conf/web/red.disruptive.dev/nginx.ssl.conf_*;
}

``` 
sudo systemctl restart nginx


npm install node-red -g

# generate pwd
mkdir pass
cd pass
npm init
npm install bcryptjs
node -e "console.log(require('bcryptjs').hashSync(process.argv[1], 8));" Uno2tres


# change node-red settings
nano .node-red/settings.js

```
    adminAuth: {
        type: "credentials",
        users: [{
            username: "sodawave@gmail.com",
            password: "$2a$08$UL.neMsSzEIWZK1R7F8uJ.3/XGi1Uf0getWqzUTIGX2ORCLSL63ve",
            permissions: "*"
        }]
    },
    functionGlobalContext: {
        // os:require('os'),
        zlib: require('zlib')
    },
```

# edit proxy settings

nano /home/admin/conf/web/red.disruptive.dev/nginx.ssl.conf 

```
server {
    listen      178.62.70.68:443 ssl http2;
    server_name red.disruptive.dev ;
    ssl_certificate      /home/admin/conf/web/red.disruptive.dev/ssl/red.disruptive.dev.pem;
    ssl_certificate_key  /home/admin/conf/web/red.disruptive.dev/ssl/red.disruptive.dev.key;
    ssl_stapling on;
    ssl_stapling_verify on;
    error_log  /var/log/apache2/domains/red.disruptive.dev.error.log error;

    include /home/admin/conf/web/red.disruptive.dev/nginx.hsts.conf*;

    location / {
        proxy_pass      http://127.0.0.1:1880;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }

    location /error/ {
        alias   /home/admin/web/red.disruptive.dev/document_errors/;
    }

    location ~ /\.(?!well-known\/|file) {
       deny all;
       return 404;
    }

    include /home/admin/conf/web/red.disruptive.dev/nginx.ssl.conf_*;
}

```

pm2 start node-red
pm2 save



# reset n8n user
n8n user-management:reset 
