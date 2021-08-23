+++
author = "Linh Nguyen"
date = 2017-03-15T16:41:00Z
description = "Nếu phần mềm thực thi lỗi, làm thế nào để bạn tìm ra nguyên nhân một cách nhanh nhất? Câu trả lời đó là: Log. Log và log!!!"
draft = false
slug = "python-for-dummies-log-sao-cho-dung"
tag = ["Python"]
title = "Python for Dummies: Log sao cho đúng"

+++

Có thể bạn xem nhẹ nhưng logging cực kỳ quan trọng. Khi một phần mềm hoạt động bình thường, nó thực thi hàng tá các tác vụ khác nhau. Nếu xảy ra lỗi, làm thế nào để bạn tìm ra nguyên nhân một cách nhanh nhất? Câu trả lời đó là "Log. Log và log!!!"

Log thế nào cho đúng? Đó là nội dung của bài viết hôm nay.

## 1. Tôi dùng `print` có được không?

Được, không ai cấm bạn cả. Tuy nhiên chỉ nên sử dụng nó trong những script đơn giản. Ở những hệ thống phức tạp hơn, **tuyệt đối** không sử dụng `print`. Vì sao ư?


Mặc định, `print` sẽ xuất nội dung ra `stdout`. Trong quá trình hoạt động, rất nhiều dữ liệu khác cũng được đẩy ra `stdout`. Sử dụng `print` sẽ tiêu tốn rất nhiều thể lực để bơi trong đống rác đó. Bạn có thể sử dụng `stderr` thay vì `stdout`, tuy nhiên điều này không làm ý tưởng này bớt tồi.

## 2. Không dùng `print` thì dùng gì?

Hãy sử dụng module `logging`. Nó là một module tiêu chuẩn do cộng đồng Python phát triển, dễ dàng sử dụng và cực kỳ linh hoạt. Bạn có thể đọc về nó ở [đây]().
Điểm khác biệt giữa `print` và `logging` module đó là:

* Logging có phân chia level cho các thông báo lỗi, bạn có thể tuỳ chọn mức độ nghiêm trọng của các thông báo này cũng như cho phép/không cho phép hiển thị chúng nếu cảm thấy không cần thiết

* Tuỳ ý cấu hình output của thông báo lỗi là console, file hay bất kể nguồn nào bạn muốn

Sử dụng module `logging` như sau:
```python
import logging

logging.warning('Watch out!')  # Hiển thị ra màn hình console
logging.info('I told you so')  # Không hiển thị gì cả???
logging.debug('Just kidding') # Không hiển thị luôn :|
```

Thử thực thi xem!

```python
➜  ~ python sample_1.py
WARNING:root:Watch out!
```

>WTH. Sao 2 dòng `INFO` và `DEBUG` không hiển thị???

Các level của module `logging` bao gồm: `DEBUG`, `INFO`, `WARNING`, `ERROR` và `CRITICAL`. Mỗi level tương ứng với một giá trị số nguyên integer. Giá trị càng cao, mức độ ưu tiên càng cao. Mặc định, `logging` sử dụng level `WARNING`, do đó 2 dòng thông báo kia không hiển thị do level thấp hơn level `WARNING`.

Sửa đoạn code trên thành:
```python
import logging

logging.basicConfig(level = logging.DEBUG)
logging.warning('Watch out!')  # Hiển thị ra màn hình console
logging.info('I told you so')  # Cũng hiển thị luôn :D
logging.debug('Just kidding') # Ok im here
```

Kết quả là:

```python
➜  ~ python sample_2.py
WARNING:root:Watch out!
INFO:root:I told you so
DEBUG:root:Just kidding
```
>WOW. Giờ thì bạn đã hiểu rồi chứ.

## 3. Tôi có cần format log message trước không?
Xét ví dụ sau:
```python
logging.debug('Just %s', 'kidding') #first

logging.debug('Just %s' % ('kidding')) #second
```
Ở dòng đầu tiên, các tham số được truyền vào `logging.debug` sẽ được `logging` module format theo dạng `'Just %s'`

Ở dòng thứ hai, chuỗi `'Just %s'` được format trước, sau đó truyền vào `logging.debug`. Docs của `logging` module đã ghi rõ, **msg là message format string** chứ không phải message string.

>Logger.debug(msg, *args, **kwargs)

>Logs a message with level DEBUG on this logger. ==The msg is the message format string, and the args are the arguments which are merged into msg using the string formatting operator.== (Note that this means that you can use keywords in the format string, together with a single dictionary argument.)

Vì vậy, bạn không cần format log message trước khi truyền vào `logging.debug` hay bất kể level khác. Nếu làm vậy, bạn đã format thừa một lần vì `logging` module đã làm việc này rồi.

## 4. Muốn ghi log ra file thì làm thế nào?
Đơn giản thôi, sử dụng `FileHandler`. Ví dụ:
```python
import logging

logger = logging.getLogger(__name__)
logger.setLevel(logging.DEBUG)

# create a file handler
handler = logging.FileHandler('app.log')
handler.setLevel(logging.DEBUG)

# create a logging format
formatter = logging.Formatter('%(asctime)s - %(name)s - %(levelname)s - %(message)s')
handler.setFormatter(formatter)

# add the handlers to the logger
logger.addHandler(handler)

logger.warning('Watch out!')
logger.info('I told you so')
logger.debug('Just kidding')
```

Ở đây, ngoài việc sử dụng output là file, format của log message cũng được thay đổi như sau:

```python
➜  ~ tail -f app.log
2017-03-15 01:03:24,004 - __main__ - WARNING - Watch out!
2017-03-15 01:03:24,005 - __main__ - INFO - I told you so
2017-03-15 01:03:24,005 - __main__ - DEBUG - Just kidding
```

>Bạn tham khảo thêm phần Logging Formaters cuối bài

## 5.Có cần ghi log ở mọi nơi không?
Chắc chắn rồi, tuy nhiên nên sử dụng level hợp lý cho từng trường hợp:

* Level `DEBUG` chỉ nên sử dụng khi bạn debug, dùng cho những thông tin liên tục thay đổi

* Level `INFO` sử dụng cho các trường hợp thay đổi trạng thái, sự kiện xảy ra bình thường

* Level `WARNING` cho các trường hợp sự kiện quan trọng nhưng không đến mức lỗi. Ví dụ: Sai mật khẩu đăng nhập, kết nối bị delay .v.v.

* Level `ERROR` sử dụng khi gặp `Exception`, `IO` fail hay `Database Connection` fail

* Level `CRITICAL` chỉ sử dụng cho các trường hợp lỗi cực kỳ nghiêm trọng như `out of memory`, `disk full`, `inode full` .v.v

## 6. Sử dụng `__name__` cho logger name làm chi vậy?

`__name__` là một `build-in variable`. Giá trị của `__name__` là tên của module hiện tại. Đặt tên cho `logger` là `__name__` mục đích để bạn dễ dàng phân biệt được message log của từng module. Đơn giản vậy thôi :D

## 7. Nếu gặp `Exception` thì log như nào?
Bạn nên ghi log kèm theo traceback. Nếu không có nó, message log của bạn không mang nhiều ý nghĩa. Sử dụng tham số `exc_info = True` để đạt được điều đó. Ví dụ:
```python
try:
    open('/path/to/does/not/exist', 'rb')
except (SystemExit, KeyboardInterrupt):
    raise
except Exception, e:
    logger.error('Failed to open file', exc_info=True)
```
Ouput:

```python
➜  ~ tail -f app.log
2017-03-15 01:05:45,757 - __main__ - ERROR - Failed to open file
Traceback (most recent call last):
  File "test.py", line 16, in <module>
    open('/path/to/does/not/exist', 'rb')
IOError: [Errno 2] No such file or directory: '/path/to/does/not/exist'
```

## 8. Tôi có nên khai báo `logger` ở level module không?
Không nên, bạn hãy theo dõi ví dụ sau:

**my_module.py**
```python
import logging

logger = logging.getLogger(__name__)

def foo():
    logger.info('Hi, foo')

class Bar(object):
    def bar(self):
        logger.info('Hi, bar')
```
**main.py**
```python
import logging

# load my module
import my_module

# load the logging configuration
logging.config.fileConfig('logging.ini')

my_module.foo()
bar = my_module.Bar()
bar.bar()
```
**logging.ini**
```
[loggers]
keys=root

[handlers]
keys=consoleHandler

[formatters]
keys=simpleFormatter

[logger_root]
level=DEBUG
handlers=consoleHandler

[handler_consoleHandler]
class=StreamHandler
level=DEBUG
formatter=simpleFormatter
args=(sys.stdout,)

[formatter_simpleFormatter]
format=%(asctime)s - %(name)s - %(levelname)s - %(message)s
datefmt=
```
Ngay lúc này, bạn mong muốn message log sẽ xuất hiện. Nhưng rất tiếc điều này không xảy ra. Bởi vì bạn khai báo `logger` tại module level và import module trước khi load cấu hình từ file `logging.ini`. Mặc định, `logging.fileConfig` và `logging.dictConfig` sẽ vô hiệu `logger` đã tồn tại. Do đó tất cả cấu hình trong file `logging.ini` sẽ không được áp dụng.

Một cách tốt hơn hết, bạn chỉ nên khai báo `logger` tại thời điểm bạn cần sử dụng nó. Mã nguồn trên sẽ thanh đổi như sau:

**my_module.py**
```python
import logging

def foo():
    logger = logging.getLogger(__name__)
    logger.info('Hi, foo')

class Bar(object):
    def __init__(self, logger=None):
        self.logger = logger or logging.getLogger(__name__)

    def bar(self):
        self.logger.info('Hi, bar')
```

>Bạn có thể set tham số `disable_existing_loggers = False` trong file `logging.ini` để giải quyết vấn đề này

## 9. Tôi nên đặt cấu hình logging ở đâu?
Bạn nên sử dụng file JSON hoặc YAML để cấu hình logging. Tất nhiên bạn cũng có thể sử dụng định dạng .INI cũ để thực hiện điều đó. Tuy nhiên, sử dụng JSON hoặc YAML sẽ dễ đọc và viết hơn.

Với trường hợp sử dụng file YAML, file cấu hình sẽ như vầy:
```yaml
---
version: 1
disable_existing_loggers: False
formatters:
    simple:
        format: "%(asctime)s - %(name)s - %(levelname)s - %(message)s"

handlers:
    console:
        class: logging.StreamHandler
        level: DEBUG
        formatter: simple
        stream: ext://sys.stdout

    info_file_handler:
        class: logging.handlers.RotatingFileHandler
        level: INFO
        formatter: simple
        filename: info.log
        maxBytes: 10485760 # 10MB
        backupCount: 20
        encoding: utf8

    error_file_handler:
        class: logging.handlers.RotatingFileHandler
        level: ERROR
        formatter: simple
        filename: errors.log
        maxBytes: 10485760 # 10MB
        backupCount: 20
        encoding: utf8

loggers:
    my_module:
        level: ERROR
        handlers: [console]
        propagate: no

root:
    level: INFO
    handlers: [console, info_file_handler, error_file_handler]
...
```
Lúc này, bạn có thể load cấu hình log bằng cách sau:
```python
import os
import logging.config

import yaml

def setup_logging(
    default_path='logging.yaml',
    default_level=logging.INFO,
    env_key='LOG_CFG'
):
    """Setup logging configuration

    """
    path = default_path
    value = os.getenv(env_key, None)
    if value:
        path = value
    if os.path.exists(path):
        with open(path, 'rt') as f:
            config = yaml.safe_load(f.read())
        logging.config.dictConfig(config)
    else:
        logging.basicConfig(level=default_level)
```
Khi khởi động chương trình, bạn chỉ việc gọi `func setup_logging()`, nó sẽ đọc cấu hình mặc định trong file `logging.yaml`. Bạn cũng có thể sử dụng biến môi trường `LOG_CFG` để load cấu hình từ một tập tin khác. Ví dụ:
```
LOG_CFG=/path/custom_logging.yaml python main.py
```
## 10. File log của tôi quá lớn, làm thế nào bây giờ?
File log lớn dần theo thời gian là điều hiển nhiên, nó ảnh hưởng trực tiếp đến dung lượng đĩa cứng. Để tránh vấn đề này, bạn nên sử dụng `RotatingFileHandler` thay cho `FileHandler`. Lúc này, các file log của bạn sẽ như sau:
```python
➜  ~ ls -al /var/log | grep opendi
-rw-r-----   1 root             admin               2898 Apr  8 22:21 opendirectoryd.log
-rw-r-----   1 root             admin               2898 Apr  7 19:38 opendirectoryd.log.0
-rw-r-----   1 root             admin              16078 Apr  6 18:34 opendirectoryd.log.1
-rw-r-----   1 root             admin               2898 Mar 30 14:47 opendirectoryd.log.2
-rw-r-----   1 root             admin              36946 Mar 30 14:46 opendirectoryd.log.3
```
Bạn tham khảo thêm thuộc tính `maxBytes` và `backupCount` tại phần *Logging Handlers* cuối bài

---
Bài viết này dựa vào phần lớn nội dung trong bài viết *"Good logging practice in Python"* khi mình ôn lại Python để sử dụng nó cho dự án cá nhân. Gần như không có trang Tiếng Việt nào đề cập chi tiết về vấn đề này. Do vậy bài viết này là cách mình hệ thống hoá lại kiến thức và tập viết một cách bài bản.

Rất vui nếu giúp ích được bạn và mình rất sẵn lòng tiếp thu mọi ý kiến đóng góp từ các bạn.

Many thanks,

## References
[1] Fangpelin "*Good logging practice in Python*" [Fang's coding note](https://fangpenlin.com/posts/2012/08/26/good-logging-practice-in-python/)

[2] Python Software Foundation "*Logging facility for Python*" [Python 2.7.13 Documentation](https://docs.python.org/2/library/logging.html)

[3] Python Software Foundation "*Logging Formaters*" [Python 2.7.13 Documentation](https://docs.python.org/2/howto/logging.html#formatters)

[4] Python Software Foundation "*Logging Handlers*" [Python 2.7.13 Documentation](https://docs.python.org/2/library/logging.handlers.html#logging.handlers.RotatingFileHandler)

[5] Hưng Nguyễn Việt "*[python] logging: đừng format log message!*" [FAMILUG](http://www.familug.org/2014/09/python-logging-ung-format-log-message.html)
