+++
author = "Tan Linh"

date = 2017-12-06T23:26:00+07:00
description = "Bài viết mở đầu cho chuỗi ngày try easy Algorithm của mình. Problem hôm nay cực kỳ đơn giản, đó là Non-descreasing array"
draft = false
slug = "algorithm-001-non-descreasing-array"
tag = ["algorithm", "golang"]
title = "Algorithm 001: Non-descreasing Array"

+++

Xin chào xin chào,

Sau một thời gian dài ngụp lặn, tới hôm nay mình mới nổi. Thời gian vừa rồi khá bận vì nhà mình có thêm thành viên mới vô cùng lém lỉnh. Công việc thời gian qua không được happy cho lắm nên gần như mình chỉ chém gió trên `#random` là chính. Thực tình rất phí phạm thời gian.

Mấy hôm nay, anh em trong hội Ruby Việt Nam đang try hard Algorithm, rất nhiều siêu xayda đã lộ mặt. Mặc dù cũng nằm trong channel nhưng tự thấy bản thân mình còn ngu muội quá nên lẳng lặng ôn lại kiến thức vỡ lòng về `Data Structure` trước. Goal đến hết tuần sau là `Array`, `List`, `Queue`, `Heap` và `Tree`. Mình bắt đầu bằng level **Easy** trên [https://leetcode](https://leetcode.com/)

Bài viết này là ghi chép về problem đầu tiên với array: `Non-descreasing Array`. Dù là level easy nhưng mình cũng phải mất 4 lần `Wrong Answer` và 1 lần `Runtime Error` mới pass được. Đầu óc chán chường kinh khủng. Vì thế, mình càng quyết tâm phải dùi mài kinh sử và uống `Hoạt huyết dưỡng não` mỗi ngày :omg:

## 1. Problem

Problem đọc qua có vẻ cực kỳ đơn giản, các bạn xem ở đây [Non-decreasing Array - LeetCode](https://leetcode.com/problems/non-decreasing-array/)

Cho một array có `n` số nguyên, nhiệm vụ của bạn là kiểm tra array này có khả năng trở thành `non-descreasing` array hay không với việc chỉ modify nhiều nhất 1 phần tử. Một array được gọi là `non-descreasing`  nếu `a[i] <= a[i+1]` với mọi `i (1 <= i < n)` . Chỗ này mình vẫn thắc mắc là `0 <= i < n` chứ nhỉ?

#### Example 1
```golang
Input: [4,2,3]
Output: true
Explanation: Bạn có thể modify phần tử đầu tiên, từ 4 thành 1 để có được non-desceasing array.
```

#### Example 2
```golang
Input: [4,2,1]
Output: false
Explanation: Bạn không thể có non-desceasing array với duy nhất 1 lần modify.
```


## 2. Solution

Về solution, mình sẽ dùng 1 vòng `for` để duyệt qua tất cả phần tử và biến array này thành `non-descreasing` thỏa mãn `a[i] <= a[i+1]`, đồng thời tính số lần thực hiện modify. Nếu `modified <= 1` đương nhiên array này có khả năng trở thành `non-descreasing` array. Đơn giản vậy thôi. Các bạn tham khảo bên dưới.


```golang
func checkPossibility(nums []int) bool {
	var modified int
	for i := 1; i < len(nums) && modified <= 1; i++ {
		if nums[i-1] > nums[i] {
			modified++
			if i - 2 < 0 || nums[i-2] <= nums[i] {
				nums[i-1] = nums[i]
			} else {
				nums[i] = nums[i-1]
			}
		}
	}
	return modified <= 1
}
```

Ở đây có một điểm cần chú ý là việc gán `nums[i-1] = nums[i]` hay `nums[i] = num[i]`. Vậy thôi, có mỗi cái này làm mình sai tận 4 lần. Kém cỏi vãi :sad: Với solution này, time complexity là `O(N)` và space complexity là `O(1)`. Có một solution khác là `Reduce to Smaller Problem`, các bạn theo dõi thêm ở đây [Non-decreasing Array Solution](https://leetcode.com/problems/non-decreasing-array/solution/)

Bài viết chỉ mang tính take note, mở đầu cho chuỗi ngày `Try Easy Algorithm` sắp tới :omg:. Rule của channel `#algorithm` là 3 problem mỗi tuần nên mình sẽ cố gắng giải quyết và viết blog một cách chăm chỉ.

P/s: Nếu bạn nào có nhã hứng, xin mời join channel `#random` của Ruby Việt Nam và ới ông bạn [@snacky](https://thefullsnack.com/) để tham gia nhé. Lệ phí 10$ cho him :troll:

## Reference

[1] *Non-decreasing Array Problem* [https://leetcode.com/problems/non-decreasing-array/](https://leetcode.com/problems/non-decreasing-array/)

[2] *Slack Ruby VietNam* [http://chat.ruby.org.vn/](http://chat.ruby.org.vn/)
