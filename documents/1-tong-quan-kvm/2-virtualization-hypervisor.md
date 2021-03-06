# Tổng quan về Virtualization và Hypervisor

## Mục lục

[1. Virtualization](#virtualization)

[2. Hypervisor](#hypervisor)

- [2.1. Native](#native)

- [2.2. Hosted](#hosted)

- [2.3. Ring](#ring)

[3. Phân loại Virtualization](#virtualization-types)

[Nguồn tham khảo](#tham-khao)

### <a name ="virtualization"> </a> 1. Virtualization

`Virtualization`, hay còn gọi là ảo hóa, là một công nghệ được thiết kế để tạo ra tầng trung gian giữa hệ thống phần cứng máy tính và phần mềm chạy trên nó. Ý tưởng của công nghệ ảo hóa máy chủ là từ một máy vật lý đơn lẻ có thể tạo thành nhiều máy ảo độc lập. Mỗi một máy ảo đều có một thiết lập nguồn hệ thống riêng rẽ, hệ điều hành riêng và các ứng dụng riêng. Ảo hóa có nguồn gốc từ việc phân chia ổ đĩa, chúng phân chia một máy chủ thực thành nhiều máy chủ logic. Một khi máy chủ thực được chia, mỗi máy chủ logic có thể chạy một hệ điều hành và các ứng dụng độc lập.

<img src="https://imgur.com/ftzDN9L.png">

- **Ưu điểm:**

    - Tiết kiệm chi phí và tối ưu hóa hạ tầng CNTT.

    - Độc lập giữa các hệ điều hành khác nhau.

    - Nhiều máy được tạo ra với các hệ điều hành khác nhau tồn tại trên cùng một máy chủ vật lý.

- **Virtual Machine (VM) là gì ?**: `Virtual Machine` hay còn gọi là máy ảo, là một môi trường hoạt động độc lập – phần mềm hoạt động cùng nhưng độc lập với hệ điều hành máy chủ.

### <a name ="hypervisor"> </a> 2. Hypervisor

`Hypervisor` hay còn gọi là phần mềm giám sát máy ảo: Là một chương trình phần mềm quản lý một hoặc nhiều máy ảo (VM). Nó được sử dụng để tạo, startup, dừng và reset lại các máy ảo. Các hypervisor cho phép mỗi VM hoặc “guest” truy cập vào lớp tài nguyên phần cứng vật lý bên dưới, chẳng hạn như CPU, RAM và lưu trữ. Nó cũng có thể giới hạn số lượng tài nguyên hệ thống mà mỗi máy ảo có thể sử dụng để đảm bảo cho nhiều máy ảo cùng sử dụng đồng thời trên một hệ thống.

***--> Hypervisor là các phần mềm công nghệ để tạo máy ảo và giám sát, điều khiển máy ảo***

Có 2 loại hypervisor là `Native` ( hay còn gọi là `Bare metal` ) và `Host Based`

#### <a name ="native"> </a> 2.1. Native

Một `Hypervisor` ở dạng `native` (hay còn gọi `bare-metal`) chạy trực tiếp trên phần cứng. Nó nằm giữa phần cứng và một hoặc nhiều hệ điều hành khách (Guest OS). Nó được khởi động trước cả hệ điều hành và tương tác trực tiếp với kernel. Điều này mang lại hiệu suất cao nhất có thể vì không có hệ điều hành chính nào cạnh tranh tài nguyên máy tính với nó. Tuy nhiên, nó cũng đồng nghĩa với việc hệ thống chỉ có thể được sử dụng để chạy các máy ảo vì hypervisor luôn phải chạy ngầm bên dưới.

Các hypervisor dạng native này có thể kể đến như VMware ESXi, Microsoft Hyper-V và Apple Boot Camp.

<img src="https://imgur.com/p04zlVA.png">

#### <a name ="hosted"> </a> 2.2. Hosted

Một `Hypervisor` dạng `hosted` được cài đặt trên một máy tính chủ (host computer), mà trong đó có một hệ điều hành đã được cài đặt. Nó chạy như một ứng dụng cũng như các phần mềm khác trên máy tính. Hầu hết các hypervisor dạng hosted có thể quản lý và chạy nhiều máy ảo cùng một lúc. Lợi thế của một hypervisor dạng hosted là nó có thể được bật lên hoặc thoát ra khi cần thiết, giải phóng tài nguyên cho máy chủ. Tuy nhiên, vì chạy bên trên một hệ điều hành, nó có thể đem lại hiệu suất tương tự như một hypervisor ở dạng native.

Ví dụ về các hypervisor dạng hosted bao gồm VMware Workstation, Oracle VirtualBox và Parallels Desktop for Mac.

#### <a name ="ring"> </a> 2.3. Ring

Một Protection Ring là một mức độ (mode/level/layer) truy cập tài nguyên hệ thống. Số lượng Ring tùy thuộc vào kiến trúc CPU và hệ điều hành chạy trên kiến trúc đó có khả năng hỗ trợ bao nhiêu Ring.

Các Ring được sắp xếp có thứ bậc, từ mức có nhiều đặc quyền nhất (dành cho trusted-software, thường được đánh số 0) đến mức có ít đặc quyền nhất (dành cho untrusted-software, được đánh số cao nhất).

<img src="https://imgur.com/hjC4tsZ.png">

Các chương trình hoạt động tại Ring 0 có đặc quyền cao nhất, có thể tương tác trực tiếp với phần cứng như CPU, Memory…

Để cho phép các ứng dụng nằm ở Ring có trọng số cao truy cập các tài nguyên được quản lý bởi các chương trình nằm ở Ring có trọng số thấp hơn, người ta xây dựng các cổng (gate) đặc biệt. Ví dụ như system call (lời gọi hàm hệ thống) giữa các Ring.

Để cho phép các ứng dụng nằm ở Ring có trọng số cao truy cập các tài nguyên được quản lý bởi các chương trình nằm ở Ring có trọng số thấp hơn, người ta xây dựng các cổng (gate) đặc biệt. Ví dụ như system call (lời gọi hàm hệ thống) giữa các Ring.

Việc quy định chặt chẽ chương trình nào nằm tại Ring nào cộng với việc xây dựng các cổng phù hợp giữa các Ring sẽ đảm bảo tính ổn định của hệ thống, đồng thời ngăn chặn các chương trình nằm trong Ring cao sử dụng trái phép (do vô tình hoặc cố ý) các tài nguyên dành cho các chương trình khác nằm tại Ring thấp hơn.

Ví dụ, một spyware đang chạy với tư cách là ứng dụng cho người dùng thông thường (thuộc untrusted software) nằm tại Ring 3 có ý định bật webcam mà không được sự đồng ý của người dùng. Hành vi này sẽ bị hệ thống ngăn chặn vì muốn truy cập tới phần cứng là thiết bị webcam nó phải sử dụng một hàm trong phần mềm điều khiển thiết bị (device driver) của webcam (thuộc trusted software) nằm tại Ring 1.

Hầu hết các hệ điều hành chỉ sử dụng 2 Ring ngay cả khi phần cứng mà hệ điều hành chạy trên đó hỗ trợ nhiều hơn 2 Ring. Ví dụ, Windows chỉ sử dụng 2 mức là Ring 0 (tương ứng với Kernel Mode) và Ring 3 (tương ứng với User Mode).

### <a name ="virtualization-types"> </a> 3. Phân loại Virtualization

- Một số kiểu Virtualization:

    - RAM virtualization

    - CPU virtualization

    - Network virtualization

    - Device I/O virtualization

#### <a name ="cpu-virtualization"> </a> 3.1. CPU virtualization

- **Full Virtualization:**

<img src="https://imgur.com/bwGUmLs.png">

Trong giải pháp này, các non-virtualizable instruction từ guest OS được translate qua binary translation ở virtualization layer và cache lại kết quả dùng cho các lần sau. Còn user level application thì thực hiện direct execution xuyên qua virtualization layer. Bằng cách này, trở ngại các chỉ thị guest OS không hoạt động ở ring khác 0 bị vượt qua còn các user level application vẫn họat động ở native speed (tốc độ đáp ứng yêu cầu giống như khi không có ảo hóa). Guest OS hoàn toàn không nhận ra nó đang nằm trên một lớp ảo hóa vì các low-level request không có gì thay đổi. Do đó guestOS hoàn toàn không phải chỉnh sửa gì.

<img src="https://imgur.com/xJMBEC5.png">

**Hiểu một cách đơn giản:** Guest OS sẽ không bị sửa đổi hệ điều hành để tương thích với phần cứng, mà sẽ dịch nhị phân các yêu cầu, rồi đưa cho VMM, xong VMM làm trung gian đưa cho Hardware xử lý.

Nhìn vào `ring = 1` của Guest OS, thì Guest OS này chỉ chạy trên quyền `user lever`, chứ không chạy trên quyền `privilege`, không trực tiếp chạy trên hardware. Nhưng vì code của OS không bị sửa đổi, nên Guest OS không biết điều đó, và sẽ làm việc bình thường như trên máy thật vậy, nhưng thực chất là đang làm việc với VMM.

<img src="https://imgur.com/f54jN2j.png">

- **Paravirtualization:**

<img src="https://imgur.com/XQDNZgK.png">

Trong paravirtualization, hypervisor sẽ cung cấp hypercall interface. Guest OS sẽ được chỉnh sửa kernel code để thay thế `non-virtualizable instruction` bằng các hypercall này. Do kernel code của guest OS phải chỉnh sửa nên giải pháp này không thể sử dụng được một số hệ điều hành mã nguồn đóng như **Windows**. Thêm nữa, do guest OS sử dụng hypercall nên nó sẽ biết được nó đang nằm trên một virtualization layer.

<img src="https://imgur.com/0PLO2UK.png">

**Hiểu một cách đơn giản:** Guest OS bây giờ đã bị sửa đổi 1 tí, để có thể nằm ở `ring 0`. Guest OS đã hiểu vị trí hiện tại chỉ là khách, nhưng có thể nhìn trực tiếp tài nguyên của máy thật, quyền truy cập vào hardware vì hiện tại đang ở `ring 0`.

Nhưng đối với các App, vẫn thấy Guest OS này không có gì thay đổi, vì App cần interface gì thì Guest OS vẫn cung cấp cho interface ý, vẫn là API đó.

<img src="https://imgur.com/4FJQwr8.png">

### <a name ="tham-khao"> </a> Nguồn tham khảo

https://news.cloud365.vn/kvm-tong-quan-ve-virtualization-va-hypervisor/