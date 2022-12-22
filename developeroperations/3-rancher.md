# Rancher

Rancher is a complete stack that eases managing multiple container clusters on the cloud. You can setup with 2 methods:

- Private Cluster: We can't access from local (on-premise)
- Public Cluster: We can access from local (cloud)

## Private Cluster Installation

Please run this command line by line:

If you don't want to expose to the internet

```bash
docker run -d --privileged --restart=unless-stopped -p 80:80 -p 443:443 -v /opt/rancher:/var/lib/rancher --name rancher rancher/rancher:v2.7.0
```

## Public Cluster Installation

If you want to expose rancher with Nginx and Let's Encrypt, please replace every domain `kubernetes.domain.com` in this method.

Please run this command line by line:

```bash
sudo apt install -y nginx
```

Create a new file for rancher reverse proxy, in this location `/etc/nginx/sites-enabled/kubernetes.domain.com`

```bash
server {
    server_name kubernetes.domain.com;

    location / {
        proxy_pass http://localhost;
    }
}
```

Install certbot for generate SSL (Let`s Encrypt)

```bash
sudo snap install core; sudo snap refresh core
sudo snap install --classic certbot
sudo ln -s /snap/bin/certbot /usr/bin/certbot
sudo certbot --nginx
sudo certbot renew --dry-run
```

Create cronjob

```bash
sudo crontab -e
```

Insert this line to the cronjob

```bash
0 0 11 */3 * sudo bash /home/ubuntu/.scripts/cronssl.sh
```

Create a new bash script in `/home/ubuntu/.scripts/cronssl.sh`

```bash
#!/usr/bin/env bash

# stopping rancher
docker stop rancher

# renewing cert
systemctl start nginx
certbot renew --post-hook "systemctl reload nginx"
ln -s /etc/letsencrypt/live/kubernetes.domain.com/* sslcerts/
systemctl stop nginx

# starting rancher
docker start rancher
```

Stop nginx service

```bash
sudo systemctl stop nginx
```

Start rancher

```bash
docker run -d -v "/home/ubuntu/sslcerts:/etc/rancher/ssl" -v /opt/rancher:/var/lib/rancher --privileged --restart=unless-stopped -p 80:80 -p 443:443 --name rancher rancher/rancher:v2.7.0 --acme-domain kubernetes.domain.com
```
