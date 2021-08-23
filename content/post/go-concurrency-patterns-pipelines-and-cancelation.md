+++
author = "Linh Nguyen"

date = 2018-04-09T11:27:39+07:00
description = ""
draft = true
slug = "go-concurrency-patterns-pipelines-and-cancellation"
tag = ["golang","design-patterns"]
title = "Go Concurrency Patterns: Pipelines and Cancellation"
og_image = "https://i.imgur.com/cCKF1ay.jpg"
+++

Hi các bạn,

Concurrency là một mảng quan trọng và thú vị trong Go. Nhân tiện có việc liên quan đến Fan-in Fan-out, mình tìm đọc lại bài viết [Go Concurrency Patterns: Piplines and cancellation](https://blog.golang.org/pipelines) và cũng nhân tiện đó viết luôn bài này. Ra vẻ cao siêu thế thôi nhưng thực ra chỉ là bài dịch thôi. Hihi. Nào chúng ta bắt đầu.

# What is a pipeline?

Đầu tiên phải nói rằng, Pipeline không có một định nghĩa cụ thể nào trong Go, tuy nhiên chúng ta có thể hiểu một các đơn giản như sau:

* Pipeline là một chuỗi các stage, chúng liên kết với nhau qua 2 loại channel: inbound và outbound
* Mỗi stage thực chất là một nhóm các goroutines thực thi cùng một function
* Trong mỗi stage, những goroutines đó làm nhiệm:
    * Nhận data từ upstream qua inbound channels
    * Làm trò mèo gì đó với đống data vừa nhận được
    * Gửi giá trị mới tới downstream qua outbound channels

Ngoại trừ stage đầu tiên và stage cuối cùng chỉ có duy nhất inbound hoặc outbound, những stage còn lại có thể có 1 hay nhiều inbound/outbound channels. Thi thoảng, stage đầu tiên sẽ được gọi là source hoặc producer. Và đôi khi, người ta sẽ gọi stage cuối cùng là sink hoặc consumer. Giống như trong Kafka ý. Hehe

Tiếp theo, chúng ta sẽ tìm hiểu một pipeline đơn giản để hiểu rõ ideas và techniques của nó. Sau đó sẽ đến ví dụ thực tế hơn.

# Squaring numbers

Giả sử như mình cần tính bình phương của mỗi số từ 1-1000 và in ra màn hình kết quả. Cách làm đơn giản nhất là dùng 1 vòng lặp for từ 1-1000. Tại mỗi bước lặp, mình chỉ việc `fmt.Println(i*i)` là đủ. Tuy nhiên, do đang nói về pipeline nên mình sẽ giới thiệu cho các bạn một cách làm vô cùng vô cùng ảo diệu. Bắt đầu!

Đối chiếu với định nghĩa pipeline ở trên, chúng ta sẽ có 3 stage:

* Stage 1: Ném lần lượt từng số integer của list 1-1000 vào inbound channel
* Stage 2: Với mỗi số integer nhận được từ inbound channel, bình phương nó lên rồi ném vào outbound channel
* Stage 3: Nhận giá trị integer từ Stage 2 và print out.

Và cuối cùng, code mẫu sẽ như sau:

**Stage 1**

```go
func gen(nums ...int) <-chan int {
    out := make(chan int)
    go func() {
        for _, n := range nums {
            out <- n
        }
        close(out)
    }()
    return out
}
```

**Stage 2**

```go
func sq(in <-chan int) <-chan int {
    out := make(chan int)
    go func() {
        for n := range in {
            out <- n * n
        }
        close(out)
    }()
    return out
}
```

**Stage 3** Dùng luôn hàm main nhé

```go
func main() {
    // Set up the pipeline.
    c := gen(2, 3)
    out := sq(c)

    // Consume the output.
    fmt.Println(<-out) // 4
    fmt.Println(<-out) // 9
}
```

Đến đây các bạn đã hiểu idea của pipeline chưa nào? Chúng ta đi sang tiếp Fan-in và Fan-out nhé

# Fan-in, Fan-out

Nhiều goroutines có thể read từ cùng một channel cho đến khi nó đóng thì được gọi là Fan-out. Việc này có ích lợi gì? Chúng ta có 1 nhóm các goroutiness và muốn chia việc cho chúng nhằm tăng tốc độ xử lý thì đấy là lúc chúng ta nên sử dụng Fan-out.

Với Fan-in thì ngược lại, một goroutine có thể read từ nhiều channel cho đến khi tất cả channel đóng. Làm sao để biết lúc nào thì tất cả channel đã đóng? Chúng ta sẽ gộp toàn bộ channel vào 1 single channel, nếu single channel này đóng chứng tỏ toàn bộ input của chúng ta cũng không còn.

Ví dụ về Fan-in, Fan-out như sau:

```go
func main() {
    in := gen(2, 3)

    // Distribute the sq work across two goroutines that both read from in.
    c1 := sq(in)
    c2 := sq(in)

    // Consume the merged output from c1 and c2.
    for n := range merge(c1, c2) {
        fmt.Println(n) // 4 then 9, or 9 then 4
    }
}
```

Ở đây, chúng ta dễ thấy sự có mặt của function `merge`. Như mình đã nói ở trên. Merge function convert toàn bộ channels thành 1 single channel bằng cách start từng goroutine cho mỗi inbound channel, sau đó copy value tới 1 outbound channel. Chưa hết, khi output gouroutines đã started, merge function start thêm một gouroutine nữa làm nhiệm vụ close outbound channel khi tất cả value đã được gửi đi.

Ngoài ra còn phải quan tâm việc đảm bảo tất cả data đã được gửi đi trước khi close channel. Điều này có thể được giải quyết bằng cách sử dụng `sync.WaitGroup`.

Code của merge function như sau:

```
func merge(cs ...<-chan int) <-chan int {
    var wg sync.WaitGroup
    out := make(chan int)

    // Start an output goroutine for each input channel in cs.  output
    // copies values from c to out until c is closed, then calls wg.Done.
    output := func(c <-chan int) {
        for n := range c {
            out <- n
        }
        wg.Done()
    }
    wg.Add(len(cs))
    for _, c := range cs {
        go output(c)
    }

    // Start a goroutine to close out once all the output goroutines are
    // done.  This must start after the wg.Add call.
    go func() {
        wg.Wait()
        close(out)
    }()
    return out
}
```

# Stopping short

Chúng ta có một số pattern như sau:

* Stages đóng luôn outbound channel khi send hết đống value
* Stages keep value nhận được từ inbound channel cho đến khi chúng bị đóng

Pattern này cho chúng ta điều gì? Nó đảm bảo việc tất cả goroutines chỉ đóng khi tất cả value đã được gửi đi thành công.
