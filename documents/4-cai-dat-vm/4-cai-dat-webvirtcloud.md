# Cài đặt Webvirtcloud để quản lý các Node KVM

## Phần 1. Chuẩn bị

Chuẩn bị một Server chạy CentOS 7

- CPUs: 2 Cores.

- RAM: 2GB.

- Disk: 20GB.

Mô hình: Tương tự như mô hình cài đặt Webvirt ở bài trước.

Đặt địa chỉ IP: 172.16.2.16

## Phần 2. Cài đặt

### Bước 1: Update OS

```
yum install epel-release -y

yum update -y
```

### Bước 2: Cài đặt các gói cần thiết

```
yum -y install python-virtualenv python-devel libvirt-devel glibc gcc nginx supervisor python-lxml git python-libguestfs
```

### Bước 3: Tạo thư mục và Clone Source Code từ trang chủ về

```
cd /srv

git clone https://github.com/retspen/webvirtcloud && cd webvirtcloud

git checkout 1e2fbc8

cp webvirtcloud/settings.py.template webvirtcloud/settings.py
```

### Bước 4: Thay thế Secret Key

```
cd /srv/webvirtcloud

vi webvirtcloud/settings.py

```

Thay đổi giá trị của `SECRET_KEY = 'anhtq1996'`.

### Bước 5: Cài đặt Webvirt Cloud

```
cd /srv/webvirtcloud

virtualenv venv

source venv/bin/activate

venv/bin/pip install -r conf/requirements.txt

cp conf/nginx/webvirtcloud.conf /etc/nginx/conf.d/

venv/bin/python manage.py migrate

```

### Bước 6: Cấu hình Supervisor

```
cp /etc/supervisord.conf /etc/supervisord.conf.bk

vi /etc/supervisord.conf
```

Thêm cấu hình sau vào `/etc/supervisord.conf`:

```
[program:webvirtcloud]
command=/srv/webvirtcloud/venv/bin/gunicorn webvirtcloud.wsgi:application -c /srv/webvirtcloud/gunicorn.conf.py
directory=/srv/webvirtcloud
user=nginx
autostart=true
autorestart=true
redirect_stderr=true

[program:novncd]
command=/srv/webvirtcloud/venv/bin/python /srv/webvirtcloud/console/novncd
directory=/srv/webvirtcloud
user=nginx
autostart=true
autorestart=true
redirect_stderr=true
```

### Bước 7: Cấu hình nginx

- Block khối `server` trong file `/etc/nginx/nginx.conf`

```
/etc/nginx/nginx.conf
```

```
#    server {
#        listen       80 default_server;
#        listen       [::]:80 default_server;
#        server_name  _;
#        root         /usr/share/nginx/html;
#
#        # Load configuration files for the default server block.
#        include /etc/nginx/default.d/*.conf;
#
#        location / {
#        }
#
#        error_page 404 /404.html;
#            location = /40x.html {
#        }
#
#        error_page 500 502 503 504 /50x.html;
#            location = /50x.html {
#        }
#    }
```

- Chỉnh sửa file `/etc/nginx/conf.d/webvirtcloud.conf`

```
upstream gunicorn_server {
    #server unix:/srv/webvirtcloud/venv/wvcloud.socket fail_timeout=0;
    server 127.0.0.1:8000 fail_timeout=0;
}
server {
    listen 80;

    server_name servername.domain.com;
    access_log /var/log/nginx/webvirtcloud-access_log; 

    location /static/ {
        root /srv/webvirtcloud;
        expires max;
    }

    location / {
        proxy_pass http://gunicorn_server;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-for $proxy_add_x_forwarded_for;
        proxy_set_header Host $host:$server_port;
        proxy_set_header X-Forwarded-Proto $remote_addr;
        proxy_connect_timeout 600;
        proxy_read_timeout 600;
        proxy_send_timeout 600;
        client_max_body_size 1024M;
    }
}
```

- Phân quyền cho thư mục chứa Code:

```
chown -R nginx:nginx /srv/webvirtcloud
```

- Restart lại các Dịch vụ

```
systemctl restart nginx && systemctl restart supervisord

systemctl enable nginx && systemctl enable supervisord
```

## Phần 3. Add host KVM vào Webvirt

Cấu hình tương tự như ở **Phần 4. Add host KVM vào Webvirt** trong bài trước.

Giao diện sau khi hoàn thành:

<img src="https://imgur.com/hcRzaQ0.png">