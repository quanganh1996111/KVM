# Tìm hiểu về file XML trong KVM

[1. Giới thiệu về file XML trong KVM](#gioi-thieu)

[2. Các thành phần trong file domain XML](#thanh-phan)

## <a name ="gioi-thieu"> </a> 1. Giới thiệu về file XML trong KVM

XML (viết tắt từ tiếng Anh: eXtensible Markup Language, tức "Ngôn ngữ đánh dấu mở rộng") là ngôn ngữ đánh dấu với mục đích chung do W3C đề nghị, để tạo ra các ngôn ngữ đánh dấu khác. Đây là một tập con đơn giản của SGML, có khả năng mô tả nhiều loại dữ liệu khác nhau. Mục đích chính của XML là đơn giản hóa việc chia sẻ dữ liệu giữa các hệ thống khác nhau, đặc biệt là các hệ thống được kết nối với Internet.

VM trong KVM có hai thành phần chính đó là VM's definition được lưu dưới dạng file XML mặc định ở thư mục `/etc/libvirt/qemu` và VM's storage lưu dưới dạng file image.

File domain XML chứa những thông tin về thành phần của máy ảo (số CPU, RAM, các thiết lập của I/O devices...)

`libvirt` dùng những thông tin này để tiến hành khởi chạy tiến trình QEMU-KVM tạo máy ảo.

Ngoài domain XML, KVM cũng có các file XML khác để lưu các thông tin liên quan tới network, storage...

Ví dụ về một file XML:

<img src="https://imgur.com/K56xnrg.png">

## <a name ="thanh-phan"> </a> 2. Các thành phần trong file domain XML

Thẻ không thể thiếu trong file domain xml là `domain`, Nó có 2 thành phần chính, `type` cho biết hypervisor đang sử dụng, `id` là mã nhận dạng của máy ảo.

**Metadata:**

- `name`: Tên máy ảo, chỉ bao gồm kí tự chữ và số và không được trùng với những máy ảo đang chạy.

- `uuid`: Mã nhận dạng quốc tế duy nhất cho máy ảo. Format theo RFC 4122. Nếu thiếu trường uuid khi khởi tạo, mã này sẽ được tự động generate.

- `title`: Tiêu đề của máy ảo.

- `description`: Đoạn mô tả của máy ảo, nó sẽ không được libvirt sửa dụng.

- `metadata`: Chứa những thông tin về file xml.

**Operating system booting:**

Có nhiều các để boot máy ảo và mỗi cách lại có những lợi ích và hạn chế riêng. Bài viết này sẽ chỉ đưa ra 2 ví dụ đó là boot từ BIOS và kernel.

**BIOS Bootloader:**

Boot thông qua BIOS được hỗ trợ bởi những hypervisors full virtualization. Người dùng sẽ phải thiết lập thứ tự ưu tiên các thiết bị boot.

```
<os>
  <type arch='x86_64' machine='pc-i440fx-rhel7.0.0'>hvm</type>
  <boot dev='hd'/>
</os>
```

- `type`: Chỉ ra loại OS được boot để tạo thành VM. hvm cho biết OS cần chạy trên "bare metal", yêu cầu full virtualization. arch chỉ ra loại kiến trúc CPU dùng để ảo hóa, machine chỉ la loại máy sử dụng.

- `boot`: dev chỉ ra thiết bị dùng để khởi động. Nó có thể là fd, hd, cdrom hoặc network. Nếu có nhiều thiết bị được khai báo, nó sẽ được sắp xếp làm thứ tự ưu tiên.

**Direct kernel boot:**

- `loader`: readonly có giá trị yes hoặc no chỉ ra file image writable hay readonly. type có giá trị rom hoặc pflash chỉ ra nơi guest memory được kết nối.

- `kernel`: đường dẫn tới kernel image trên hệ điều hành máy chủ.

- `initrd`: đường dẫn tới ramdisk image trên hđh máy chủ.

- `cmdline`: xác định giao diện điều khiển thay thế.

**CPU Allocation:**

```
<cpu mode='custom' match='exact' check='partial'>
  <model fallback='allow'>Broadwell-noTSX-IBRS</model>
  <feature policy='require' name='md-clear'/>
  <feature policy='require' name='spec-ctrl'/>
  <feature policy='require' name='ssbd'/>
</cpu>
```

- `cpuset`: danh sách các cpu vật lí mà máy ảo sử dụng.

- `current`: chỉ định cho phéo kích hoặt nhiều hơn số cpu đang sử dụng.

- `placement`: vị trí của cpu, giá trị bao gồm static và dynamic, trong đó static là giá trị mặc định.

**Memory Allocation:**

```
<memory unit='KiB'>1048576</memory>
<currentMemory unit='KiB'>1048576</currentMemory>
```

- Dung lượng RAM tối đa ở thời điểm khởi động.

- `unit`: đơn vị, mặc định là KiB (kibibytes = 1024 bytes), có thể sử dụng b (bytes), KB (Kilobytes = 1000 bytes), MB (Megabytes = 1000000 bytes), M hoặc MiB (Mebibytes = 1,048,576 bytes), GB (gigabytes = 1,000,000,000 bytes), G hoặc GiB (gibibytes = 1,073,741,824 bytes), TB (terabytes = 1,000,000,000,000 bytes), T hoặc TiB (tebibytes = 1,099,511,627,776 bytes)

- `maxMemory`: Dung lượng RAM tối đa có thể sử dụng

- `currentMemory`: Dung lượng RAM thực tế đang được sử dụng

**Events configuration:**

```
<on_poweroff>destroy</on_poweroff>
<on_reboot>restart</on_reboot>
<on_crash>destroy</on_crash>
```

- `on_poweroff`: Hành động được thực hiện khi người dùng yêu cầu tắt máy.

- `on_reboot`: Hành động được thực hiện khi người dùng yêu cầu reset máy.

- `on_crash`: Hành động được thực hiện khi có sự cố.

- Những hành động được phép thực thi:

    - `destroy`: Chấm dứt và giải phóng tài nguyên.

    - `restart`: Chấm dứt rồi khởi động lại giữ nguyên cấu hình.

    - `preserve`: Chấm dứt nhưng dữ liệu vẫn được lưu lại.

    - `rename-restart`: Khởi động lại với tên mới.

- `destroy` và `restart` được hỗ trợ trong cả `on_poweroff` và `on_reboot`. `preserve` dùng trong `on_reboot`, `rename-restart` dùng trong `on_poweroff`.

- `on_crash` hỗ trợ 2 hành động:

    - `coredump-destroy`: domain bị lỗi sẽ được dump trước khi bị chấm dứt và giải phóng tài nguyên.

    - `coredump-restart`: domain bị lỗi sẽ được dump trước khi được khởi động lại với cấu hình cũ.

**Hypervisor features:**

```
<features>
  <acpi/>
  <apic/>
</features>
```

- `pae`: Chế độ mở rộng địa chỉ vật lí cho phép sử dụng 32 bit để lưu trữ tới hơn 4GB bộ nhớ.

- `acpi`: Được sử dụng để quản lí nguồn điện.

- `apic`: Sử dụng cho quản lí IRQ.

- `hap`: Bật/tắt chết độ phần cứng hỗ trợ, mặc định nó sẽ bật.

**Time keeping:**

```
<clock offset='utc'>
  <timer name='rtc' tickpolicy='catchup'/>
  <timer name='pit' tickpolicy='delay'/>
  <timer name='hpet' present='no'/>
</clock>
```

- `offset`: giá trị `utc`, `localtime`, `timezone` và `variable`.

**Devices:**

```
<devices>
    <emulator>/usr/libexec/qemu-kvm</emulator>
```

Đường dẫn tới thiết bị mô phỏng nhị phân. Trong KVM, đó là /usr/bin/kvm

**Hard drives, floppy disks, CDROMs:**

### Disk

```
<disk type='file' device='disk'>
    <driver name='qemu' type='qcow2'/>
    <source file='/var/lib/libvirt/images/centos7-01.qcow2'/>
    <target dev='hda' bus='ide'/>
    <address type='drive' controller='0' bus='0' target='0' unit='0'/>
</disk>
```

- `disk`: Mô tả ổ đĩa, bao gồm các giá trị:

    - `type`: kiểu ổ đĩa, có thể chọn "file", "block", "dir", "network" hoặc "volume".

    - `device`: Cách ổ đĩa tiếp xúc với hệ điều hành. Các giá trị có thể chọn là "floppy", "disk", "cdrom", "lun". Giá trị mặc định là "disk".

    - `snapshot`: Chọn chế độ mặc định của ổ đĩa khi snapshot. Các giá trị ở đây là "internal", "external" và "no".

- `source`:

    - `file`: Đường dẫn tới ổ đĩa.

    - `dir`: Đường dẫn tới thư mục chứa ổ đĩa.

- `target`: 

    - `dev`: tên loại ổ đĩa, ví dụ: vda, hda...

    - `bus`: xác định loại thiết bị ổ đĩa để mô phỏng, các giá trị : "ide", "scsi", "virtio", "xen", "usb", "sata", or "sd" "sd".

- `address`:

    - `type`: Loại controller, có thể chọn "pci" hoặc "drive", đối với "drvie", các giá trị "controller", "bus", "target", và "unit" sẽ được mặc định thêm vào và có giá trị là 0.

### Controller

```
<controller type='usb' index='0' model='ich9-ehci1'>
    <address type='pci' domain='0x0000' bus='0x00' slot='0x05' function='0x7'/>
</controller>
<controller type='usb' index='0' model='ich9-uhci1'>
    <master startport='0'/>
    <address type='pci' domain='0x0000' bus='0x00' slot='0x05' function='0x0' multifunction='on'/>
</controller>
<controller type='usb' index='0' model='ich9-uhci2'>
    <master startport='2'/>
    <address type='pci' domain='0x0000' bus='0x00' slot='0x05' function='0x1'/>
</controller>
<controller type='usb' index='0' model='ich9-uhci3'>
    <master startport='4'/>
    <address type='pci' domain='0x0000' bus='0x00' slot='0x05' function='0x2'/>
</controller>
<controller type='pci' index='0' model='pci-root'/>
<controller type='ide' index='0'>
    <address type='pci' domain='0x0000' bus='0x00' slot='0x01' function='0x1'/>
</controller>
<controller type='virtio-serial' index='0'>
    <address type='pci' domain='0x0000' bus='0x00' slot='0x06' function='0x0'/>
</controller>
```

- Tùy thuộc vào cấu trúc của máy ảo mà nó có thể có các thiết bị ảo đi kèm, mỗi cái lại đi theo một bộ điều khiển. Thường thì libvirt sẽ tự động chỉ ra mà không cần khai báo qua file xml.

- Mỗi bộ điều khiển có một tham số bắt buộc là `type` và `index`, các giá trị có thể chọn của type là: 'ide', 'fdc', 'scsi', 'sata', 'usb', 'ccid', 'virtio-serial' hoặc 'pci'. Trong khi đó `index` sẽ chỉ ra thứ tự ưu tiên.

### Network interfaces

Có một vài kiểu set up network ví dụ như Virtual network (type = network), Bridge to LAN (type = bridge), Userspace SLIRP stack (type=user). Ở đây tôi sẽ nói về Bridge to LAN.

```
<interface type='bridge'>
    <mac address='52:54:00:34:b7:c2'/>
    <source bridge='br0'/>
    <model type='rtl8139'/>
    <address type='pci' domain='0x0000' bus='0x00' slot='0x03' function='0x0'/>
</interface>
```

- `source`: tham số bắt buộc là "bridge": tên bridge.

- `mac`: tham số bắt buộc là "address": địa chỉ mac.

- `model`: tham số bắt buộc là "type", các giá trị thường được sử dụng trong KVM: "ne2k_isa, i82551, i82557b, i82559er, ne2k_pci, pcnet, rtl8139, e1000, virtio".

### Input Devices

```
<input type='mouse' bus='ps2'/>
<input type='keyboard' bus='ps2'/>
```

Chỉ có 1 tham số bắt buộc đó là `type`, các giá trị có thể chọn là 'mouse', 'tablet', 'keyboard' hoặc 'passthrough'. Tham số `bus` để xác định chính xác thiết bị, các giá trị có thể chọn là "xen" (paravirtualized), "ps2", "usb" và "virtio".

### Graphical framebuffers

```
<graphics type='spice' autoport='yes'>
    <listen type='address'/>
    <image compression='off'/>
</graphics>
```

- `graphic` : Thuộc tính bắc buộc là type, các giá trị có thể chọn : "sdl", "vnc", "spice", "rdp" và "desktop". Đối với mỗi loại sẽ có thêm những tham số được thêm vào.

### Video devices


```
<video>
    <model type='qxl' ram='65536' vram='65536' vgamem='16384' heads='1' primary='yes'/>
    <address type='pci' domain='0x0000' bus='0x00' slot='0x02' function='0x0'/>
</video>
```

- `model`: Tham số bắt buộc là "type", các giá trị có thể lựa chọn là "vga", "cirrus", "vmvga", "xen", "vbox", "qxl", "virtio" và "gop", tùy thuộc vào hypervisor.

    - `heads`: số lượng màn hình.

    - `ram` và `vram` chỉ ra kích thước của primary và secondary bar.

### Guest interface

- Serial Port:

```
<serial type='pty'>
    <target type='isa-serial' port='0'>
    <model name='isa-serial'/>
    </target>
</serial>
```

- Console: Nếu không có target type được chọn , mặc định trong KVM sẽ sử dụng serial.

### Sound devices

- `sound`: tham số bắt buộc là model, các giá trị có thể chọn : 'es1370', 'sb16', 'ac97', 'ich6' và 'usb'

```
<sound model='ich6'>
    <address type='pci' domain='0x0000' bus='0x00' slot='0x04' function='0x0'/>
</sound>
```

### Memory balloon device

```
<memballoon model='virtio'>
    <address type='pci' domain='0x0000' bus='0x00' slot='0x07' function='0x0'/>
</memballoon>
```

Được thêm tự động, mặc định với KVM, model sẽ là `virtio`.

Trên đây chỉ là một số thành phần chính trong file domain xml của KVM, các bạn tham khảo thêm [tại đây](https://libvirt.org/formatdomain.html#elementsDevices)