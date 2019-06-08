---
layout: post
title: Java中String、StringBuffer、StringBuild的区别
categories: [java]
description: Java中String、StringBuffer、StringBuild的区别
---


JAVA平台提供三个类：`String、StringBuild、StringBuffer`，它们可以储存和操作字符串，即包含多个字符的字符数据。

`String`类提供了数值不可改变的字符串。而这个`StringBuffer`和`StringBuild`类提供的字符串进行修改。当你知道字符数据要改变的时候你就可以使用StringBuffer或者StringBuild。典型地，你可以使用StringBuffers或者StringBuild来动态构造字符数据。

String 类中使用 final 关键字修饰字符数组来保存字符串，`private　final　char　value[]`，所以 `String 对象是不可变的。而StringBuilder 与 StringBuffer 都继承自 AbstractStringBuilder 类，在 AbstractStringBuilder 中也是使用字符数组保存字符串char[]value 但是没有用 final 关键字修饰，所以这两种对象都是可变的。

StringBuilder 与 StringBuffer 的构造方法都是调用父类构造方法也就是 AbstractStringBuilder 实现的，大家可以自行查阅源码。

AbstractStringBuilder.java

```java
abstract class AbstractStringBuilder implements Appendable, CharSequence {
    char[] value;
    int count;
    AbstractStringBuilder() {
    }
    AbstractStringBuilder(int capacity) {
        value = new char[capacity];
    }
```

其中`StringBuffer`是线程安全的，StringBuffer对方法加了同步锁或者对调用的方法加了同步锁。`StringBuilder` 并没有对方法进行加同步锁，所以是非线程安全的。

StringBuffer.java的部分代码

```java
@Override
    public synchronized int length() {
        return count;
    }

    @Override
    public synchronized int capacity() {
        return value.length;
    }


    @Override
    public synchronized void ensureCapacity(int minimumCapacity) {
        super.ensureCapacity(minimumCapacity);
    }

    /**
     * @since      1.5
     */
    @Override
    public synchronized void trimToSize() {
        super.trimToSize();
    }

    /**
     * @throws IndexOutOfBoundsException {@inheritDoc}
     * @see        #length()
     */
    @Override
    public synchronized void setLength(int newLength) {
        toStringCache = null;
        super.setLength(newLength);
    }
```

每次对 `String` 类型进行改变的时候，都会生成一个新的 `String`对象，然后将指针指向新的 `String` 对象。`StringBuffer` 每次都会对 `StringBuffer` 对象本身进行操作，而不是生成新的对象并改变对象引用。相同情况下使用 `StringBuilder` 相比使用 `StringBuffer` 仅能获得 10%~15% 左右的性能提升，但却要冒多线程不安全的风险。

对于三者使用的总结：

1. 操作少量的数据: 适用String
2. 单线程操作字符串缓冲区下操作大量数据: 适用StringBuilder
3. 多线程操作字符串缓冲区下操作大量数据: 适用StringBuffer
