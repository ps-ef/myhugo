+++
author = "Tan Linh"

date = 2018-11-13T00:23:57+07:00
description = ""
draft = false
slug = "understanding-priority-on-linux-processes"
tag = ["niceness", "nice value", "devops", "linux"]
title = "Understanding Priority on Linux Processes"
+++

Một buổi sáng như bao ngày bình thường, mình thức dậy và tự hỏi: Hôm nay mình phải làm những việc gì nhỉ? Đối với một người dù không biết sắp xếp kế hoạch như mình thì những việc cơ bản sau sẽ được thực hiện:

* Vệ sinh cá nhân. Đánh răng, rửa mặt, tắm rửa
* Ăn sáng và đi làm
* Giải quyết từng task đang tồn đọng
* Nghe sếp chửi. Chán đíu muốn làm nữa và nghỉ việc.
* Đang bực người yêu léo nhéo và chia tay
* Về nhà ngồi viết những dòng này….

Đùa chút thôi, mình đã qua cái tuổi chia tay người yêu rồi :troll:. 

Mình chỉ định nói một điều đó là: Với một người bình thường, hàng ngày bạn phải handle hàng tá các công việc khác nhau cùng một lúc. Bạn đã chọn làm việc này hay việc kia trước như nào? Rất may mắn là nhờ não bạn biết việc A phải được ưu tiên hơn việc B. Hoặc sếp bảo bạn cứ làm đi mặc dù bạn thấy nó chẳng quan trọng méo gì cả :troll:

## 1. Priority là cái gì?

Là cách các bạn ưu tiên việc A hay việc B hơn đó. Với con người là vậy còn Linux thì sao? Nó cũng same vậy thôi. 

Bạn sẽ nói với nó rằng: 

> Mày phải ưu tiên chạy process A của tao nhé, còn process B của thằng C thì túc tắc cũng được.

Cái guidelines đó được gọi là `niceness` hoặc `nice value` trong Linux. Với những bạn chưa biết thì `nice` là một program có trên Unix hoặc họ Unix như Linux. Nó cho phép thực thi một command hoặc một shell script với độ ưu tiên xác định. Điều đó có nghĩa rằng CPU sẽ hiểu process này được ưu tiên nhiều resources hơn process khác.

Với khoảng giá trị của `nice value` từ -20 đến 19. Giá trị càng thấp, độ ưu tiên càng cao. Nếu một process có `nice value` lớn hơn 19, CPU sẽ thực thi nó lúc nào **có thể** hay nói một cách văn học là `Lúc nào rảnh tao sẽ chạy process của mày, OK?`

> Lưu ý: Giá trị mặc định của `niceness` hay `nice value` là 0

Với `nice value`, chúng ta dễ dàng kiểm soát CPU sử dụng tài nguyên một cách hiệu quả nhất. Những process nào quan trọng, bạn có thể set `nice value` thấp để CPU hiểu rằng nó cần quan tâm cẩn thận những cháu này và ngược lại. 

Ngoài ra, nếu bạn có quan hệ thân thiết với anh DevOps, bạn có thể nhờ anh ý set `nice value` cho user bạn thấp hơn default để các processes  được ngốn resources nhiều hơn. Điều này hoàn toàn hợp lệ và được công nhận trong hiến pháp luôn đó :troll:

## 2. Tôi kiểm tra process của tôi được ưu tiên như nào?

Cách đơn giản nhất là sử dụng lệnh `top`

![Use top command](https://i.imgur.com/iEsk2oK.png)

Một cách khác là sử dụng lệnh `ps`. Trong đó `NI` chính là `nice value`

`ps -o pid,comm,nice -p 246`

Kết quả sẽ như hình:

![Use ps command](https://i.imgur.com/ttxMnqo.png)

## 3. Tôi muốn lower `nice value` khi chạy process mới thì làm sao?
Để CPU ưu ái process của bạn hơn, gõ ngay lệnh sau vào terminal

`nice -n -20 wget https://p**nhub.com/download/xxxxxxxxxxx.mp4`

> Lưu ý: Không nên thử trên production. Chỉ những chuyên gia có kinh nghiệm (bị đuổi việc) như mình mới nên làm điều đó.

Một điều ghi nhớ đó là: Giá trị của `nice value` là số dương sẽ làm độ ưu tiên của process giảm đi so với mặc định đấy.

## 4. Process đang chạy nhưng tôi muốn `nice value` của nó thấp hơn nữa thì sao?
Rõ ràng có những lúc thấy process có vẻ chậm chạp, thay vì phải chờ đến lúc CPU rảnh rang hơn chút, bạn có thể nhờ anh CPU quan tâm mình hơn bằng một cái phong bì với cú pháp sau:

`renice [nice value] -p [process ID]`

Ví dụ:

`renice -5 -p 4051`

![Có quan hệ tốt với anh DevOps đó](https://i.imgur.com/eqpE5Ly.png)

Điều này làm process của bạn được ưu tiên hơn những thằng default value bằng 0 khác :troll:

> Lưu ý quan trọng: Chỉ có root hoặc sudoers mới có quyền set giá trị `nice value` là số âm. Bạn đã hiểu vì sao phải làm thân anh DevOps rồi chứ :omg:

## 5. Tôi đã làm thân anh DevOps rồi, làm sao để process của tôi được ưu tiên?
Nếu đã làm thân với anh DevOps, mọi chuyện dần dễ dàng hơn rồi đấy. Bạn nhờ anh ta sửa file `/etc/security/limits.conf` là được. Syntax như sau:

`[username] [hard|soft] priority [nice value]`

Ví dụ:

`tanlinhnd hard priority -10`

Thực ra file `/etc/security/limits.conf` không chỉ đơn thuần limit priority của từng user, mình paste luôn phần `man` để các bạn tham khảo.

```
# /etc/security/limits.conf
#
#Each line describes a limit for a user in the form:
#
#<domain>        <type>  <item>  <value>
#
#Where:
#<domain> can be:
#        - a user name
#        - a group name, with @group syntax
#        - the wildcard *, for default entry
#        - the wildcard %, can be also used with %group syntax,
#                 for maxlogin limit
#        - NOTE: group and wildcard limits are not applied to root.
#          To apply a limit to the root user, <domain> must be
#          the literal username root.
#
#<type> can have the two values:
#        - "soft" for enforcing the soft limits
#        - "hard" for enforcing hard limits
#
#<item> can be one of the following:
#        - core - limits the core file size (KB)
#        - data - max data size (KB)
#        - fsize - maximum filesize (KB)
#        - memlock - max locked-in-memory address space (KB)
#        - nofile - max number of open files
#        - rss - max resident set size (KB)
#        - stack - max stack size (KB)
#        - cpu - max CPU time (MIN)
#        - nproc - max number of processes
#        - as - address space limit (KB)
#        - maxlogins - max number of logins for this user
#        - maxsyslogins - max number of logins on the system
#        - priority - the priority to run user process with
#        - locks - max number of file locks the user can hold
#        - sigpending - max number of pending signals
#        - msgqueue - max memory used by POSIX message queues (bytes)
#        - nice - max nice priority allowed to raise to values: [-20, 19]
#        - rtprio - max realtime priority
#        - chroot - change root to directory (Debian-specific)
#
#<domain>      <type>  <item>         <value>
#

#*               soft    core            0
#root            hard    core            100000
#*               hard    rss             10000
#@student        hard    nproc           20
#@faculty        soft    nproc           20
#@faculty        hard    nproc           50
#ftp             hard    nproc           0
#ftp             -       chroot          /ftp
#@student        -       maxlogins       4

# End of file
```

Cuối cùng, bài viết chứa rất nhiều ví dụ được thực hiện bởi chuyên gia. Vui lòng không thử trên production để tránh bị đuổi việc :troll:.

Cheers,

## 6. References
[1] Wikipedia [nice (Unix)](https://en.wikipedia.org/wiki/Nice_(Unix))

[2] nixtutor.com [Changing Priority on Linux Processes](https://www.nixtutor.com/linux/changing-priority-on-linux-processes/)