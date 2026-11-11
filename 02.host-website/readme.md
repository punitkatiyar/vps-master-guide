# Host Static Website using nginx

> sudo apt install nginx -y

> sudo systemctl start nginx

> sudo systemctl enable nginx

> sudo systemctl status nginx

## Connect your domain to VPS (DNS Configuration)

```
Type	Host	Value
A	@	YOUR_VPS_IP
A	www	YOUR_VPS_IP
```

## Access FTP For Root 

> apt install vsftpd -y

## Install SSL Certificate (HTTPS)

> sudo apt install certbot python3-certbot-nginx -y

> sudo certbot --nginx -d example.com -d www.example.com

> sudo certbot renew --dry-run


