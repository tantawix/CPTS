- الweb transfer من احسن الطرق اللي ممكن تنقل بيها ملفات لأن لو في firewall هو بيسمح اصلا بالhttp فا مش هيمنعك وكمان في معظم الحالات الملفات اللي بتتنقل عن طريق الhttp بتكون encrypted
- شرحنا قبل كده ازاي نرفع ملفات عن طريق الuploadserver بس احنا المرادي هنستخدم apache او nginx
---
## Nginx - Enabling PUT

- A good alternative for transferring files to `Apache` is [Nginx](https://www.nginx.com/resources/wiki/) because the configuration is less complicated, and the module system does not lead to security issues as `Apache` can.
#### Create a Directory to Handle Uploaded Files

```shell-session
0xTroj6n@htb[/htb]$ sudo mkdir -p /var/www/uploads/SecretUploadDirectory
```
#### Change the Owner to www-data

```shell-session
0xTroj6n@htb[/htb]$ sudo chown -R www-data:www-data /var/www/uploads/SecretUploadDirectory
```
#### Create Nginx Configuration File

Create the Nginx configuration file by creating the file `/etc/nginx/sites-available/upload.conf` with the contents:

```shell-session
server {
    listen 9001;
    
    location /SecretUploadDirectory/ {
        root    /var/www/uploads;
        dav_methods PUT;
    }
}
```
#### Symlink our Site to the sites-enabled Directory

```shell-session

0xTroj6n@htb[/htb]$ sudo ln -s /etc/nginx/sites-available/upload.conf /etc/nginx/sites-enabled/
```
#### Start Nginx

```shell-session
0xTroj6n@htb[/htb]$ sudo systemctl restart nginx.service
```

If we get any error messages, check `/var/log/nginx/error.log`. If using Pwnbox, we will see port 80 is already in use.

#### Verifying Errors

```shell-session
0xTroj6n@htb[/htb]$ tail -2 /var/log/nginx/error.log

2020/11/17 16:11:56 [emerg] 5679#5679: bind() to 0.0.0.0:`80` failed (98: A`ddress already in use`)
2020/11/17 16:11:56 [emerg] 5679#5679: still could not bind()
```
#### Remove NginxDefault Configuration

```shell-session
0xTroj6n@htb[/htb]$ sudo rm /etc/nginx/sites-enabled/default
```
#### Upload File Using cURL

```shell-session
0xTroj6n@htb[/htb]$ curl -T /etc/passwd http://localhost:9001/SecretUploadDirectory/users.txt
```


```shell-session
0xTroj6n@htb[/htb]$ sudo tail -1 /var/www/uploads/SecretUploadDirectory/users.txt 

user65:x:1000:1000:,,,:/home/user65:/bin/bash
```
---
