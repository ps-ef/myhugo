+++
author = "Tan Linh"
date = "2017-05-08T16:16:30+07:00"
description = "Dạo này công việc động chạm nhiều đến bash shell. Mình note lại một số điều mà mình sử dụng trong quá trình làm việc. "
draft = false
slug = "til-some-useful-bash-script"
tag = ["TIL","Linux", "Vim"]
title = "Some useful bash script"

+++

Dạo này công việc động chạm nhiều đến bash shell. Mình note lại một số điều mà mình sử dụng trong quá trình làm việc. 

## 1. Array

Array[1] là kiểu dữ liệu cơ bản trong tất cả các ngôn ngữ lập trình (NNLT). Bash shell cũng hỗ trợ array và bạn có thể sử dụng bằng cách khai báo như sau:

<script src="https://gist.github.com/tanlinhnd/59084712a6266bffad4115ea1cd4f98a.js"></script>

Giống như các NNLT khác, chỉ số của mảng bắt đầu từ 0 -> n. Để sử dụng mảng, các bạn theo dõi bên dưới như sau:

<script src="https://gist.github.com/tanlinhnd/67c329ddec924b42bfbdf0aed8f6e024.js"></script>

Ở đây, bạn có thể sử dụng `*` hoặc `@` để lấy toàn bộ phần tử của mảng.

Ngoài cách sử dụng chỉ số mảng tăng dần, bạn cũng có thể khai báo mảng dạng dictionary như sau:

<script src="https://gist.github.com/tanlinhnd/3edb1fbdebb90f6f40cdd48fd6ece5fb.js"></script>

Sau khi google thì mình sử dụng dictionary như sau, khai báo và sử dụng có tí trong sáng :D

<script src="https://gist.github.com/tanlinhnd/dd86c3e4b99c3e2d7b043776aeb6cb0b.js"></script>

## 2. Variable `$?`

Biến `$?` lưu trữ kết quả của command vừa được thực thi. Nếu command thực thi thành công, `$?` có giá trị là `0`, ngược lại `$?` có giá trị `1`. Mình sử dụng nó để kiểm tra việc move file sang phân vùng backup có thành công không, sau đó mới zip file nguồn. Ví dụ sau đối với trường hợp kiểm tra ping:

<script src="https://gist.github.com/tanlinhnd/7490d0e174f9b3c76e1ba0e7f98d3fd0.js"></script>

## 3. Append text to end of line

Hàng ngày, mình có một lượng lớn dữ liệu file CDR lưu trữ dạng CSV cần làm sạch. Sếp yêu cầu thêm một trường tên file CDR vào cuối các dòng của file để kiểm soát dữ liệu bla bla. Ok im fine!

Mình implement sử dụng vim như sau, các bạn có thể tham khảo:

<script src="https://gist.github.com/tanlinhnd/132757112e192e53ef47ea10ac0d0ebd.js"></script>

Không có gì cao siêu cả, mình dùng `replace` trong vim thôi. Hehe

tanlinhnd, 08-05-2017

## Reference
[1] The Linux Documentation Project *"Array variables"* [Bash Guide for Beginners](http://tldp.org/LDP/Bash-Beginners-Guide/html/sect_10_02.html)