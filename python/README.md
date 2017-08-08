## 数据结构
1. *True, False, float, double, int, long, list, set, map, tuple*

## 语法

__global__
```python
number = 1
def yetanotherfunc():
    global number
    # This will correctly change the global.
    number = 3
```
__letter r__
```python
# Open the file C:\\binary.dat for writing. The letter r before the
# filename string is used to prevent backslash escaping.
myfile = open(r"C:\\binary.dat", "w")
```

__List Comprehension__
```python
#列表推导式由一个表达式以及紧跟着这个表达式的for语句构成，for语句还可以跟0个或多个if或for语句
print [x * y for x in lst1 if 4 > x > 1 for y in range(10) if y > 5]
# 在内存占用过大的情况下用生成器(Generator)
for num in (x * y for x in lst1 if 4 > x > 1 for y in range(10) if y > 5)
```

__排序__
```python
a = [('2011-03-17', '2.26', 6429600, '0.0'), ('2011-03-16', '2.26', 12036900, '-3.0'), ('2011-03-15', '2.33', 15615500,'-19.1')]
sorted(a, key=lambda result: result[1],reverse=True)

from operator import itemgetter
dict = {"a":"1","sss":"2","ffdf":'5',"ffff2":'3'}
sorted_dict = sorted(dict.items(),key=itemgetter(1))
```

__时间__
```python
import datetime,time,pytz

tz = pytz.timezone('Asia/Shanghai')
queryTime = datetime.datetime.now(tz) + datetime.timedelta(seconds=seconds)
# datetime转成string
print queryTime.strftime("%Y%m%d %H%M%S")
# string转成datetime
print datetime.datetime.strptime("20170101 000000", "%Y%m%d %H%M%S")
# datetime转成毫秒
ctime = int(time.mktime(queryTime.timetuple())*1000)

queryTime = time.localtime()
# struct_time转成string
print queryTime.strftime("%Y%m%d %H%M%S")
# struct_time转成毫秒
ctime = int(time.mktime(queryTime)) * 1000
# 毫秒转成struct_time
a = 1302153828
queryTime = time.localtime(a)
# string转成struct_time
print time.strptime("20170101 000000", "%Y%m%d %H%M%S")
```

__运算__
```python
#除后向下取整
print 5.0//2
# 2的5次方
print 2**5
```

__文件和目录__
```python
import os

fileList = []
rootdir = "/data"
for root, dirs, files in os.walk(rootdir):
    if '.svn' in dirs: dirs.remove('.svn')  # 排除特定目录    
    for file in files:
        if os.path.splitext(file)[1] == 'txt':# 查找特定扩展名的文件
            file_dir_path = os.path.join(root,file)
            fileList.append(file_dir_path)  # yield ?
print fileList
# os.mkdir,listdir,rmdir,isfile,isdir...

with open('/tmp/sth.jpg','rb') as f:
   f.read(1024) # f.read(),f.readline(),f.readlines()

from io import StringIO,BytesIO

```

__执行shell命令__
```python
import commands
(exitstatus, outtext) = commands.getstatusoutput('ls /bin/ls')
```

__捕获__
```python
try:
    do_some_func()
except KeyboardInterrupt as kbi:
    print "User Press Ctrl+C,Exit"
except EOFError as eofe:
    print "User Press Ctrl+D,Exit"
finally:
    print "finally"
```

__opt__
```python
import sys,os,getopt,traceback

def usage():
    print '''
Usage: this_script.py [options...]
Options:
-e : Exchange Name
-c : User-Defined Category Name
-h : this help info
this_script.py -c "HA Ha"
'''

try:
    opts, args = getopt.getopt(sys.argv[1:],'he:c')
except getopt.GetoptError:
    traceback.print_exc()
    usage()
    sys.exit()

if len(opts) == 0:
    usage()
    sys.exit()

for opt, arg in opts:
    if opt in ('-h', '--help'):
        print 'help message:'
        usage()
        sys.exit()
    elif opt == '-c':
        print "user-defined %s " % arg
    elif opt == '-e':
        print "Exchange Name %s" % arg
```
__函数式编程__
__高阶函数__
```python
sorted

def is_odd(n):
    return n % 2 == 1
filter(is_odd,[1,2,3])
# [1,3]
```
__偏函数__
```python
import functools 

# 把一个函数的某些参数给固定住（也就是设置默认值），返回一个新的函数，调用这个新函数会更简单
int2 = functools.partial(int, base=2)
```
__装饰器__
```python
import time
from functools import wraps
 
def timethis(func):
    '''
    Decorator that reports the execution time.
    '''
    @wraps(func)
    def wrapper(*args, **kwargs):
        start = time.time()
        result = func(*args, **kwargs)
        end = time.time()
        print(func.__name__, end-start)
        return result
    return wrapper
 
@timethis
def countdown(n):
    while n > 0:
        n -= 1
 
countdown(100000)
# 还有类装饰器
```

__上下文管理库(ContextLib)__
```python
import time
 
class demo:
    def __init__(self, label):
        self.label = label
 
    def __enter__(self):
        self.start = time.time()
 
    def __exit__(self, exc_ty, exc_val, exc_tb):
        end = time.time()
        print('{}: {}'.format(self.label, end - self.start))
 
with demo('counting'):
    n = 10000000
    while n > 0:
        n -= 1
 
# counting: 1.36000013351

# 另一种写法
from contextlib import contextmanager
import time
 
@contextmanager
def demo(label):
    start = time.time()
    try:
        yield
    finally:
        end = time.time()
        print('{}: {}'.format(label, end - start))
 
with demo('counting'):
    n = 10000000
    while n > 0:
        n -= 1
 
# counting: 1.32399988174
```

__杂项__
* 正则
```python
import re
re.match(r'\d+\w+?(\d+)','123abc456').groups() # group(0),group(1)
#等效于
pattern = re.compile(r'\d+\w+?(\d+)')
if pattern.match('123abc456'):
    print 'ok'
else:
    print 'fail'
    
re.split(r'[ac]','123abc123')
```
* 0 < x < 3
* 可以使用 del 删除变量或删除数组中的元素。
* for teama, teamb in zip(groupa, groupb):

## 类库
* pickle,json
pickle.dumps(d),pickle.dump(d,f),d = pickle.load(f)
json.dumps(d),json.dump(d,f),d = json.loads(json_str),d = json.load(f)
* collections ?
* itertools ?
