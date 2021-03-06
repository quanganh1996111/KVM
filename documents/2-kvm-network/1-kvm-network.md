# Tổng quan về mô hình mạng trong KVM

## Mục lục

[1. Linux-bridge](#bridge)

[2. NAT](#nat)

[Nguồn tham khảo](#tham-khao)

KVM được biết đến là một cơ sở hạn tầng ảo hóa cho nhân Linux. KVM cũng cung cấp các mô hình mạng trong việc ảo hóa network. Các mô hình bao gồm:

- NAT.

- Host-only.

- Linux-bridge.

## <a name ="bridge"> </a> 1. Linux-bridge

### 1.1. Khái niệm - ứng dụng

Linux bridge là một phần mềm đươc tích hợp vào trong nhân Linux để giải quyết vấn đề ảo hóa phần network trong các máy vật lý. Về mặt logic Linux bridge sẽ tạo ra một con switch ảo để cho các VM kết nối được vào và có thể nói chuyện được với nhau cũng như sử dụng để ra mạng ngoài

Ngoài ra khi tìm hiểu Linux bridge còn có một số thuật ngữ như:

- Port : tương tự như cổng của một con switch thật

- Bridge: tương đương với từ switch

- tap : được hiểu ở lớp 2

- FDB : Forwading database

### 1.2. Cấu trúc

**Cấu trúc của Virtual Switch:**

<img src="https://imgur.com/Sqg4lqk.png">

Chúng ta có thể thấy rằng có một con switch được tạo ra nằm bên trong của máy vật lý. Các VM kết nối đến đây để có thể liên lạc được với nhau. Nếu muốn liên lạc ra bên ngoài ta có thể kết nối con switch này với card mạng trên máy vật lý của ta (giống như ta dùng dây kết nối switch với router). Ta có thể kết nối switch với 1 hoặc nhiều port.

Chú ý ta không thể kết nối switch ảo với card wireless do HĐH không hỗ trợ.

**Cấu trúc của Linux-bridge:**

<img src="https://imgur.com/FUSVibY.png">

- Tap : có thể hiểu nó là một giao diên mạng để các máy ảo có thể giao tiếp được với bridge và nó nằm trong nhân kernel. Tap hoat động ở lớp 2 trong mô hình OSI

- fd (forward data): dùng để chuyển tiếp data từ máy ảo

**Chức năng của một switch ảo do Linux bridge tạo ra:**

- STP: là tính năng chống loop gói tin trong switch.

- VLan: là tính năng rất quan trọng trong một switch.

- FDB: là tính năng chuyển gói tin theo database được xây dựng giúp tăng tốc độ của switch.

## <a name ="nat"> </a> 1. NAT

**Mô hình:**

<img src="https://imgur.com/KDLI9qv.png">

Nếu như với mô hình linux bridge KVM tạo ra một virtual switch thì ta cũng có thể hình dung với mô hình mạng NAT này KVM sẽ tạo ra một thiết bị là virtual router. Khi ta tạo một dải mạng với mô hình NAT thì lúc này virtual router sẽ NAT từ dải mạng mà ta tạo ra ra địa chỉ của card mạng vật lý trên KVM host để đi ra ngoài internet.

Khi một dải mạng tạo ra ta sẽ thấy trên KVM host xuất hiện một thêm một card mạng. Card mạng này đóng vai trò là gateway cho dải mạng mà ta tạo ra.

**Cấu trúc hoạt động:**

<img src="https://imgur.com/LcyE3ax.png">

VM sẽ được gán một IP trên mạng con khác với Máy chủ Vật lý, nhưng VM có khả năng giao tiếp với mạng bên ngoài giống như máy chủ, nhưng các máy chủ từ bên ngoài không thể truy cập trực tiếp vào máy ảo của bạn.

## <a name ="tham-khao"> </a> Nguồn tham khảo

https://jamielinux.com/docs/libvirt-networking-handbook/bridged-network.html

https://blog.cloud365.vn/linux/mo-hinh-mang-nat-kvm/