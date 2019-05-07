# 面试随记

## 一、java 集合体系
### 1.父类 Collection --> 主要子接口： List，Set，Queue  
List主要子类：ArrayList,LinkedList，Vector  
Set主要子类：HashSet，LinkedHashSet，TreeSet  
Queue主要子类：。。。...待补充  

List添加顺序有序，元素可重复  
Set添加顺序无序，元素不可重复  

ArrayList底层数据结构为数组，增删改比较慢，查询比加快，初始化的时候构建空数组，第一次add的时候会赋初始长度，默认10，扩容为长度的一半  
LinkedList底层数据结构为链表，增删改快，查询比较慢，没有初始长度，没有扩容  
两者都是线程不安全的--------为什么，如何线程不安全？？  



HashSet  
TreeSet  
LinkedHashSet  


### 2.父类Map  --> 主要子类：HashMap，HashTable，TreeMap，ConcurrentHashMap
HashMap是线程不安全的，效率高，1.7和1.8实现线程安全的方式不同，允许null键null值  
HashTable是线程安全的，效率略低，不允许null键null值（出来的早，没有做处理）
ConcurrentHashMap是线程安全的，1.7和1.8实现线程安全的方式不同

HashMap底层是hash表（数组+链表，1.8引进红黑树---------红黑树原理？？？？？？）第一次put的时候初始化数组长度，默认长度为16，扩容因子是0.75。每次put的时候先对key进行hash计算，然后hash值对数组长度取模，找到该值在数组中的位置。数组中存储的是Node对象，Node包括hash，k，v，nextNode四个属性。当数组的长度超过当前长度和扩容因子的乘积的时候，执行扩容机制，扩容为当前长度的2倍，对原来数组中的数据重新计算hash值并定位（一般来说是原来的位置或原来的位置+扩容长度）。对key的hash计算是一个算法优化点，减少了hash碰撞。当数组的位置之前有数据的时候，比较两个key是否相同，相同的话替换原来的值，不相同的话在原来的key后面按照链表的方式添加元素。1.8之后当链表的长度大于8的时候转换为红黑树，小于6的时候再从红黑树转换为链表
HashTable是线程安全的，（为什么线程安全，哪里线程安全）我只知道是用synchronized的保证了线程安全，应该是扩容的时候会保证线程安全
ConcurrentHashMap是线程安全的，1.8之前应该是采用分段锁保证线程安全，1.8之后是采用cas机制+synchronized保证线程安全


# 问题收集
1.arrayList和LinkedList为什么线程不安全，如何线程安全
2.红黑树
3.hashTable和concurrentHashMap哪里控制了线程安全?扩容的时候吗，还有呢？

