# python code snippet

说明：以下代码均基于python3.6

<!-- TOC -->

- [python code snippet](#python-code-snippet)
    - [获取文件夹中所有文件、文件夹](#获取文件夹中所有文件文件夹)
    - [创建文件夹](#创建文件夹)
    - [读取文件](#读取文件)
        - [按行读取文件](#按行读取文件)
        - [读取json文件](#读取json文件)
        - [读取csv文件](#读取csv文件)
    - [写入文件](#写入文件)
        - [生成json文件](#生成json文件)
        - [生成csv文件](#生成csv文件)
    - [正则表达式](#正则表达式)
        - [正则`match()`和`search()`的不同](#正则match和search的不同)
    - [字符串](#字符串)
        - [字符串格式化](#字符串格式化)
            - [格式化例子](#格式化例子)
        - [判断字符串中是否包含中文](#判断字符串中是否包含中文)
        - [特殊字符处理](#特殊字符处理)
    - [伪随机数](#伪随机数)
        - [获取一个集合内随机的不重复元素](#获取一个集合内随机的不重复元素)
    - [时间与日期](#时间与日期)
        - [时间转换为格式字符串](#时间转换为格式字符串)
        - [字符串格式化为时间](#字符串格式化为时间)
        - [时区](#时区)
        - [计算时间差](#计算时间差)
        - [获取一段时间](#获取一段时间)
    - [并发编程](#并发编程)
        - [多线程+多进程处理文件](#多线程多进程处理文件)
        - [限制并发数量](#限制并发数量)
            - [使用`multiprocessing.Queue`控制并发数量](#使用multiprocessingqueue控制并发数量)
            - [使用pool限制进程数量](#使用pool限制进程数量)
    - [协程](#协程)
    - [异常处理](#异常处理)
        - [捕捉异常，打印异常栈](#捕捉异常打印异常栈)
    - [日志系统](#日志系统)
        - [SMTP Handler](#smtp-handler)
    - [网络](#网络)
        - [发送GET请求](#发送get请求)
        - [发送POST请求](#发送post请求)
            - [requests包发送POST请求](#requests包发送post请求)
    - [面向对象](#面向对象)
        - [常用方法](#常用方法)
        - [对象判等](#对象判等)
    - [链接数据库](#链接数据库)
        - [链接mongodb](#链接mongodb)
        - [`ObjectId`和`string id`相互转换](#objectid和string-id相互转换)
        - [更新数据](#更新数据)
        - [排序](#排序)
        - [与json转换](#与json转换)
        - [正则查询](#正则查询)
        - [获取所有字段名称用于导出数据](#获取所有字段名称用于导出数据)
        - [批量更新](#批量更新)
    - [测试](#测试)
        - [单元测试](#单元测试)
        - [基准测试](#基准测试)

<!-- /TOC -->

## 获取文件夹中所有文件、文件夹

``` python
import os
with os.scandir(path) as it:
    for entry in it:
        if entry.is_file() and entry.name.endswith('.xlsx'):
            # do something
```

`os.scandir()`方法返回一个`DirEntry`对象的迭代器，通过`DirEntry`对象可以获取文件或文件夹的属性。

- 查看 [`os.scandir()`](https://docs.python.org/3.5/library/os.html?highlight=scandir#os.scandir)
- 查看 [`os.DirEntry`](https://docs.python.org/3.5/library/os.html?highlight=scandir#os.DirEntry)

注意：文档中有说明

> Using `scandir()` instead of `listdir()` can significantly increase the performance of code that also needs file type or file attribute information, because DirEntry objects expose this information if the operating system provides it when scanning a directory.

## 创建文件夹

``` python
import os
if not os.path.exists(path):
    os.mkdir(path)
```

## 读取文件

使用`open`方法和`with`结构可以方便的读取文件，并释放资源：

``` python
with open(file_path, mode="r", encoding="utf-8") as f:
    content = f.read()
```

1. `open()`方法中`mode`参数为读取文件的方式，常用的有:

    - `r`：只读；
    - `r+`：既可读，也可写；
    - `rb`：以字节方式读取文件，此模式下不能设置编码格式，即不设置参数`encoding`。

2. `file`对象的`read()`方法，可以接受数字类型的参数值，如`r.read(1024)`：

   - 当`open()`的参数`mode=r`，则是读取的**字符**个数；
   - 当`open()`的参数`mode=rb`，则是读取的**字节**个数。

3. 编码方式为`UTF-8 with BOM`的文件，`encoding`参数为`utf-8-sig`。

### 按行读取文件

有些情况下，文件内容是按行存放的，这些文件也可能比较大，这样的话按行读取就比较好处理了。

``` python
with open(file_path, mode="r", encoding="utf-8") as f:
    line = f.readline()
    while line:
        # do something ...
        line = f.readline()
```

``` python
with open(file_path, mode="r", encoding="utf-8") as f:
    lines = f.readlines()
    for line in lines:
        # do something ...
```

上述情况，与下面的代码等价：

``` python
for line in open(file_path, mode="r", encoding="utf-8"):
    # do something ...
```

`readline(size=-1)`和`readlines(hint=-1)`可以传入字节数来控制获取内容的大小，如：

``` python
with open(file_path, mode="r", encoding="utf-8") as f:
   lines = f.readlines(10)
    while lines:
        for line in lines:
            # do something ...
        lines = f.readlines(10)
```

### 读取json文件

``` python
import json
with open(json_path, mode="r", encoding="utf-8") as f:
    content = json.load(f)
```

### 读取csv文件

[csv doc](https://docs.python.org/3.5/library/csv.html#id3)

1. 需要导入包：`csv`;
2. `open`函数需要设置参数`newline=''`;
3. `csv.reader(f, dialect='excel-tab')`设置方言（dialect）为excel-tab可以读取以tab键为分割的txt文件；
4. `csv.DictReader(f)`读取文件，可以根据header获取相应列的内容。

``` python
import csv
with open(csv_path, mode='r', encoding='utf-8', newline='') as f:
    reader = csv.reader(f)
    header = next(reader) # 获取表头信息，有表头的时候使用
    for row in reader:
        print(row[0])
```

## 写入文件

``` python
with open(file_path, mode="w", encoding="utf-8") as f:
    f.write(content)
```

1. `open()`方法中`mode`参数为读取文件的方式，常用的有:

    - `w`：只写，文件存在会被重写，文件不存在则创建；
    - `w+`：既可读，也可写；
    - `wb`：以字节方式写文件，此模式下不能设置编码格式，即不设置参数`encoding`；
    - `a`：追加模式，文件存在则追加文件内容，不存在则创建；
    - `ab`：字节方式的追加模式。

### 生成json文件

``` python
import json
with open('./output/' + file_name, 'w', encoding="utf-8") as f:
    json.dump(content, f)
```

- 设置`json.dump()`方法的参数`ensure_ascii=False`，就可以正常导出json中的中文（或者其他非ASCII编码的字符）了。
- 设置`json.dump()`方法的参数`separators=(',', ':')`，就可以将导出紧凑的json格式了。
- 设置`json.dump()`方法的参数`indent=4`，就可以将导出进行缩进格式化后的json的了。

### 生成csv文件

``` python
import csv
with open(csv_path, mode='w', encoding='utf-8') as f:
    writer = csv.writer(f)
    writer.writerow(['data1', 'data2', 'data3']) # 单行写入
    writer.writerows([['data1', 'data2', 'data3']]) # 多行写入
```

## 正则表达式

1. 字符串替换匹配项：

    ``` python
    import re
    pattern = r''
    string = re.sub(pattern, "", string)
    ```

2. 使用正则表达式语法`(?P<name>...)`

    可以将匹配到的内容与`name`组成一个dict

    ``` python
    import re
    m = re.match(r"(?P<first_name>\w+) (?P<last_name>\w+)", "Malcolm Reynolds")
    m.group('first_name') # 'Malcolm'
    m.group('last_name') # 'Reynolds'
    m.groupdict() # {'first_name': 'Malcolm', 'last_name': 'Reynolds'}
    ```

3. 使用`start()`和`end()`截取字符串

    ``` python
    email = "tony@tiremove_thisger.net"
    m = re.search("remove_this", email)
    email[:m.start()] + email[m.end():] # 'tony@tiger.net'
    ```

### 正则`match()`和`search()`的不同

- `match()`方法从字符串头部开始匹配，如果第一个字符不匹配则该字符串都不匹配；
- `search()`方法从字符串中查找匹配的字符，并给出第一个匹配子串的位置。

参看：`https://docs.python.org/3.5/library/re.html#search-vs-match`

## 字符串

### 字符串格式化

1. [python format string syntax](https://docs.python.org/2/library/string.html#format-examples)

    ``` python
    '{1} {0}'.format(*["first", "last"]) # unpacking argument sequence
    ```

   - 上例子中`*`是解包操作；

2. [Formatted string literals](https://docs.python.org/3/reference/lexical_analysis.html#f-strings) (f-string)

    ``` python
    name = "Fred"
    f"He said his name is {name}."
    ```

3. [printf-style String Formatting](https://docs.python.org/3/library/stdtypes.html#printf-style-string-formatting) (`%` format)

    ``` python
    name = "Fred"
    "His name is %s" % name
    ```

- 有关`%`操作符和`str.format()`方法的讨论：[Python string formatting: % vs. .format](https://stackoverflow.com/questions/5082452/python-string-formatting-vs-format/)
- 有关字符串格式化的介绍：[Formatted Output](https://www.python-course.eu/python3_formatted_output.php)

#### 格式化例子

[Python document: format-examples](https://docs.python.org/3/library/string.html#format-examples)

1. 格式化时间format方式

    ``` python
    >>> import datetime
    >>> d = datetime.datetime(2010, 7, 4, 12, 15, 58)
    >>> '{:%Y-%m-%d %H:%M:%S}'.format(d)
    ```

2. 百分比计算

    ``` python
    >> points = 19
    >>> total = 22
    >>> 'Correct answers: {:.2%}'.format(points/total)
    'Correct answers: 86.36%'
    ```

3. 千位分隔

    ``` python
    >>> '{:,}'.format(1234567890)
    '1,234,567,890'
    ```

4. 替换 %s and %r:

    ``` python
    >>> "repr() shows quotes: {!r}; str() doesn't: {!s}".format('test1', 'test2')
    "repr() shows quotes: 'test1'; str() doesn't: test2"
    ```

5. 对齐文字

    ``` python
    Aligning the text and specifying a width:

    >>> '{:<30}'.format('left aligned')
    'left aligned                  '
    >>> '{:>30}'.format('right aligned')
    '                 right aligned'
    >>> '{:^30}'.format('centered')
    '           centered           '
    >>> '{:*^30}'.format('centered')  # use '*' as a fill char
    '***********centered***********'
    ```

### 判断字符串中是否包含中文

``` python
import re
chinese_pattern = re.compile(r'[\u4e00-\u9fa5]+')

string = '有中文'
if chinese_pattern.search(string):
    print(string + '包含中文')
```

### 特殊字符处理

`\xa0` 字符处理

- `https://stackoverflow.com/questions/10993612/python-removing-xa0-from-string`
- `https://www.cnblogs.com/BlackStorm/p/6359005.html`

TODO `\u3000` 字符处理

TODO: 字符串中带有`u`未解码的字符处理

## 伪随机数

### 获取一个集合内随机的不重复元素

```python
random.sample(sample_space, k=num)
```

- 从sample_space中选取k个独立元素

## 时间与日期

``` python
from datetime import datetime
import time

# 获取当前时间
dt = datetime.now()

# 获取时间戳
t = dt.timestamp()
# or
t = time.time()

# 时间戳转换成datetime
dt = datetime.fromtimestamp(t)
```

### 时间转换为格式字符串

``` python
from datetime import datetime

dt = datetime.now()

# 转换成ISO 8601标准字符串
date_str = dt.isoformat()

# 自定义转换
date_str = dt.strftime("%Y-%m-%dT %H:%M:%S")
```

- [datetime.isoformat()文档](https://docs.python.org/3.6/library/datetime.html#datetime.datetime.isoformat)


### 字符串格式化为时间

``` python
from datetime import datetime

date_str = '2019-05-19 02:17 PM'

dt = datetime.strptime(date_str, "%Y-%m-%d %I:%M %p")
start = datetime.strptime("13:30:27,369", "%H:%M:%S,%f")
end = datetime.strptime("13:31:21,852", "%H:%M:%S,%f")
```

- [格式化说明](https://docs.python.org/3.6/library/datetime.html#strftime-and-strptime-behavior)

### 时区

``` python
from datetime import datetime, timezone, timedelta

# UTC东8区， 即北京时间
dt = datetime.now(tz=timezone(timedelta(hours=8)))

dt.tzname() # 'UTC+08:00'
```

### 计算时间差

``` python
from datetime import datetime

start = datetime.strptime("13:30:27,369", "%H:%M:%S,%f")
end = datetime.strptime("13:31:21,852", "%H:%M:%S,%f")
duration = end - start # duration 类型 datetime.timedelta
# datetime.timedelta Only days, seconds, and microseconds remain
(d.days, d.seconds, d.microseconds)
# (-1, 86399, 999999)
```

### 获取一段时间

``` python
from datetime import datetime, timedelta

def convert2datetime(time_value: str):
    return datetime.strptime(time_value, "%Y-%m-%d")

def datetime2str(date):
    return date.strftime("%Y-%m-%d")

def time_range(start, end):
    delta = timedelta(days=1)
    start_time = convert2datetime(start)
    end_time = convert2datetime(end)
    while start_time <= end_time:
        yield start_time
        start_time = start_time + delta

if __name__ == '__main_':
    start = '2019-12-02'
    end = '2020-03-10'
    for time in time_generator():
        print(datetime2str(time))
```

TODO different between datatime & time

## 并发编程

TODO: need more details, examples

并发编程先要分析任务是计算量大还是读写（IO）量大：

- 读写量大的话推荐使用线程或者异步；
- 计算量大的话推荐使用进程，但是注意进程数量和cpu核数的平衡，如果进程数量过多，会导致CPU频繁调度，效率会变低；

### 多线程+多进程处理文件

该方式处理的文件为每行为一个json对象。

TODO: 初期的代码，待优化

``` python
import os, json, math
import multiprocessing, threading

process_num = 3 # 进程最大数量
thread_num = 10 # 线程最大数量

def core_execute(lines):
    output_file = 'output.json'
    with open(output_file, mode='a', encoding='utf-8') as output:
        for line in lines:
            # 处理数据
            obj = json.loads(line)
            converted = execute(obj)
            output.write(json.dumps(converted, ensure_ascii=False, separators=(',', ':')) + "\n")

def assign_thread(lines):
    batch_size = math.ceil(len(lines) / thread_num) # 计算每个线程需要处理的数量
    thread_pool = []
    # 分配线程
    for i in range(thread_num):
        offset = batch_size * i
        t = threading.Thread(target=core_execute, args=(lines[offset:offset + batch_size],))
        t.start()
        thread_pool.append(t)
    for thread in thread_pool:
        thread.join()

def assign_process():
    execute_file = 'should_be_execute.json'
    output_file = 'output.json'
    if os.path.exists(output_file):
        os.remove(output_file)
    buffer_size = int(10 * 1024 * 1024 / 2) # 一次读取文件的大小
    process_pool = []
    with open(execute_file, mode='r', encoding='utf-8') as f:
        # 读取并分配进程
        lines = f.readlines(buffer_size)
        while lines:
            logging
            p = multiprocessing.Process(target=assign_thread, args=(lines,))
            p.start()
            process_pool.append(p)
            while len(process_pool) >= process_num:
                process_pool.pop(0).join
            lines = f.readlines(buffer_size)
        for process in process_pool:
            process.join()
```

注：多进程不可使用一个文件对象，因为每个进程会拷贝一个文件对象，会导致各进程间写文件时相互覆盖。

### 限制并发数量

#### 使用`multiprocessing.Queue`控制并发数量

> multiprocessing.Queue are thread and process safe.

场景需求：

1. 根据指定数据（如读取文件、数据库）请求API或者其他网络资源；
2. 将获取到的资源进行处理；
3. 网络资源的请求同一时间最多不能超过10个

``` python
import threading
from multiprocessing import Process, Queue

def worker(data, q: Queue, token_q: Queue):
    try:
        # request_web_resources will require API or some web resources
        result = request_web_resources(org)
    except Exception as e:
        print(e)
    else:
        q.put(result)
    finally:
        token_q.get()

def producer(result_q: Queue):
    token_q = Queue(10)  # 限制线程数量为10
    thread_pool = []
    count = 1
    # data_generator will generate some data used to requesting API or some web resources
    for data in data_generator(file_path):
        token_q.put(1)
        print(f'begin executing {count}')
        t = threading.Thread(target=worker, args=(data, result_q, token_q))
        t.start()
        thread_pool.append(t)
        count += 1
    for thread in thread_pool:
        thread.join()
    result_q.put(None) # stop consumer

def consumer(result_q: Queue):
    while True:
        item = result_q.get()
        if item is None:
            break
        # user item to do something

if __name__ == '__main__':
    result_q = Queue()
    p1 = Process(target=producer, args=(result_q,))
    p1.start()
    p2 = Process(target=consumer, args=(result_q,))
    p2.start()
    p1.join()
    p2.join()
```

注意：线程之间的通信也可以使用`queue.Queue`

> queue module is especially useful in threaded programming when information must be exchanged safely between multiple threads.

#### 使用pool限制进程数量

[Using multiprocessing pool in Python](https://stackoverflow.com/questions/55535961/using-multiprocessing-pool-in-python)

注意：

`pool.apply_async()` 执行的方法出错不会影响整个程序的执行，很难判断出错原因，建议可以加上`error_callback`来处理。

关注返回结果：

``` python
if __name__ == '__main__':
    # start 4 worker processes
    with Pool(processes=4) as pool:

        # print "[0, 1, 4,..., 81]"
        print(pool.map(f, range(10)))

        # print same numbers in arbitrary order
        for i in pool.imap_unordered(f, range(10)):
            print(i)

        # evaluate "f(20)" asynchronously
        res = pool.apply_async(f, (20,))      # runs in *only* one process
        print(res.get(timeout=1))             # prints "400"

        # evaluate "os.getpid()" asynchronously
        res = pool.apply_async(os.getpid, ()) # runs in *only* one process
        print(res.get(timeout=1))             # prints the PID of that process

        # launching multiple evaluations asynchronously *may* use more processes
        multiple_results = [pool.apply_async(os.getpid, ()) for i in range(4)]
        print([res.get(timeout=1) for res in multiple_results])
```

不关注返回结果

``` python
if __name__ == '__main__':
    # start 4 worker processes
    with Pool(processes=4) as pool:
        pool.apply_async(f, (20,))
        pool.close()
        poll.join()
```

注意：`pool.close()`一定要在`poll.join()`之前执行

> `pool.join()`：Wait for the worker processes to exit. One must call close() or terminate() before using join().

## 协程

TODO：例子和说明

[Python实战异步爬虫(协程)+分布式爬虫(多进程)](https://blog.csdn.net/SL_World/article/details/86633611)

[Python异步IO之协程(一):从yield from到async的使用](https://blog.csdn.net/SL_World/article/details/86597738)

[深入理解yield](http://www.cnblogs.com/coderzh/articles/1202040.html)

## 异常处理

### 捕捉异常，打印异常栈

``` python
import traceback

try:
    # do something
except Exception as e:
    err_stack = traceback.format_exc()
    print("doing something, but get error: %s" % e)
    print('error stack:\n%s' % err_stack)
finally:
    # close something
```

## 日志系统

1. 常用日志配置

    ``` python
    logging.basicConfig(filename='execute.log',
            filemode='w', level=logging.DEBUG,
            format='%(asctime)s - %(threadName)s - %(levelname)s - %(message)s',
            datefmt='%Y/%m/%d %I:%M:%S %p')
    ```

    打印日志格式如：`2018/07/06 11:54:48 AM - MainThread - INFO - message`

### SMTP Handler

可以通过SMTP协议将日志以邮件的形式发送

``` python
import logging
from logging.handlers import SMTPHandler


def get_smtp_logger():
    logger = logging.getLogger("smtp")
    server_host = ''  # 更换为具体邮件服务器的URL
    fromaddr = ''  # 发件人
    toaddrs = ''  # 收件人，使用list可以发送多个
    subject = 'test logger email' # 发件的主题内容
    password = ""  # password
    smtp_handler = SMTPHandler(
        mailhost=server_host,
        fromaddr=fromaddr,
        toaddrs=toaddrs,
        subject=subject,
        credentials=(fromaddr, password)
    )
    formatter = logging.Formatter('%(asctime)s - %(levelname)s - %(message)s')
    smtp_handler.setLevel(logging.WARNING)
    smtp_handler.setFormatter(formatter)
    logger.addHandler(smtp_handler)
    return logger


if __name__ == '__main__':
    logger = get_smtp_logger()
    logger.warning("this is a warning test")
    logger.error("this is a error test")
```

> 使用场景：
> 由于每次打印一次可输出的日志信息就会发一封邮件，所以不适合频繁打印的日志信息。
> 建议使用于WARNING或者ERROR级别的日志输出，在发生致命错误或异常的时候，如服务崩溃提示，可以在最上层进行异常捕捉，在无法处理异常需要提示维护人员的时候，可以将错误和异常堆栈信息打印并发送邮件。

## 网络

可以使用python的原生包：

- Python document: [HOWTO Fetch Internet Resources Using The urllib Package](https://docs.python.org/3.6/howto/urllib2.html)

也可以使用`requests`包:

- requests document：[Quickstart](http://docs.python-requests.org/en/latest/user/quickstart/#quickstart)

### 发送GET请求

``` python
import json
import urllib.parse
import urllib.request

url = 'https://www.example.com'
values = {'name' : 'Michael Foord',
          'location' : 'Northampton',
          'language' : 'Python' }
url_values = urllib.parse.urlencode(values)
full_url = url + '?' + url_values
req = urllib.request.Request(full_url)
req.add_header('Authorization', '') # add header
with urllib.request.urlopen(req) as response:
  resp_data = response.read().decode('utf-8')
  results = json.loads(resp_data) # for json results
  print(results)
```

### 发送POST请求

[stackoverflow: How to Send Post Request](https://stackoverflow.com/questions/11322430/how-to-send-post-request)

``` python
import json
import urllib.parse
import urllib.request

url = 'https://www.example.com'
values = {'name' : 'Michael Foord',
          'location' : 'Northampton',
          'language' : 'Python' }
data = urllib.parse.urlencode(values)
data = data.encode('utf-8') # data should be bytes
req = urllib.request.Request(url, data=data, method='POST')
req.add_header('Content-Type', 'application/json')
with urllib.request.urlopen(req) as response:
    resp_data = response.read().decode('utf-8')
    results = json.loads(resp_data) # for json results
    print(results)
```

// TODO 发送from表格的数据

// TODO 发送文件

#### requests包发送POST请求

requests document: [More complicated POST requests](http://docs.python-requests.org/en/latest/user/quickstart/#more-complicated-post-requests)

TODO: 发送json，发送表格，发送文件

``` python
import requests
import json

url = 'https://www.example.com'
values = {'name' : 'Michael Foord',
          'location' : 'Northampton',
          'language' : 'Python' }
headers = {'user-agent': 'my-app/0.0.1'}
r = requests.post(url, json=payload, headers=headers)
print(r.text)
```

## 面向对象

### 常用方法

The getattr(obj, name[, default]) − to access the attribute of object.

The hasattr(obj,name) − to check if an attribute exists or not.

The setattr(obj,name,value) − to set an attribute. If attribute does not exist, then it would be created.

The delattr(obj, name) − to delete an attribute.

### 对象判等

TODO：`==`、`is`、`in`区别

TODO: `@staticmethod` 和 `@classmethod` 的区别，和各自使用的场景

## 链接数据库

### 链接mongodb

- 需要下载[`pymongo`](https://api.mongodb.com/python/current/index.html#)作为python链接数据库的驱动。

``` python
from pymongo import MongoClient
client = MongoClient(host=host, port=port) # type of port is int
db = client[db_name]
# 权限验证则
db.authenticate(name=username, password=password, mechanism='SCRAM-SHA-1')
collection = db[collection_name]
results = collection.find({})
for result in results:
    print(str(result['_id']))
```

或者，可以：

``` python
client = MongoClient(host=host,
                    port=port,
                    username=username,
                    password=password,
                    authSource=db_name,
                    authMechanism='SCRAM-SHA-1')
db = client[db_name]
collection = db[collection_name]
results = collection.find({})
for result in results:
    print(str(result['_id']))
```

### `ObjectId`和`string id`相互转换

``` python
import bson
obj_id = bson.ObjectId(string_id) # string_id -> ObjectId
string_id = str(obj_id) # ObjectId -> string_id
```

### 更新数据

replace_one

### 排序

### 与json转换

``` python
from bson import json_util
```

### 正则查询

### 获取所有字段名称用于导出数据

### 批量更新

## 测试

### 单元测试

import unittest

### 基准测试

[如何对你的Python代码进行基准测试](https://www.cnblogs.com/meishandehaizi/p/5863234.html)