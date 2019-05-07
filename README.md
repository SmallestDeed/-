# 面试随记

## java 集合体系
父类 collection --> 主要子接口： list，set，queue  
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


父类Map  --> 主要子接口
