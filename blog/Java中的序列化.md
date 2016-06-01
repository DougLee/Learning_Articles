title: "Java中的序列化"
date: 2016-06-01 10:15:51
tags: Java
---

## 什么是序列化(Serialization)
序列化是将对象的状态信息转换为可以存储或传输的形式的过程。一般将一个对象存储至一个存储媒介。在网络传输过程中，可以是字节或是XML等格式。而字节或XML编码格式等还可以还原完全相等的对象。这个相反的过程就是反序列化。

## 为什么需要序列化
对象序列化主要是为了支持两种特性：

1. 对象持久化
2. RMI(远程方法调用)，需要通过序列化来传递参数和返回值

<!-- more -->
Java平台允许我们在内存中创建可以复用的Java对象，但一般情况下，只有当JVM处于运行状态时，这些对象才可能存在，这些对象的生命周期不会比JVM的生命周期更长。但在实际应用中，就可能要求在JVM停止运行之后能够保存(持久化)指定的对象，并在将来重新读取被保存的对象。Java对性爱那个序列化就能够帮助我们实现这个功能。

使用Java对象序列化，在保存对象时，会把其状态保存为一组字节，之后在反序列化的时候将这些字节组装成对象。需要注意的是，对象序列化保存的是对象的状态，即它的成员变量。由此可知，对象序列化不会关注类中的静态变量。

## Java中如何进行序列化和反序列化

看一个序列化的例子

```java
public class People implements Serializable {
    /**
     * 
     */
    private static final long serialVersionUID = -2203725781495892444L;

    private String name;

    private int age;

    public People(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String toString() {
        return "name: " + name + " age: " + age;
    }

    public static void main(String[] args) throws IOException, ClassNotFoundException {
        People p = new People("LaoWang", 30);

        //序列化
        ObjectOutputStream out = new ObjectOutputStream(new FileOutputStream("test.bin"));
        out.writeObject("serialization is amzing.");
        out.writeObject(p);
        out.close();

        //反序列化
        ObjectInputStream in = new ObjectInputStream(new FileInputStream("test.bin"));
        String s = (String) in.readObject();
        People newP = (People) in.readObject();

        System.out.println(s);
        System.out.println(newP.toString());
    }
}

output:
serialization is amzing.
name: LaoWang age: 30
```

在Java中，只要实现了java.io.Serializable接口，那么它就可以被序列化。否则会抛出NotSerializableException异常。

**序列化接口没有方法或字段，仅用于标识可序列化的语义。**

那么问题来了，Serializable接口并没有方法和字段，为什么只有实现了该接口的类的对象才能被序列化？

答案在ObjectOutputStream的源码中！在ObjectOutputStream实例化的对象调用writeObject()之后会在内部调用一个**writeOjbect0**方法，该方法有如下几行代码会告诉我们问题的答案。
```java
            // remaining cases
            if (obj instanceof String) {
                writeString((String) obj, unshared);
            } else if (cl.isArray()) {
                writeArray(obj, desc, unshared);
            } else if (obj instanceof Enum) {
                writeEnum((Enum<?>) obj, desc, unshared);
            } else if (obj instanceof Serializable) {
                writeOrdinaryObject(obj, desc, unshared);
            } else {
                if (extendedDebugInfo) {
                    throw new NotSerializableException(
                        cl.getName() + "\n" + debugInfoStack.toString());
                } else {
                    throw new NotSerializableException(cl.getName());
                }
            }
```

## 用户自定义序列化

在序列化过程中，如果被序列化的类定义了**writeObject**和**readObject**方法，JVM会试图调用对象类里的writeObject和readObject方法，进行用户自定义的序列化和反序列化。如果没有这样的方法，则默认调用的是ObjectOutputStream的defaultWriteObject方法和ObjectInputStream的defaultReadObject方法。

用户自定义的writeObject和readObject方法可以允许用户控制序列化的过程，比如可以在序列化的过程中动态改变序列化的值。看如下一段代码
```java

public class People implements Serializable {
    /**
     * 
     */
    private static final long serialVersionUID = -2203725781495892444L;

    private String name;

    private int age;

    public People(String name, int age) {
        this.name = name;
        this.age = age;
    }

    // 序列化委托方法
    private void writeObject(ObjectOutputStream out) throws IOException {
        out.defaultWriteObject();
    }

    // 反序列化委托方法
    private void readObject(ObjectInputStream in) throws ClassNotFoundException, IOException {
        in.defaultReadObject();
        name = "changed ...";
    }

    public String toString() {
        return "name: " + name + " age: " + age;
    }

    public static void main(String[] args) throws IOException, ClassNotFoundException {
        People p = new People("LaoWang", 30);

        // 序列化
        ObjectOutputStream out = new ObjectOutputStream(new FileOutputStream("test.bin"));
        out.writeObject("serialization is amzing.");
        out.writeObject(p);
        out.close();

        // 反序列化
        ObjectInputStream in = new ObjectInputStream(new FileInputStream("test.bin"));
        String s = (String) in.readObject();
        People newP = (People) in.readObject();

        System.out.println(s);
        System.out.println(newP.toString());
    } 
    
output:
serialization is amzing.
name: changed ... age: 30
```
## 序列化与单例模式
通过对Singleton序列化与反序列化得到的对象是一个新的对象，这就破坏了Singleton的单例性。

**因为序列化会通过反射调用无参数的构造方法创建一个新的对象。**

要防止序列化破坏单例模式，需要在Singleton中定义readResolve方法。
```java

public class Singleton implements Serializable {
    /**
     * 
     */
    private static final long serialVersionUID = -6325747321001681720L;
    private volatile static Singleton singleton;

    private Singleton() {
    }

    public static Singleton getSingleton() {
        if (singleton == null) {
            synchronized (Singleton.class) {
                if (singleton == null) {
                    singleton = new Singleton();
                }
            }
        }
        return singleton;
    }

    private Object readResolve() {
        return singleton;
    }
}
```
至于原因可以在ObjectInputStream.readOrdinaryObject中找到
```java
        if (obj != null &&
            handles.lookupException(passHandle) == null &&
            desc.hasReadResolveMethod())
        {
            Object rep = desc.invokeReadResolve(obj);
            if (unshared && rep.getClass().isArray()) {
                rep = cloneArray(rep);
            }
            if (rep != obj) {
                handles.setObject(passHandle, obj = rep);
            }
        }
```
## 序列化相关的接口和类

+ java.io.Serializable
+ java.io.Externalizable
+ ObjectOutput
+ ObjectInput
+ ObjectOutputStream
+ ObjectInputStream

## 知识点小结

1. 在Java中，只要一个类实现了java.io.Serializable接口，那么这个类就可以被序列化
2. 通过ObjectOutputStream和ObjectInputStream对对象进行序列化和反序列化操作
3. 是否允许反序列化，不仅取决于类路径和功能代码是否一致，一个重要的点是两个类的序列化ID：serialVersionUID是否一致
4. 序列化并不保存静态变量
5. 要想父类对象也可以序列化，那父类也需要实现Serializable接口
6. Transient关键字的作用是控制变量的序列化，在变量声明前加上Transient关键字可以阻止该变量被序列化到文件中；在被反序列化后，Transient变量的赋值被设为初始值，int类型为0，对象类型为null
