---
title: 基础知识复习笔记1-Java集合
header_image: /intro/post-bg.jpg
abstract: 重操旧业
categories: 学习
tags:
  - 笔记
abbrlink: 67cafe3d
date: 2024-02-12 19:55:55
---
### 前言
复习笔记系列记录一些C++/Java，本科专业课的一些知识，强化记忆

本科学习数据结构、算法、高级语言程序设计都是C++，Java是选修课。虽然大二的时候选过，但是就像自学的Python一样，会写代码但对基础原理理解不深，所以打算写笔记记录一些自己的学习理解，也方便以后查阅。

### 集合
Java的集合也叫容器，类似于C++的stack容器适配器。C++中的stack、queue和priority_queue实际上底层都是deque容器，deque实现了它们所需的方法。Java中类似，所有容器都是从Collection和Map接口派生出来的，从Collection和Map接口又派生出许多子接口，由“集合（容器）”实现这些接口的方法。

- **Collection：** 存放单一元素
  - **List**：ArrayList，LinkedList
  - **Set**：HashSet，TreeSet，LinkedHashSet
  - **Queue**：PiorityQueue
    - **Deque**：LinkedList，ArrayDeque
- **Map：** 存放键值对
  - HashMap，TreeMap，LinkedHashMap

以上黑体是接口，常规体是集合/容器。

#### ArrayList和LinkedList
ArrayList类似于C++的vector，底层用数组实现，可以随机访问、自动扩容。扩容机制也和vector类似，大约为1.5倍。

LinkedList是双向链表，头尾插入删除O(1)，随机位置插入删除平均仍然是O(n)，所以实际上与ArrayList相比优势不大，但适合作为双向队列的接口实现。

#### HashSet，HashMap
##### 底层实现
HashMap和HashSet底层实现相同，都是数组+链表。当冲突形成的拉链表长度大于8，而且数组长度大于64时，会将链表转化为红黑树，降低查找开销。数组长度不大于64则优先数组扩容。

##### 下标计算
HashSet类似于C++的unordered_set，元素存放无序、不可重复。计算元素存放位置时，先对元素进行一次hashCode()得到哈希值h，再将h和h的高16位或运算，得到最终的hash。然后`(n-1) & hash`计算出下标。当n是2的幂次时，`(n-1)&hash == hash % n`，所以HashMap表的长度总是2的幂次。

##### 冲突处理
插入的时候，先对元素进行hashcode计算，如果冲突，则调用equals对比元素的值，相同则覆盖值，不同则按照哈希冲突拉链处理。

##### 遍历
可以用foreach语法糖遍历，但遍历中不能对元素进行remove。如果需要遍历中对元素remove，需要用Iterator。
```
Iterator<Map.Entry<Object, Object>> it = mp.entrySet().iterator();
while(it.hasNext()) {
    Map.Entry<Object, Object> entry = it.next();
    System.out.println(entry.getKey() + entry.getValue());
}
```
foreach语法糖本质也是用迭代器遍历，但是remove会使用集合的remove方法，而不是迭代器的remove方法，导致出错。

不建议使用map.keySet()获取键表，然后用get(key)的方法遍历，相当于遍历了两次，效率很低。

#### TreeSet和TreeMap
两者关系和HashSet、HashMap差不多，TreeMap是有序的，会默认按照键值升序排序。

##### 底层实现
红黑树，查找和增删的复杂度相同，效率低于HashMap。

##### 自定义排序
- 方法一：自定义类实现Comparable接口，在类中重写compareTo函数：
```java
  public class Node implements Comparable<Node>{
    public int value;
    public char ch;
    public Node(int v, char c) {
        value = v;
        ch = c;
    }

    // hashCode和equals重写是为了让自定义类型在Set中去重

    @Override
    public int hashCode() {
        return new Integer(value).hashCode() + new Character(ch).hashCode();
    }

    @Override
    public boolean equals(Object o) {
        Node n2 = (Node) o;
        return value == n2.value && ch == n2.ch;
    }

    @Override
    public int compareTo(Node o) {
        return value < o.value ? 1 : -1;
    }
}

```
- 方法二：在创建TreeMap时定义比较函数compare：
```
  Map<Character, Integer> mp = new TreeMap<>(new Comparator<Character>() {
    @Override
    public int compare(Character o1, Character o2) {
        return o1 < o2 ? 1 : -1;
    }
  });
```

#### Queue
PiorityQueue默认是小顶堆，自定义比较的方法和TreeMap一样。

- poll和take都是取出一个元素，poll不会抛出异常
- peek和element都是获取顶端元素，peek不会抛出异常
- offer和add都是添加一个元素，offer不会抛出异常
- Deque的offerFirst、pollLast等同理

