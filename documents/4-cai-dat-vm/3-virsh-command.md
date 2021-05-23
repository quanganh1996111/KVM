# Tìm hiểu một số câu lệnh Virsh để quản lý KVM

## A. Một số câu lệnh thao tác với VM

- Khởi động VM:

    - Start:

```
virsh start <ten_vm>
```

    - Start và mở Console:

```
virsh start --console
```

- Tắt VM:

```
virsh shutdown <tên_vm>
```

```
virsh destroy <tên_vm>
```

- Tự động bật máy áo khi khởi động KVM:

```
- Enable:

virsh autostart <tên_vm>

- Disable:

virsh autostart --disabel <tên_vm>

```

- List tất cả VM đang có:

```
virsh list -all
```

- Chuyển chế độ console GuestOS <-> HostOS:

Hệ điều hành chủ (host operating system): là hệ điều hành chạy trên máy chủ.

Hệ điều hành khách (guest operating system): là hệ điều hành chạy trên một VM.

- Dump file xml của VM:

```
virsh dumpxml <tên_vm>
```

- Reboot VM:

```
virsh reboot <tên_vm>

virsh reset <tên_vm>
```

- Hiển thị Thông tin về CPU:

```
virsh vcpinfo <tên_vm>
```

- Suspend VM:

```
virsh suspend <tên_vm>
```

- Cho phép máy ảo hoạt động sau khi suspend:

```
virsh resume <tên_vm>
```

- Hiển thị thông tin IO Disk:

```
virsh blkiotune <tên_vm>
```

- Hiển thi CPU Statistic:

```
virsh cpu-stats <tên_vm>
```

## B. Lệnh giám sát một số thông tin của VM

 Thông tin chung của máy ảo:

```
virsh dominfo <tên_vm>
```

- Thông tin về disk máy ảo:

```
virsh domblklist <tên_vm>
```

- Thông tin I/O disk:

```
virsh domblkstat  <tên_vm>
```

- Thông tin về interface:

```
virsh domcontrol <tên_vm>

virsh domif-getlink <tên_vm>
```

- List all interface máy ảo:

```
virsh domiflist <tên_vm>
```

- Hiển thị thông tin về RAM máy ảo:

```
virsh dommemstat <tên_vm>
```

- Hiển thị thông tin trạng thái máy ảo:

```
virsh domstate <tên_vm>
```