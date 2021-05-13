# Sử dụng Lệnh để cài đặt Virtual Machine

## Mục lục

[1. Tìm hiểu qua về virt-install](#tim-hieu)

[2. 2. Tìm hiểu các options khi sử dụng virt-install để tạo VM](#option)

## <a name ="tim-hieu"> </a> 1. Tìm hiểu qua về virt-install

`virt-install` là công cụ command line dùng cho việc tạo máy ảo trên KVM sử dụng thư viện quản lí "libvirt".

`virt-install` hỗ trợ cả dạng text lẫn đồ họa, người sử dụng có thể lựa chọn giữa VNC - SDL cho chế độ cài đồ họa hoặc text console. Bên cạnh đó, bạn cũng có thể lựa chọn cài đặt dùng 1 hoặc nhiều các thành phần như virtual disks, card mạng, ...

`virt-install` có thể lấy các file cài đặt từ trên NFS, HTTP, FTP servers để tiến hành cài máy ảo. Bên cạnh đó, nó cũng cho phép sử dụng PXE booting hoặc file image.

Chỉ cần nhập đúng những dòng lệnh, virt-install sẽ tự động cài đặt máy ảo mà không cần sự giám sát. Nó cũng có chế độ tương tác với `option --prompt` nhưng virt-install sẽ chỉ yêu cầu những tùy chọn tối thiểu.

Có 3 cách tạo VM bằng `virt-install`:

- Tạo từ file ISO.

- Tạo từ file Image.

- Tạo từ Internet.

##  <a name ="option"> </a> 2. Tìm hiểu các options khi sử dụng virt-install để tạo VM

**Thông số tối thiểu để tạo một VM bao gồm các options sau:**

- `--name`

- `--ram`

- `--disk`

- `--filesystem` or `--nodisks`

- Các tùy chọn cài đặt.

**Các tùy chọn thông thường:**

- `-h` hoặc `--help`: Show các tùy chọn.

- `-n` hoặc `--name = NAME`: Tên của máy ảo muốn tạo, nó không được trùng với các máy ảo đang chạy.

- `-r` hoặc `--ram=RAM`: RAM cho máy ảo (theo megabytes).

- `--arch=ARCH` : Nếu bỏ qua tùy chọn này, kiến trúc CPU của máy chủ sẽ được sử dụng cho máy ảo.

- `-u` hoặc `--uuid` : UUID cho máy ảo, nếu bỏ qua, UUID sẽ được sinh ngẫu nhiên.

- `--vcpus = VCPUS[, maxvcpus=MAX][, sockets=#][,cores=#][,threads=#]` : Số lượng CPU cho máy ảo, nếu maxvcpus được thiết lập, máy ảo sẽ có thể sử dụng tới số lượng tối đa CPU đã cho phép, tuy nhiên nó sẽ khởi động với số VCPUS ban đầu.

- `--cpuset=CPUSET` : Cho biết cpu vật lý nào sẽ được máy ảo sử dụng, nếu chọn auto, máy ảo sẽ tự động chọn lựa.

- `--cpu MODEL` : Lựa chọn CPU models, xem các model được liệt kê tại file `cpu_map.xml` trong thư mục của `libvirt`. Ví dụ: `--cpu core2duo`

- `--description` : Mô tả cho máy ảo.

- `--security type = TYPE[,label=LABEL][,relabel=yes|no]` : Thiết lập domain security driver, có thể chọn `dynamic` hoặc `static`.

**Các tùy chọn cài đặt:**

- `-c` hoặc `--cdrom=CDROM` : File hoặc thiết bị được sử dụng như ổ đĩa ảo để cài đặt máy ảo. Nó có thể là đường dẫn đến file ISO trong máy hoặc ổ địa, hoặc cũng có thể là đường link (giống với tùy chọn `--location`). Nếu cdrom đã được thiết lập thông qua `--disk` thì nó sẽ mặc định được sử dụng làm phương tiện cài đặt.

- `-l` hoặc `--location=LOCATION` : Đường dẫn tới file cài đặt từ trên internet.

- `--pxe` : Sử dụng giao thức boot PXE để tiến hành cài đặt máy ảo.

- `--import` : Bỏ qua phần cài đặt os cho máy ảo, dùng file image có sẵn thông qua tùy chọn `--disk` hoặc `filesystem`.