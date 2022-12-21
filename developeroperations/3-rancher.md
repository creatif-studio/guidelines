# Rancher

Rancher is a complete stack that eases the operational and security challenges of managing multiple container clusters on the cloud.

## Installation

Please run this command line by line:

If you don't want to expose to the internet

```bash
docker run -d --privileged --restart=unless-stopped -p 80:80 -p 443:443 -v /opt/rancher:/var/lib/rancher --name rancher rancher/rancher:v2.7.0
```

If you want to expose to the internet

```bash
docker run -d --privileged --restart=unless-stopped -p 80:80 -p 443:443 -v /opt/rancher:/var/lib/rancher --name rancher rancher/rancher:v2.5.5 --acme-domain kubernetes.domain.com
```

## Integration with Nginx Let's Encrypt (Optional)

```bash
sudo apt install nginx
sudo apt install 
sudo snap install core; sudo snap refresh core
sudo snap install --classic certbot
sudo ln -s /snap/bin/certbot /usr/bin/certbot
sudo certbot --nginx
sudo certbot renew --dry-run
```

Create cronjob
```bash
crontab -e

# insert this line to the cronjob
0 0 11 */3 * sudo bash /root/.scripts/cronssl.sh
```

Create a new bash script in /root/.scripts/cronssl.sh

```bash
#!/usr/bin/env bash

# stopping rancher
docker stop rancher-270

# renewing cert
systemctl start nginx
certbot renew --post-hook "systemctl reload nginx"
ln -s /etc/letsencrypt/live/rch.muunship.com/* sslcerts/
systemctl stop nginx

# starting rancher
docker start rancher-270
```
