# Domain SSL configure Steps

After hosting your app, follow instructions below
## 1. Install Nginx
```
sudo apt install nginx
```
Go to this location
```
sudo nano /etc/nginx/sites-available/default
```
Add the following to the location part of the server block
```
    location / {
        proxy_pass http://localhost:5000; #whatever port your app runs on
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
```
Check NGINX config error
```
sudo nginx -t
```
Restart NGINX
```
sudo service nginx restart
```
You should now be able to visit your IP with no port (port 80) and see your app. Now let's add a domain

## 2. Add domain
If you have domain, you can add A record to your "EC2 instance IP / Cloudflare" with a new subdomain as I'm going to show you next

### 2.1 Check that Port 80 redirect to App server

## 3. Install Free SSL
### 3.1 Installing Certbot
```
sudo snap install core; sudo snap refresh core
```
```
sudo apt remove certbot
```
```
sudo snap install --classic certbot
```
```
sudo ln -s /snap/bin/certbot /usr/bin/certbot
```
### 3.2 Confirming Nginx Configuration
```
sudo nano /etc/nginx/sites-available/default
```
Edit this line
```
server_name example.com www.example.com;
```
```
sudo nginx -t
```
```
sudo systemctl reload nginx
```

### 3.3 Obtaining a FREE SSL Certificate
```
sudo certbot --nginx -d app.example.com 
```
Output:
```
IMPORTANT NOTES:
Successfully received certificate.
Certificate is saved at: /etc/letsencrypt/live/your_domain/fullchain.pem
Key is saved at: /etc/letsencrypt/live/your_domain/privkey.pem
This certificate expires on 2022-06-01.
These files will be updated when the certificate renews.
Certbot has set up a scheduled task to automatically renew this certificate in the background.

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
If you like Certbot, please consider supporting our work by:
* Donating to ISRG / Let's Encrypt: https://letsencrypt.org/donate
* Donating to EFF: https://eff.org/donate-le
```

### 3.4 Verifying Certbot Auto-Renewal
```
sudo systemctl status snap.certbot.renew.service
```
Output:
```
○ snap.certbot.renew.service - Service for snap application certbot.renew
     Loaded: loaded (/etc/systemd/system/snap.certbot.renew.service; static)
     Active: inactive (dead)
TriggeredBy: ● snap.certbot.renew.timer
```
To test the renewal process, you can do a dry run with certbot:
```
sudo certbot renew --dry-run
```
## 4. Visit your website with HTTPS://
