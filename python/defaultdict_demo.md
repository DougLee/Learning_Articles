## 初识defaultdict

之前在使用字典的时候, 用的比较随意, 只是简单的使用*dict*. 
然而这样在使用不存在的key的时候发生KeyError这样的一个报错, 这时候就该*defaultdict*登场了.

## 如何使用defaultdict

可以这样
```python
from collections import defaultdict

d1 = defaultdict(int)
```
或者这样
```python
import collections

d1 = collections.defaultdict(int)
```

## defaultdict与dict实例化字典类型的区别

使用defaultdict任何未定义的key都会默认返回一个根据method_factory参数不同的默认值, 而相同情况下dict()会返回KeyError.
比较下面代码:
```python
d1 = dict()
d2 = defaultdict(list)
print(d1['a'])
print(d2['a'])
```

## defaultdict的构造

python官方文档中对defaultdict的定义如下:

    class collections.defaultdict([default_factory[, ...]])

python官方文档中对defaultdict的解释如下:

    defaultdic
    dict subclass that calls a factory function to supply missing values

default_factory 接收一个工厂函数作为参数, 例如*int* *str* *list* *set*等.
defaultdict在dict的基础上添加了一个*__missing__(key)*方法, 在调用一个不存的key的时候, defaultdict会调用__missing__, 返回一个根据default_factory参数的默认值, 所以不会返回Keyerror.


## Example

Example 1
```python
s = 'mississippi'
d = defaultdict(int)
for k in s:
    d[k] += 1
print(d)
```

Example 2
```python
s = [('red', 1), ('blue', 2), ('red', 3), ('blue', 4), ('red', 1), ('blue', 4)]
d = defaultdict(set)
for k, v in s:
    d[k].add(v)

print(d)
```
