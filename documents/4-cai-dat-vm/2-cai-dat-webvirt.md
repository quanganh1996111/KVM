# Cài đặt Webvirt trên CentOS7

## Mục lục

[1. Chuẩn bị môi trường](#moi-truong)

[2. Mô hình triển khai](#mo-hinh)

[3. Cài đặt Server Webvirt](#cai-dat)

## <a name ="moi-truong"> </a> 1. Chuẩn bị môi trường

Chuẩn bị Một Server cài đặt KVM - Một Server cài đặt Webvirt.

- Cấu hình Server Webvirt cùng dải IP với Server KVM:

```
TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=static
DEFROUTE=yes
NAME=ens33
UUID=433d8c16-4dc9-45ce-a362-1928089318bc
DEVICE=ens33
ONBOOT=yes
IPADDR=192.168.1.151
NETMASK=255.255.255.0
GATEWAY=192.168.1.1
DNS1=8.8.8.8
```

- Disable `Firewalld` và `SElinux`:

```
sudo systemctl disable firewalld
sudo systemctl stop firewalld
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/sysconfig/selinux
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
```

- Update OS

```
yum install epel-release
yum update -y
```

## <a name ="mo-hinh"> </a> 2. Mô hình triển khai

<img src="https://imgur.com/5GsLQi1.png">

## <a name ="cai-dat"> </a> 3. Cài đặt Server Webvirt

**Bước 1:** Cài đặt các package cần thiết:

```
yum -y install git python-pip libvirt-python libxml2-python python-websockify supervisor nginx
yum -y install gcc python-devel
pip install numpy
```

**Bước 2:** Thiết lập thư mục cho webvirt, python và môi trường Django:

```
mkdir /var/www/ 
cd /var/www
```

- Trong thư mục `/www` vừa tạo, clone thư viện webvirt về:

```
git clone git://github.com/retspen/webvirtmgr.git
```

- Cài đặt môi trường cho webvirt:

```
cd webvirtmgr
sudo pip install -r requirements.txt
```

- Đồng bộ Database:

```
./manage.py syncdb
```

<img src="https://imgur.com/lqqg5kp.png">

Username/Pass chính là nhập thông tin tài khoản để login vào webvirt

- Thu thập thông tin Database:

```
./manage.py collectstatic
```

<img src="https://imgur.com/GK6qXc6.png">

**Bước 3:** Cấu hình Webserver nginx

- Tạo file config:

```
vi /etc/nginx/conf.d/webvirtmgr.conf
```

Nội dung của file config:

```
server {
    listen 80 default_server;

    server_name $hostname;
    #access_log /var/log/nginx/webvirtmgr_access_log; 

    location /static/ {
        root /var/www/webvirtmgr/webvirtmgr; # or /srv instead of /var
        expires max;
    }

    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-for $proxy_add_x_forwarded_for;
        proxy_set_header Host $host:$server_port;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_connect_timeout 600;
        proxy_read_timeout 600;
        proxy_send_timeout 600;
        client_max_body_size 1024M; # Set higher depending on your needs 
    }
}
```

- Chỉnh sửa file config của nginx, comment vào khối `server`

```
vi /etc/nginx/nginx.conf
```

<img src="https://imgur.com/DI0KlmB.png">

- Khởi động lại nginx:

```
systemctl restart nginx
systemctl enable nginx
chkconfig supervisord on
```

**Bước 4:** Cấu hình supervisor

```
sudo chown -R nginx:nginx /var/www/webvirtmgr
```

- Tạo file `webvirtmgr.ini`:

```
vi /etc/supervisord.d/webvirtmgr.ini
```
Chèn nội dung:

```
[program:webvirtmgr]
command=/usr/bin/python /var/www/webvirtmgr/manage.py run_gunicorn -c /var/www/webvirtmgr/conf/gunicorn.conf.py
directory=/var/www/webvirtmgr
autostart=true
autorestart=true
logfile=/var/log/supervisor/webvirtmgr.log
log_stderr=true
user=nginx

[program:webvirtmgr-console]
command=/usr/bin/python /var/www/webvirtmgr/console/webvirtmgr-console
directory=/var/www/webvirtmgr
autostart=true
autorestart=true
stdout_logfile=/var/log/supervisor/webvirtmgr-console.log
redirect_stderr=true
user=nginx
```

- Stop/start supervisord:

```
systemctl stop supervisord
sudo systemctl start supervisord
```

**Bước 5:** Update thay đổi trong webvirtmgr

```
cd /var/www/webvirtmgr
git pull
./manage.py collectstatic
systemctl restart supervisord
```

- Tiến hành debug lại:

```
./manage.py runserver 0:8000
```

<img src="https://imgur.com/cFbBBtR.png">

**Bước 6:** Truy cập vào Webvirt

<img src="https://imgur.com/t91bHRn.png">

<img src="https://imgur.com/c7iCKgN.png">

