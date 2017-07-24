## 数据结构
1. *bool.True, bool.False, float, double, int, long, list, set, map, tuple*

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
```

__杂项__
* 可以使用 del 删除变量或删除数组中的元素。

## 类库
* pickle ?
