

先看一下yield from的一个简单的用法.

```python
def generator2():
    for i in range(4):
        yield i


def generator3():
    for j in range(4, 8):
        yield j


def generator():
    for i in generator2():
        yield i
    for j in generator3():
        yield j


for i in generator():
    print(i)
```

其中generator()也可以这么实现

```python
def generator():
    yield from generator2()
    yield from generator3()
```

两者的结果是一样的.

