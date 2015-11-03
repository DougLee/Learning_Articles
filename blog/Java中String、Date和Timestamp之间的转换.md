title: "Java中String、Date和Timestamp之间的转换"
date: 2015-09-24 14:18:47
tags: java
---
记录一下Java中String和Date、Timestamp之间的转换。下文的代码可能会用到一些包。

```java
import java.sql.Timestamp;
import java.text.DateFormat;
import java.text.SimpleDateFormat;
import java.util.Date;
```

### String和Date之间的转换

```java
Date date = new Date();
SimpleDateFormat dateformat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        
//Date to String
String a1 = dateformat.format(date);
System.out.println("Date to String：" + a1);

try {
    //String to Date
    Date d = dateformat.parse("2015-09-24 10:48:06");
    System.out.println("String to Date: " + d);
} catch (Exception e) {
    e.printStackTrace();
}
```

### String和Timestamp之间的转换

```java
Timestamp ts = new Timestamp(System.currentTimeMillis());
System.out.println("Timestamp: " + ts);

// String to Timestamp
String tsStr = "2011-05-09 11:49:45";
try {
    ts = Timestamp.valueOf(tsStr);
    System.out.println("String to Timestamp: " + ts);
} catch (Exception e) {
    e.printStackTrace();
}

// Timestamp to String
String tsStr1 = "";
DateFormat sdf = new SimpleDateFormat("yyyy/MM/dd HH:mm:ss");
try {
    // 方法一
    tsStr1 = sdf.format(ts);
    System.out.println("Timestamp to String1: " + tsStr);
    // 方法二
    tsStr1 = ts.toString();
    System.out.println("Timestamp to String2: " + tsStr);
} catch (Exception e) {
    e.printStackTrace();
}
```

### Date和Timestamp之间的转换
Timestamp继承自Date

```java
Timestamp ts = new Timestamp(System.currentTimeMillis());
System.out.println("Timestamp: " + ts);
// Date to Timestamp (father to child)
Timestamp ts1 = new Timestamp(date.getTime());
System.out.println("Date to Timestamp: " + ts1);

// Timestamp to Date
try {
    date = ts;
    System.out.println("Timestamp to Date: " + date);
} catch (Exception e) {
    e.printStackTrace();
}
```
