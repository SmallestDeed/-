面试体系小记
======


# 一、java 集合体系
## 1.父类 Collection --> 主要子接口： List，Set，Queue  
* List主要子类：ArrayList,LinkedList，Vector  
* Set主要子类：HashSet，LinkedHashSet，TreeSet  
* Queue主要子类：。。。...待补充  
<br>

* List添加顺序有序，元素可重复  
* Set添加顺序无序，元素不可重复  
<br>

* ArrayList底层数据结构为数组，增删改比较慢，查询比加快，初始化的时候构建空数组，第一次add的时候会赋初始长度，默认10，扩容为长度的一半，有sort()方法可以排序，传入比较器即可  
* LinkedList底层数据结构为链表，双向链表，增删改快，查询比较慢，没有初始长度，没有扩容，没有sort()方法排序  
两者都是线程不安全的--------为什么，如何线程不安全？？
* Vector是线程安全的,用synchronized修饰了
<br>
<br>

* HashSet底层是hashMap的key，允许一个null键，允许null值，线程不安全，无序，不可重复  
* TreeSet底层是treeMap的key，可排序  
* LinkedHashSet底层是LinkedHashMap，插入和输出顺序一致  
<br>

## 2.父类Map  --> 主要子类：HashMap，HashTable，TreeMap，ConcurrentHashMap
* HashMap是线程不安全的，效率高，1.7和1.8实现线程安全的方式不同，允许null键null值，据说null键存放在node数组的首位？？？  
* HashTable是线程安全的，效率略低，不允许null键null值（出来的早，没有做处理）
* ConcurrentHashMap是线程安全的，1.7和1.8实现线程安全的方式不同

* HashMap底层是hash表（数组+链表，1.8引进红黑树---------红黑树原理？？？？？？）第一次put的时候初始化数组长度，默认长度为16，扩容因子（负载因子）默认是0.75。每次put的时候先对key进行hash计算，然后hash值对数组长度做类似取模的运算（做了位运算和&运算还是啥的），找到该值在数组中的位置。数组中存储的是Node对象，Node包括hash，k，v，nextNode四个属性。当数组的长度超过当前长度和扩容因子的乘积的时候，执行扩容机制，扩容为当前长度的2倍，对原来数组中的数据重新计算hash值并定位（一般来说是原来的位置或原来的位置+扩容长度）。对key的hash计算是一个算法优化点，减少了hash碰撞。hash碰撞：key计算hash之后得到相同的位置。减少hash碰撞的办法：1.数组的长度必须是偶数，奇数的话数组中的有些位置会分配不到；2.可以判断当前位置有值的话+1。当数组的位置之前有数据的时候，比较两个key是否相同，相同的话替换原来的值，不相同的话在原来的key后面按照链表的方式添加元素。key必须重写hashCode方法和equels方法。1.8之后当链表的长度大于8的时候转换为红黑树，小于6的时候再从红黑树转换为链表。 

* HashTable是线程安全的，（为什么线程安全，哪里线程安全）我只知道是用synchronized的保证了线程安全，应该是扩容的时候会保证线程安全  
* ConcurrentHashMap是线程安全的，1.8之前应该是采用分段锁保证线程安全，1.8之后是采用cas机制+synchronized保证线程安全  
* LinkedHashMap保证了插入和输出的顺序  
* TreeMap底层是红黑树，线程不安全，插入的元素必须实现comparator接口或comparable接口，插入之后即按一定方式排序  


# 问题收集
### 1.arrayList和LinkedList为什么线程不安全，如何线程安全
ArrayList没有线程安全的控制，add方法中判断当前数组是否足够长度是线程不安全的；判断完给数组某一位置赋值的时候也是线程不安全的，造成替换原来的值但size+了多次  
LinkedList同理，在判断是否是firstNode的时候会存在线程安全问题，在size++的时候也会存在线程安全问题  
线程安全的方式：1.创建集合或使用集合的时候用synchronized；2.用Collections.synchronizedList(list)  
### 2.红黑树  
特性：1.根节点是黑色；2.每个叶子节点都是黑色的空节点（NIL节点）；3.每个红色的节点的两个子节点都是黑色的（不能有两个连续的红色节点）；4.从任意节点到任意叶子节点每条路径黑色节点的数量必须一致  
红黑树添加元素分为三步：1.按照二叉树的方式添加元素；2.将新添加的节点变成红色（可以不违背黑色节点数量相同的原则）；3.如果不满足原则，进行旋转，变色达到平衡  
<br>
红黑树和avl树（平衡树）的区别：红黑树不是绝对的平衡，添加和删除快一点，旋转的次数少一点；查询多，添加删除少的话可以用avl树
<br>
顺便说下二叉树的缺点：元素按顺序添加很容易形成链表结构，不平衡
### 3.hashTable和concurrentHashMap哪里控制了线程安全?扩容的时候吗，还有呢？  
hashTable直接在几乎所有的方法上加了synchronized  

### 4.hashMap减少hash碰撞的方法？
### 5.concurrentHahshMap的size方法？不准确？怎么解决？


# 二、多线程
### 1.线程的几种创建方式
继承Thread类
实现Runnable接口
实现Callable接口
线程池

### 2.线程池的execute方法和submit方法的区别：
execute一般是用ThreadPoolExecutor的exectue方法，可接受runnable作为参数，没有返回值，不能捕获异常
submit一般是采用AbstractExecutorService的submit方法，可接受runnable或者callable作为参数，有返回值，可以捕获异常

### 3.线程池的实现原理
1.线程池和阻塞队列初始化，线程池初始化的时候，里面没有线程。  
2.当一个任务进来，判断当前正在运行的线程数是否小于核心线程数：小于的话，创建线程去执行任务；大于的话，就把任务放到阻塞队列去。  
3.当运行的线程执行完成之后，会去阻塞队列取任务。  
4.当核心线程都在运行，阻塞队列已经满了的情况下，会创建小于最大线程数的线程去执行不能放入阻塞队列的任务。  
5.当当前运行线程数大于最大线程数的时候，通过自定义的reject（）函数去处理新加的任务。  
6.当当前线程池的空闲线程大于核心线程的时候，会回收多于核心线程数的线程。  
秒杀场景会遇到这种情况  
<br>


# 三、Spring
## 1.IOC
* 控制反转：依赖对象的获取反转了。之前是程序在需要的时候主动创建依赖对象，现在是统一由spring容器来管理，程序被动的等待spring帮你注入。IOC很好的体现了面向对象的法则之一--好莱坞法则：别来找我们，我们去找你。  
* DI依赖注入：被注入对象依赖IOC容器注入所需外部资源。  
IOC是思想，DI是实现。  
<br>
* IOC的注入方式：构造器注入和setter注入  
<br>
* IOC在控制反转和依赖注入主要分为两个阶段：容器启动阶段和bean实例化阶段。  
容器启动阶段主要步骤是：加载配置信息，分析配置信息，装备到BeanDefinition，其他后处理。。。  
bean实例化阶段主要步骤是：实例化对象，装配依赖，生命周期回调，对象其他处理，注册回调接口。。。。  
<br>
* spring提供了两种容器：BeanFactory和ApplicationContext
BeanFactory是基础容器，默认使用延迟加载策略。当客户端对象需要访问容器管理的对象时才会去初始化实例。容器启动比较快，适用于系统资源有限，功能要求不是很严格的情况  
ApplicationContext是BeanFactory的子类，进行了功能扩展，支持事件发布，国际化等。默认在容器启动的时候就初始化所有的被容器管理的实例，所以容器初始化阶段会稍慢。适用于系统资源充足，要求更多功能的场景。   
<br>
* 容器初始化阶段详解：  
1.低级容器加载配置文件信息：spring的BeanDefinitionReader的子类会获取xml或者其他文件的配置信息并解析，然后将解析之后的bean的信息映射到BeanDefinition对象中，BeanFactory中的每个bean就是存储在BeanDefinition中。   
2.加载成功后，高级容器启动高级功能，如接口回调，事件发布，监听器，自动实例化单例等...   
3.初始化入口时refresh()方法，该方法会判断当前上下文是否存在容器，存在会销毁容器，确保重新创建的bean在新的容器中。  
4.容器内部维护了一个hashMap来存放每个bean的BeanDefinition，所有对bean的操作都是围绕这个hashMap来进行的。  
5.调用getBean()方法实例化bean，有依赖关系的递归调用。   
参考资料：https://www.cnblogs.com/ITtangtang/p/3978349.html    
<br>
<br>

## 2.AOP  
Spring AOP:面向切面编程，spring核心功能之一，实现原理是动态代理  
动态代理分为JDK动态代理和CJLIB动态代理：jdk动态代理要求被代理对象必须实现接口，CJLIB动态代理可以不实现接口。   
JDK动态代理：主要是通过invocationHandler 和 Proxy 类实现动态代理。需要有一个类实现InvocationHandler，并重写invoke方法，被代理对象作为属性，并在内部调用Proxy.newIntance创建出一个代理类，然后InvocationHandler的invoke方法内部通过代理类调用原方法，并在调用前后添加增强的逻辑。  
CJLIB动态代理：   



## 3.spring Boot  
starter-parent 定义了有各种版本号   

@SpringBootApplication注解  

run方法：创建内置tomcat等web容器，自动配置等等    



# 四、mysql
### mysql查询主要分为三个部分  
1.处理连接：服务端为每一个访问的客户端建立一个连接，接收服务端发来的文本文件  
2.解析与优化：缓存查询（取消了）；语法解析；查询优化  
3.存储引擎：innoDB（支持事务，外键，回滚）；myisam  


### 2.mysql索引失效的情况  
1.复合索引使用了非左边的索引  
2.like查询%放在了开头  
3.如果mysql估计根据索引查询比全表扫描慢，就不使用索引  
4.如果用or连接，or前面有索引，后面没有，则不使用索引  


<br>
<br>

# 五、dubbo  
### 1.常见的配置：  
service：服务的提供者；属性有：interface：接口全类名；ref：引用，配置为实现类上的名称；protocol：协议，常用dubbo；version：版本；timeout：超时时间；group：分组；    
reference:服务的消费者；属性有：id：使用的时候的名称；interface：接口全类名；retries：超时重试次数，默认2；timeout：超时时间；version：版本；group：分组，多实现的时候主要就是通过group来区分；  
application：应用配置；属性有：name，名称    
protocol：协议，由提供方指定，消费方被动接受；属性有：name：协议名称，常见dubbo；port：暴露的端口；    
registry：注册中心；属性有：protocol：协议类型，常见的有zookeeper；address：注册中心地址；  





