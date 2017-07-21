Java中常用来处理字符串的类有三个: String, StringBuffer和StringBuilder.

## 区别
三者都继承自CharSequence接口, 首先说明三者间主要区别

+ String字符串常量
+ StringBuffer字符串变量(线程安全)
+ StringBuilder字符串变量(线程不安全)

## String
在Java中String对象是不可变的. 每次改变String类型的时候都会生成一个新的String对象, 然后将指针指向新的String对象. 

如下代码中, str最终的值"ab"并非str所引用对象的内容发生了变化, 而是str在执行的过程中重新引用了另外一个String对象"ab". 并且String的+操作, 会转化成StringBuilder的append()方法来实现.

```java
String str = "a";
str += "b";
```

特别的, 如果代码如下这样直接拼接字符串的话, 在编译过程中JVM会直接优化成把str的引用指向"ab".

```java
String str = "a" + "b";
```

## StringBuilder和StringBuffer

StringBuffer和StringBuilder都可变, 区别在于StringBuilder是线程不安全的, 而StringBuffer的API前多了一个synchronized关键字, 所以StringBuffer是线程安全的. 所谓的线程安全就是在多线程中多个线程同时操作一个对象不会出现问题.
然而真实的应用场景中, 还真没怎么见过有多个线程轮流操作一个字符串的情况.

实际上StringBuilder是在JDK 1.5中才加上的, 之前只有StringBuffer, 写过几年C#的我很是怀疑StringBuilder的这个命名是从C#借鉴来的. 至于为什么先有一个线程安全的StringBuffer后来再有一个线程不安全的StringBuilder, 而且从两者的命名来看你压根区分不出哪个是线程安全的, 个人感觉这应该是一个设计失误吧...

StringBuffer和StringBuilder都继承自AbstractStringBuilder, 而AbstractStringBuilder里有个expandCapacity方法用来扩容, 这就导致了StringBuffer和StringBuilder中另外一个重要的问题, 就是初始化时的容量问题.
### StringBuilder扩容的问题

在StringBuilder中有一个char[], 初始的容量是16. 那么问题来了, 如果要append的长度超过了16, 会发生什么?

答案是会调用expandCapacity, 成倍扩容! 所以在高性能场景下StringBuilder的初始长度很重要很重要.
```java
void expandCapacity(int minimumCapacity) {
    int newCapacity = value.length * 2 + 2;
    if (newCapacity - minimumCapacity < 0)
        newCapacity = minimumCapacity;
    if (newCapacity < 0) {
        if (minimumCapacity < 0) // overflow
            throw new OutOfMemoryError();
        newCapacity = Integer.MAX_VALUE;
    }
    value = Arrays.copyOf(value, newCapacity);
}
```
初始长度太小扩容时会带来时间消耗, 初始长度太大又会带来空间消耗. 可以参考[这里](http://www.voidcn.com/blog/FX_SKY/article/p-6302561.html)的用法来复用StringBuilder.
