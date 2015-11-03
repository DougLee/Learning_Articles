title: "Python模块学习之re"
date: 2015-09-14 14:59:54
tags:
---

正则表达式是开发中比较重要的一个技能，可以说几乎上是没有不用做字符串处理的程序。
## 正则表达式基础

### 基本用法
下图列出了一些基本的正则表达式语法：
![pic](/image/python_Re/1.png)

### 贪婪模式

在Python里默认数量词是贪婪的，总是尝试匹配尽可能多的字符。

### 转义字符

Python在正则表达式里使用"\"作为转义字符，这就可能造成反斜杠困扰。假如你需要匹配文本中的字符"\"，那么使用编程语言表示的正则表达式里将需要4个反斜杠"\\\\"：前两个和后两个分别用于在编程语言里转义成反斜杠，转换成两个反斜杠后再在正则表达式里转义成一个反斜杠。Python里的原生字符串很好地解决了这个问题，这个例子中的正则表达式可以使用r"\\"表示。同样，匹配一个数字的"\\d"可以写成r"\d"。有了原生字符串，你再也不用担心是不是漏写了反斜杠，写出来的表达式也更直观。

## Re模块

### 初次见面

```python
import re

pattern = r"a"
s = "abcde"

print(re.match(pattern, s))
```

### re.compile(pattern, falgs=0)

这个方法是Pattern类的工厂方法，将字符串形式的正则表达式编译为Pattern对象。第二个参数是匹配模式

### re模块常用函数

#### re.search(pattern, string, flags=0)

re.search函数会在字符串内查找模式匹配，只找到第一个匹配然后返回，如果字符串没有匹配，则返回None。
```python
import re

s = '1111-1119'
pattern = r'(\d+)-(\d+)'

result = re.search(pattern, s, re.I)
if result:
    print(result.group(0))
    print(result.group(1))
    print(result.group(2))
else:
    print('Not match')
```

#### re.match(pattern, string, flags=0)
尝试从字符串的开始匹配一个模式，也可以说是匹配第一个单词

```python
import re

s = 'abc1111-1119'
pattern = r'(\d+)-(\d+)$'

result = re.match(pattern, s, re.I)
if result:
    print(result.group(0))
    print(result.group(1))
    print(result.group(2))
else:
    print('Not match')
# 结果为Not match
```

**match只检测是不是在字符串的开始位置匹配，search会扫描整个字符串查找匹配**。

#### re.findall(pattern, string, flags=0)
搜索string，以列表形式返回全部能匹配的子串,若没有匹配则返回None。

```python
import re

s = 'abc1111-1119'
pattern = r'(\d+)-(\d+)$'

t = re.findall(pattern, s)
print(t)
# result: [('1111', '1119')]
```

#### re.spit(pattern, string, maxsplit=0, flags=0)
分割字符串

+ pattern: 规则  
+ string：要分割的字符串
+ maxsplit：分割的次数，默认为0,即表示全部分割

```python
import re

s = 'a1b2c3d4'
pattern = r'\d+'

print(re.split(pattern, s, 1))
# ['a', 'b2c3d4']
print(re.split(pattern, s))
#['a', 'b', 'c', 'd', '']
```

#### re.sub(pattern, repl, string, count=0, flags=0)
替换字符串的匹配项

+ pattern: 规则 
+ repl：替换后的字符串 
+ string：要替换的字符串
+ maxsplit：匹配的次数，默认为0，全部匹配

```python
import re

s = 'a1b2c3d4'
pattern = r'\d+'

print(re.sub(pattern, '|', s, 1))
# a|b2c3d4
print(re.sub(pattern, '|', s))
#a|b|c|d|

```

#### re.subn(pattern, repl, string, count=0, flags=0)
返回替换后的字符串以及替换次数

参数同sub

```python
import re

s = 'a1b2c3d4'
pattern = r'\d+'

print(re.subn(pattern, '|', s, 1))
# ('a|b2c3d4', 1)
print(re.subn(pattern, '|', s))
#('a|b|c|d|', 4)
```
