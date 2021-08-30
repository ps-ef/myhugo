+++
author = "Linh Nguyen"

date = 2021-08-17T23:53:22+07:00
description = ""
draft = false
slug = "design-of-the-unix-operating-system-general-overview-of-file-system"
tag = ["linux", "unix", "os"]
title = "Design of the Unix Operating System: General Overview of Filesystem"
+++

Hi, lâu ngày quá. Thực sự thì lười lắm lắm, gần 3 năm rồi mình mới mở lại cái blog sau 2 lần đổi domain :laughing:

Gần đây, trong một lần lang thang thì bắt gặp comment của anh huydx về cuốn "Design of The Unix Operating System". Tiện lúc đang tìm job mới nên mình quyết tâm dành thời gian đọc và nghiên cứu sâu hơn về OS cũng như Kernel. Tèn ten, do vậy chúng ta có post này đây. Đi thôi nào.

> Có nhiều phần mình viết lại không thoát nghĩa nên sẽ ghi chú thêm phần nguyên văn nhé hoặc dùng luôn tiếng anh nhé.

## 1. Why UNIX became so popular?

Từ những năm 1984, đã có khoảng 100,000 Unix system được sử dụng trên thế giới, chạy trên nhiều nền tảng khác nhau từ microprocessors đến các máy mainframes. Chưa từng có một OS nào khác có thể làm điều tương tự. Và để trả lời cho câu hỏi trên, chúng ta có vài ý như sau:

* Được viết bởi high-level language, do đó dễ dàng đọc hiểu cũng như thay đổi, hoạt động trên nhiều nền tảng khác nhau. Denis Ritchie [1] tính toán rằng first system viết bằng C thì chậm hơn và lớn hơn từ 20-40% so với viết bằng Assembly. Đổi lại, những ưu điểm khi viết bằng high-level language là vượt trội nếu so với những khuyết điểm còn lại.
* Unix cung cấp 1 interface đơn giản nhưng cực kỳ mạnh mẽ. Những chương trình phức tạp có thể build từ những chương trình đơn giản hơn (Nguyên văn: It provides primitives that permit complex programs to be built from simpler
programs)
* Unix sử dụng một file system dạng phân cấp, dễ dàng để maintain cũng như implement
* Sử dụng byte stream cho định dạng file, các chương trình sẽ dễ dàng để write hơn.
* Cung cấp một interface đơn giản và nhất quán cho các thiết bị ngoại vi
* Multi user, multi process system, mỗi user có thể  nhiều tác vụ đồng thời
* Ẩn đi các kiến trúc của phần cứng với người dùng, giúp cho họ dễ dàng viết những chương trình hoạt động trên nhiều phần cứng khác nhau.

## 2. Architecture of the UNIX Operating System

![2 concepts in the Unix system model](/design-of-the-unix-os/2-concepts-of-unix.png)

File và process là 2 central concepts của Unix system model. Ở đây, có 3 level mà chúng ta dễ dàng nhận thấy đó là: user, kernel và hardware.

Với kiến trúc của Unix, Kernel tương tác trực tiếp với phần cứng, cung cấp các interface cho chương trình cũng như cô lập chúng khỏi phần cứng. Ví dụ những chương trình như sh, vi hay wc ở layer ngoài sẽ tương tác với Kernel thông qua các API gọi là system call.

Syscall cho phép các chương trình bên ngoài yêu cầu Kernel thực thi các tác vụ cụ thể và cung cấp cơ chế trao đổi dữ liệu giữa chương trình và Kernel. 

## 3. An Overview of the Filesystem

Trên Unix, file system đặc trưng bởi các tính chất sau:

* Cấu trúc phân cấp
* Xử lý nhất quán dữ liệu trong file
* Khả năng tạo cũng như xóa files
* Khả năng mở rộng file
* Khả năng bảo vệ dữ liệu trong file
* Khả năng xử lý các thiết bị ngoại vi dưới dạng file

![Sample file system tree](/design-of-the-unix-os/sample-file-system-tree.png)

Chắc rằng mọi người ai cũng biết, file system được tổ chức như một cái cây với gốc của nó (root) được viết là `/`. Các node non-leaf sẽ là các thư mục (directory) và các files sẽ là lá. Đường dẫn của file (path name) sẽ thể hiện việc file được lưu trữ thế nào trong hệ thống phân cấp của file system. Ví dụ một path name sẽ có dạng như sau: `/usr/src/cmd/who.c`. 

Inode (index node) là một data-structure trong Unix file system chứa thông tin về disk layout của dữ liệu trong file và các thông tin khác như owner, access permission, access times. Mỗi một file hoặc directory đại diện bởi 1 inode. Tuy nhiên, có trường hợp file có nhiều hơn 1 path name, và tất cả chúng map tới một inode, trường hợp này còn gọi là hard-link. 

Khi một process create một file mới, Kernel sẽ assign nó với một unused inode. Một lỗi mà có thể các bạn đã từng gặp đó là kiểm tra dung lượng partition bằng `df -h` vẫn còn trống nhưng không tạo thêm được file mới do không còn inode trống.

Kernel chứa 2 loại data-structure là `file table` và `user file descriptor table`. Với `file table` là global kernel structure, còn `file descriptor` được allocate cho mỗi process. 

Mỗi khi process open hoặc create một file, Kernel sẽ cấp phát 1 entry trên `file table` và `descriptor table` tương ứng với inode table. Entry trên 3 table này cung cấp trạng thái của file và user nào access nó. `file table` sẽ keep track byte offset của file, việc read và write của user sẽ bắt đầu từ offset nào. `desriptor table` định danh tất cả các file đang mở của một process.

<!-- ![3 table](/design-of-the-unix-os/3-table-file-data-structure.png) -->

![Relationship between 3 tables](/design-of-the-unix-os/relationship-between-3-tables.png)

Kernel khi nhận được system call để open or create 1 một, sẽ return lại một `file descriptor table`, cụ thể nó là 1 index trong user file descriptor table. Với mỗi process, `file descriptor table` sẽ có 1 entry cho mỗi FD và 1 pointer tới `file table`. Struct của fdtable được define trong `include/linux/fdtable.h`. Các bạn có thể tham khảo tại đây [https://elixir.bootlin.com/linux/latest/source/include/linux/fdtable.h](https://elixir.bootlin.com/linux/latest/source/include/linux/fdtable.h)

Về mặt logic, Kernel deal với file system thay vì disk. Unix chia physical disk thành các logical disk gọi là partition. Với mỗi partition là một file system riêng biệt, từ đây về sau, file system sẽ refer tới một single partition. Mỗi disk sẽ được đại diện bởi 1 major device number, và mỗi partition sẽ gắn với 1 minor device number.

File system chứa một số lượng logical blocks tuần tự, có thể chứa 512, 1024, 2048 hoặc một bội số của 512 bytes. Size của local block là đồng nhất trong file system, có thể khác tùy vào cấu hình của từng file system.

![file system structure](/design-of-the-unix-os/file-system-structure.png)


Mỗi file system sẽ có cấu trúc như trên, trong đó:

* Một `boot block` nằm trong 1 vài sector đầu tiên của file system. Nó chứa boootstrap code dùng để boot hoặc init OS. Chỉ cần 1 `boot block` để boot OS, và thường các file system khác sẽ có `boot block` rỗng.
* Một `super block` mô tả trạng thái của file system, ví dụ như: total size của partition, block size, pointer tới các free block, inode number của root directory, magic number .v.v.
* Tiếp theo của `super block` là một mảng các inode (`inode list`). Một inode sẽ là root inode của file system và là cấu trúc thư mục có thể truy cập được sau khi thực hiện system call `mount`. User sẽ làm việc với file qua file name, còn Kernel sẽ qua inode.
* Cuối cùng là `data block` chứa dữ liệu của file.

Ở trên, chúng ta có nói qua về một số thông tin mà inode lưu trữ. Cụ thể và chi tiết nhất, inode lưu trữ các thông tin sau:

* Chủ sở hữu của file
* Định dạng file (ví dụ như: regular, directory, special device, pipe .v.v)
* Quyền truy cập file 
* Thời gian access và chỉnh sửa cuối cùng
* Số lượng link trỏ đến file
* Pointers tới data block của file
* Dung lượng file bằng bytes (với regular file), major và minor device number cho các device đặc biệt
* path name của file

## 4. Duplicated file descriptors (between processes)

Hẳn các bạn đã biết về Nginx, một HTTP server cũng như một reverse proxy. Process model của Nginx được thể hiện như sau:

![The Nginx Process model](/design-of-the-unix-os/infographic-Inside-NGINX_process-model.png)

Kiểm tra status của Nginx bằng `systemctl`, chúng ta thu được:

![Nginx status](/design-of-the-unix-os/nginx-status.png)

Nginx sử dụng một Master Process và 1 vài Child Processes để handle requests. Trong đó:

* Master process thực hiện quản lý các cấu hình, binding các port cần listen và create 1 số các child processes con. Có 3 loại process con là Cache Manager, Cache Loader và Worker. Tuy nhiên chúng ta sẽ quan tâm đến Worker process
* Worker processes thực hiện handle network connections, read & write content vào disk cũng như communicate với upstream servers.

Và bỗng dưng trong đầu bạn lóe lên 1 câu hỏi. "Ủa, Master process quản lý binding port, làm thế nào mà Worker lại handle được network connection vậy?". Câu trả lời là file.

Tại sao lại như vậy? Như đã nói ở trên, tất cả mọi thứ trong Unix đều là file, và socket cũng là file. Đúng không nhỉ :sunglasses:. Vấn đề còn lại chỉ là làm thế nào để Master process share socket với Worker process.

Quay lại model của Nginx, chúng ta nhận ra rằng Worker processes được sinh ra bởi Master process và cụ thể bằng hàm fork(2). Hàm fork(2) create một process con và copy file descriptor của process cha. Do vậy, file descriptor của process con refer tới cùng một open file description tương ứng trong file descriptor của process cha. Đồng nghĩa với việc 2 file descriptor share cho nhau open file status flags, file offset và cả signal-driven I/O attribures. Các bạn có thể xem diagram bên dưới.

![File descriptor table of child process](/design-of-the-unix-os/fork-child-process-table.png)

Từ đây, câu chuyện làm thế nào để Master process có thể share socket với Worker process đã sáng tỏ. Một ví dụ bằng python các bạn có thể xem qua. Source code tại [https://gist.github.com/wjzhangq/474997](https://gist.github.com/wjzhangq/474997)

Chạy đoạn code trên. Kết quả thu được như bên dưới. Clear đúng không :smile:

![Example of fork child process](/design-of-the-unix-os/fork-child-process-example.png)

Thank các bạn đã dành thời gian để đọc đến đây. Nếu có gì sai sót,rất mong được góp ý chân thành ở phần comment. 

P/s: Sẽ bổ sung phần comment sớm :stuck_out_tongue_closed_eyes:

## References

[1] Denis Ritchie [https://en.wikipedia.org/wiki/Dennis_Ritchie](https://en.wikipedia.org/wiki/Dennis_Ritchie)

[2] Design of Unix Operating System [https://bit.ly/2XPvvEE](https://bit.ly/2XPvvEE)

[3] Unix Filesystem Organiztion [https://web.cs.wpi.edu/~rek/DCS/D04/UnixFileSystems.html](https://web.cs.wpi.edu/~rek/DCS/D04/UnixFileSystems.html) 

[4] Relationship between fd and open files [https://man7.org/training/download/lusp_fileio_slides.pdf](https://man7.org/training/download/lusp_fileio_slides.pdf)

[5] Inside NGINX: How We Designed for Performance & Scale [https://www.nginx.com/blog/inside-nginx-how-we-designed-for-performance-scale/](https://www.nginx.com/blog/inside-nginx-how-we-designed-for-performance-scale/)

[6] Man fork(2) [https://man7.org/linux/man-pages/man2/fork.2.html](https://man7.org/linux/man-pages/man2/fork.2.html)