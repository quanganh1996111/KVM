# Tạo và quản lý Linux-bridge



## 1. Chuẩn bị môi trường KVM

Chuẩn bị Một Server có cài đặt KVM - Hệ điều hành CentOS 7

Trên Server có Cổng mạng Vật lý `em1`, trước tiên ta cần cấu hình Cổng vật lý `em1` nối với Bridge `br0`

- Cấu hình trên Cổng vật lý `em1`:

`vi /etc/sysconfig/network-scripts/ifcfg-em1`

```
TYPE=Ethernet
NAME=em1
DEVICE=em1
ONBOOT=yes
HWADDR=84:2b:2b:4e:b9:87
BRIDGE=br0
```

Lưu ý: phần `HWADDR` kiểm tra đúng thông tin địa chỉ MAC, trước khi cấu hình dùng lệnh `ip a` để kiểm tra địa chỉ MAC.

- Tạo cấu hình cho `br0`:

`vi /etc/sysconfig/network-scripts/ifcfg-br0`

```
DEVICE=br0
TYPE=Bridge
BOOTPROTO=static
IPADDR=172.16.2.13
GATEWAY=172.16.10.1
NETMASK=255.255.240.0
ONBOOT=yes
DNS1=8.8.8.8
```

`BRIDGE=br0` trên cấu hình của `em1` phải đúng với cấu hình `DEVICE=br0` trên cấu hình của `br0`

Sau khi cấu hình xong restart lại network để nhận cấu hình mới.

<img src="https://imgur.com/KehqwOX.png">

## 2. Cài đặt VM trên KVM nhận card Bridge

Chọn Network vào `br0` để VM nhận card mạng `bridge`.

<img src="https://imgur.com/OVfrc0y.png">

Sau khi hoàn thành cài đặt, kiểm tra thấy VM đã nhận IP với dải IP của Card vật lý

<img src="https://imgur.com/MEwMhPc.png">

