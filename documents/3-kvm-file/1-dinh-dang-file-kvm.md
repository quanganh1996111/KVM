# Định dạng file trong KVM

## Mục lục

[1. File Image trong KVM](#file-image)

[2. Định dạng file trong KVM](#dinh-dang)

[Nguồn tham khảo](#tham-khao)

## <a name ="file-image"> </a> 1. File Image trong KVM

Công nghệ ảo hóa phần cứng QEMU rất giống với KVM. Cả hai đều được điều khiển thông qua libvirt, cả hai đều hỗ trợ tính năng thiết lập giống nhau, và tất cả các hình ảnh máy ảo tương thích với KVM thì cũng tương thích với QEMU. Điểm khác biệt chính giữa QEMU và KVM là QEMU không hỗ trợ ảo hóa bản địa (native virtualization); do đó, QEMU có hiệu suất kém hơn KVM.

QEMU được sử dụng trong các trường hợp sau:

- Chạy trên các phần cứng cũ không hỗ trợ ảo hóa.

- Chạy các dịch vụ Compute trên một máy ảo cho mục đích phát triển hoặc thử nghiệm.

QEMU hỗ trợ các định dạng hình ảnh máy ảo sau đây:

- Raw

- QEMU Copy-on-write (qcow2)

- VMWare virtual machine disk format (vmdk)

File Image (còn gọi là file ảnh) của đĩa CD/DVD chính là một dạng file có định dạng theo các chuẩn tạo file ảnh. File image là một file đóng gói hết tất cả nội dung của một đĩa CD/DVD vào trong nó.

Trong KVM Guest có 2 thành phần chính đó là VM definition được lưu dưới dạng file xml tại /etc/libvirt/qemu. File này chứa các thông tin của máy ảo như tên, số ram, số cpu... File còn lại là storage thường được lưu dưới dạng file image tại thư mục /var/lib/libvirt/images.

3 định dạng thông dụng nhất của file image sử dụng trong KVM đó là ISO, raw và qcow2.

## <a name ="dinh-dang"> </a> 2. Định dạng file trong KVM

### 2.1. File ISO

File ISO là file ảnh của 1 đĩa CD/DVD, nó chứa toàn bộ dữ liệu của đĩa CD/DVD đó. File ISO thường được sử dụng để cài đặt hệ điều hành vủa VM, người dùng có thể import trực tiếp hoặc tải từ trên internet về.

Boot từ file ISO cũng là một trong số những tùy chọn mà người dùng có thể sử dụng khi tạo máy ảo.

### 2.2. File raw

Là định dạng file image phi cấu trúc

Khi người dùng tạo mới một máy ảo có disk format là raw thì dung lượng của file disk sẽ đúng bằng dung lượng của ổ đĩa máy ảo bạn đã tạo.

Định dạng raw là hình ảnh theo dạng nhị phân (bit by bit) của ổ đĩa.

Mặc định khi tạo máy ảo với virt-manager hoặc không khai báo khi tạo VM bằng virt-install thì định dạng ổ đĩa sẽ là raw. Hay nói cách khác, raw chính là định dạng mặc định của QEMU.

### 2.3. File qcow2

Qcow2 là một định dạng tập tin cho image nơi các tập tin được sử dụng bởi QEMU. Viết tắt của "QEMU Copy On Write " và sử dụng một cách thức tối ưu hóa lưu trữ disk để trì hoãn phân bổ dung lượng lưu trữ cho đến khi nó thực sự cần thiết. Các tập tin trong định dạng qcow có thể chứa một loạt các disk image thường được gắn liền với client cụ thể các hệ điều hành . Hai phiên bản của các định dạng tồn tại: qcow, và qcow2, trong đó sử dụng các .qcow và .qcow2 mở rộng tập tin, tương ứng.

Qcow2 là một phiên bản cập nhật của định dạng qcow, nhằm để thay thế. Khác biệt với bản gốc là qcow2 hỗ trợ nhiều snapshots thông qua một mô hình mới, linh hoạt để lưu trữ ảnh chụp nhanh. Khi khởi tạo máy ảo mới sẽ dựa vào disk này rồi snapshot thành một máy mới.

Qcow2 hỗ trợ copy-on-write với những tính năng đặc biệt như snapshot, mã hóa ,nén dữ liệu...

- Các tập tin với định dạng này có thể phát triển khi dữ liệu được thêm vào. Điều này cho phép kích thước tệp nhỏ hơn hình ảnh đĩa thô , phân bổ toàn bộ không gian image vào tệp, ngay cả khi các phần của nó trống. Điều này đặc biệt hữu ích cho các hệ thống tập tin không hỗ trợ các lỗ hổng, chẳng hạn như FAT32.

- Định dạng qcow cũng cho phép lưu trữ các thay đổi được thực hiện với một base image chỉ đọc trên một tập tin qcow riêng biệt bằng cách sử dung copy on write . Tập tin qcow mới này chứa đường dẫn đến base image để có thể tham chiếu trở lại khi cần thiết. Khi một phần dữ liệu cụ thể đã được đọc từ image mới này, nội dung sẽ được lấy ra từ nó nếu nó là mới và được lưu giữ ở đó; Nếu không, dữ liệu sẽ được lấy ra từ base image.

- Tính năng tùy chọn bao gồm AES mã hóa và zlib dựa trên giải nén trong suốt .

- Một bất lợi của hình ảnh qcow là không thể được gắn trực tiếp như disk image.

Copy on write (cow), đôi khi được gọi là chia sẻ tiềm ẩn, là một kỹ thuật quản lý tài nguyên được sử dụng trong lập trình máy tính để thực hiện có hiệu quả thao tác "nhân bản" hoặc "sao chép" trên các tài nguyên có thể thay đổi. Nếu một tài nguyên được nhân đôi nhưng không bị sửa đổi, không cần thiết phải tạo một tài nguyên mới. Tài nguyên có thể được chia sẻ giữa bản sao và bản gốc. Sửa đổi vẫn phải tạo ra một bản sao, do đó kỹ thuật: các hoạt động sao chép được hoãn đến việc viết đầu tiên. Bằng cách chia sẻ tài nguyên theo cách này, có thể làm giảm đáng kể lượng tiêu thụ tài nguyên của các bản sao chưa sửa đổi.

Qcow2 hỗ trợ việc tăng bộ nhớ bằng cơ chế Thin Provisioning (Máy ảo dùng bao nhiêu file có dung lượng bấy nhiêu).

**Cách chuyển đổi giữa RAW và qcow2**

- Chuyển đổi từ qcow2 sang raw:

qemu-img convert -f qcow2 -O raw /<đường_dẫn_file_qcow2> /<đường_dẫn_lưu_file_raw>

- Chuyển đổi từ raw sang qcow2:

qemu-img convert -f raw -O qcow2/<đường_dẫn_file_raw> /<đường_dẫn_lưu_file_qcow2>

## <a name ="tham-khao"> </a> Nguồn tham khảo

https://github.com/domanhduy/ghichep/blob/master/DuyDM/KVM/docs/Phan-biet-raw-qcow-iso-cua-image.md

https://docs.openstack.org/image-guide/convert-images.html