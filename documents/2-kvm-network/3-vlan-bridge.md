# Cấu hình VLAN với Bridge trên KVM.

## 1. Mô hình

<img src="https://imgur.com/3OmOGFO.png">

Cổng vật lý `em1`: Cấu hình Bridge `br0` kết nối với Internet.

Cổng vật lý `em2`: Cấu hình VLAN - Bridge `vlan40` và `vlan41` với Switch Local.

## 2. Cấu hình VLAN

### Cấu hình công vật lý `em2`

```
TYPE=Ethernet
BOOTPROTO=none
NAME=em2
DEVICE=em2
ONBOOT=yes
```

### Cấu hình cổng `em2` nối với `vlan40` với `vlan41` bằng Bridge

- VLAN40:

```
DEVICE=em2.40
BOOTPROTO=none
ONBOOT=yes
VLAN=yes
BRIDGE=vlan40
TYPE=Ethernet
NM_CONTROLLED=no
```

- VLAN41:

```
DEVICE=em2.41
BOOTPROTO=none
ONBOOT=yes
VLAN=yes
BRIDGE=vlan40
TYPE=Ethernet
NM_CONTROLLED=no
```

### Cấu hình vlan

- VLAN40:

```
DEVICE=vlan40
TYPE=Bridge
BOOTPROTO=none
ONBOOT=yes
NM_CONTROLLED=no
IPADDR=10.10.40.2
NETMASK=255.255.255.0
GATEWAY=10.10.40.1
```

- VLAN41:

```
DEVICE=vlan41
TYPE=Bridge
BOOTPROTO=none
ONBOOT=yes
NM_CONTROLLED=no
IPADDR=10.10.41.2
NETMASK=255.255.255.0
GATEWAY=10.10.41.1
```

Sau khi cấu hình xong, restart lại network: `systemctl restart network`

Cấu hình Network:

<img src="https://imgur.com/W6bMwth.png">

Kiểm tra ping đến địa chỉ trên Switch Local `10.10.40.254` và `10.10.41.254`:

<img src="https://imgur.com/Tuz9Gyp.png">