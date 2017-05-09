+++
author = "Tan Linh"

date = 2017-04-05T01:29:00Z
lastmode = 2017-05-05T02:00:00Z
description = "Những đặc điểm như hình dáng khuôn mặt,mắt,mũi,miệng liệu đã đủ để máy tính nhận diện khuôn mặt? Làm thế nào để giúp máy tính thực hiện được việc đó..."
draft = false
slug = "nhan-dien-khuon-mat-voi-opencv"
tag = ["OpenCV","Face recognition","Python"]
title = "Nhận diện khuôn mặt với OpenCV"

+++

## Giới thiệu OpenCV
[OpenCV (Open source Computer Vision)](http://opencv.org/) là thư viện hàng đầu trong lĩnh vực Computer Vision (Thị giác máy tính???) được phát triển bởi Intel từ năm 1999. Hiện tại, OpenCV được phát hành miễn phí theo giấy phép BSD và do Itseez duy trì.

Bắt đầu từ phiên bản OpenCV 2.4, lớp `FaceRecognizer` cung cấp cho người dùng cách thức nhận diện khuôn mặt(Face recognition) vô cùng đơn giản. Ở phần tiếp theo của bài viết, mình sẽ hướng dẫn cách thức triển khai thuật toán Face recognition với `FaceRecognizer` trong OpenCV cũng như cung cấp cái nhìn tổng quan về giải thuật phía sau nó, bao gồm các giải thuật sau:

- Eigenfaces
- Fisherfaces
- Local Binary Patterns Histograms

## Tổng quan về nhận diện khuôn mặt

Với con người, việc nhận diện hay phân biệt khuôn mặt cực kỳ đơn giản. Một đứa trẻ từ 1-3 tuổi đã có khả năng phân biệt được các khuôn mặt khác nhau mà nó từng gặp. Với máy tính, mọi việc không đơn giản như vậy. Những đặc điểm như hình dáng khuôn mặt, mắt, mũi, miệng liệu đã đủ để có thể nhận diện khuôn mặt? David Hubel và Torsten Wiesel đã nghiên cứu về não bộ con người và đưa ra kết luận. Thay vì nhìn thế giới xung quanh như những mảnh ghép riêng biệt, phần vỏ não thị giác kết hợp các mảnh ghép đó thành một mô hình xác định để đưa ra kết quả chính xác. Với nhận diện khuôn mặt cũng vậy.

Face recognition mặt dựa trên các đặc điểm hình học của khuôn mặt là hướng tiếp cận trực quan nhất và được công nhận rộng rãi nhất. Nó được thực hiện như sau: 

- Đánh dấu vị trí của những đặc điểm như mắt, mũi, tai .v.v.
- Sử dụng thông số đó để xây dựng nên vector đặc trưng thể hiện khoảng cách, góc giữa các đặc điểm

Việc nhận diện khuôn mặt sẽ được thực hiện bằng cách tính toán các thông số của vector trên ảnh và so sánh với vector trước đó đã xây dựng được.

Hướng tiếp cận này gần như không bị ảnh hưởng bởi sự thay đổi ảnh sáng trong các bức ảnh. Tuy nhiên, việc xác định chính xác vị trí các điểm đánh dấu đòi hỏi những thuật toán vô cùng phức tạp. Một số công trình nghiên cứu mới chỉ ra rằng việc sử dụng các đặc điểm hình học là không đủ để nhận diện khuôn mặt.

*To be continued...*
