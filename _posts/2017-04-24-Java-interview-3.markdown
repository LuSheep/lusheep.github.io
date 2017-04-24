---
layout:     post
title:      "Java笔试面试题整理第三波"
subtitle:   "关于hashCode方法、Override和Overload、ArrayList Vector LinkedList、StringBuffer StringBuilder、Map Set List..."
date:       2017-04-24
author:     "Lusheep"
header-img: "img/post-first-blog-bg.jpg"
tags:
    - Java笔试面试题整理
---

> （本系列同步更新于 [CSDN博客](http://blog.csdn.net/shakespeare001)）
>
> 本系列整理Java相关的笔试面试知识点，其他几篇文章如下：
>
> [ ](http://blog.csdn.net/shakespeare001/article/details/51388516)[Java笔试面试题整理第八波](http://blog.csdn.net/shakespeare001/article/details/51669476)
>
>  [Java笔试面试题整理第七波](http://blog.csdn.net/shakespeare001/article/details/51388516)
>
> [ Java笔试面试题整理第六波](http://blog.csdn.net/shakespeare001/article/details/51330745)
>
>  [Java笔试面试题整理第五波](http://blog.csdn.net/shakespeare001/article/details/51321498)
>
> [ Java笔试面试题整理第四波](http://blog.csdn.net/shakespeare001/article/details/51274685)
>
> [ Java笔试面试题整理第三波](http://blog.csdn.net/shakespeare001/article/details/51247785)
>
> [ Java笔试面试题整理第二波](http://blog.csdn.net/shakespeare001/article/details/51200163)
>
> [ Java笔试面试题整理第一波](http://blog.csdn.net/shakespeare001/article/details/51151650)

# 1、String的split(String regex)方法参数注意点

使用这个方法时，当我们直接以“.”为参数时，是会出错的，如：

```java
1. String str = "12.03";  
2. String[] res = str.spilt(".");    //出错！！！
```

此时，我们得到的res是为空的（不是null），即str = [];

因为String的split(String regex)根据给定的<font color=red>正则表达式</font>的匹配来拆分此字符串，而<font color=red>"."是正则表达式中的关键字</font>，没有经过转义split会把它当作一个正则表达式来处理的，需要写成str.split("\\.")进行转义处理。

# 2、关于hashCode方法

​    【参考文章：

​          [http://www.cnblogs.com/dolphin0520/p/3681042.html](http://www.cnblogs.com/dolphin0520/p/3681042.html)

​    】

​    我们可以先通过HashMap中hashCode的作用来体验一下。

​    我们知道HashMap中是不允许插入重复元素的，如果是插入的同一个元素，会将前面的元素给覆盖掉，那势必在HashMap的put方法里对key值进行了判断，检测其是否是同一个对象。其put源码如下：    

```java
1. public V put(K key, V value) {  
2.       if (table == EMPTY_TABLE) {    //key的hashCode值放在了table里面  
3.           inflateTable(threshold);  
4.       }  
5.       if (key == null)  
6.           return putForNullKey(value);  
7.       int hash = hash(key);    //计算我们传进来的key的hashcode值  
8.       int i = indexFor(hash, table.length);  
9.       for (Entry<K,V> e = table[i]; e != null; e = e.next) {  
10.           Object k;  
11.           if (e.hash == hash && ((k = e.key) == key || key.equals(k))) {    //将传进来的key的hashcode值于HashMap中的table里面存放的hashCode值比较  
12.               V oldValue = e.value;  
13.               e.value = value;  
14.               e.recordAccess(this);  
15.               return oldValue;  
16.           }  
17.       }  
18.       modCount++;  
19.       addEntry(hash, key, value, i);  
20.       return null;  
21.   }  
```

​    可以看到这里的判断语句 if (e.hash == hash <font color=red>&&</font> ((k = e.key) == key || key.equals(k)))，里面通过&&逻辑运算符相连，先判断e.hash == hash，即判断传进来的key的hashCode值与table中的已有的hashCode值比较，如果不存在该key值，也就不会再去执行&&后面的equals判断；当已经存在该key值时，再调用equals方法再次确定两个key值对象是否相同。<font color=red>从这里可以看出，hashCode方法的存在是为了减少equals方法的调用次数，从而提高程序效率</font>。

​    可以看到，<font color=red>判断两个对象是否相同，还是要取决于equals方法</font>，而<font color=red>两个对象的hashCode值是否相等是两个对象是否相同的必要条件</font>。所以有以下结论：

​    （1）如果两个对象的hashCode值<font color=red>不等</font>，根据必要条件理论，那么这两个对象<font color=red>一定不是</font>同一个对象，即他们的equals方法一定要返回false；

​    （2）如果两个对象的hashCode值<font color=red>相等</font>，这两个对象也不一定是同一个对象，即他们的equals方法返回值不确定；

​    反过来，

​    （1）如果equals方法返回true，即是同一个对象，它们的hashCode值一定相等；

​    （2）如果equals方法返回false，hashCode值也不一定不相等，即是不确定的；

（hashCode返回的值一般是对象的存储地址或者与对象存储地址相关联的hash散列值）

然而，<font color=red>很多时候我们可能会重写equals方法，来判断这两个对象是否相等，此时，为了保证满足上面的结论，即满足hashCode值相等是equals返回true的必要条件，我们也需要重写hashCode方法，以保证判断两个对象的逻辑一致（所谓的逻辑一致，是指equals和hashCode方法都是用来判断对象是否相等）</font>。如下例子：

```java
1. public class Person {  
2.     private String name;  
3.     private int age;  
4.     public Person(String name,int age){  
5.         this.name = name;  
6.         this.age = age;  
7.     }  
8.     public String getName() {  
9.         return name;  
10.     }  
11.     public void setName(String name) {  
12.         this.name = name;  
13.     }  
14.     public int getAge() {  
15.         return age;  
16.     }  
17.     public void setAge(int age) {  
18.         this.age = age;  
19.     }  
20.   
21.     @Override  
22.     public boolean equals(Object obj) {  
23.         return this.name.equals(((Person)obj).name) && this.age== ((Person)obj).age;  
24.     }  
25. }  
```

在Person里面重写了equals方法，但是没有重写hashCode方法，如果就我们平时正常来使用的话也不会出什么问题，如：    

```java
1. Person p1 = new Person("lly",18);  
2. Person p2 = new Person("lly",18);  
3. System.out.println(p1.equals(p2));    //返回true  
```

上面是按照了我们重写的equals方法，返回了我们想要的值。但是当我们使用HashMap来保存Person对象的时候就会出问题了，如下：        

```java
1. Person p1 = new Person("lly", 18);  
2. System.out.println(p1.hashCode());  
3. HashMap<Person, Integer> hashMap = new HashMap<Person, Integer>();  
4. hashMap.put(p1, 1);  
5. System.out.println(hashMap.get(new Person("lly", 18))); //此时返回了null，没有按我们的意愿返回1  
```

​    这是因为，我们没有重写Person的hashCode方法，使hashCode方法与我们equals方法的逻辑功能一致，此时的Person对象调用的hashCode方法还是父类的默认实现，即返回的是和对象内存地址相关的int值，这个时候，p1对象和new Person("lly",18);对象因为<font color=red>内存地址不一致，所以其hashCode返回值也是不同的。故HashMap会认为这是两个不同的key，故返回null</font>。

​    所以，我们想要正确的结果，只需要重写hashCode方法，让equals方法和hashCode方法始终在逻辑上保持一致性。

在《[Java](http://lib.csdn.net/base/javase)编程思想》一书中的P495页有如下的一段话：

　　“设计hashCode()时最重要的因素就是：无论何时，对同一个对象调用hashCode()都应该产生同样的值。如果在将一个对象用put()添加进HashMap时产生一个hashCdoe值，而用get()取出时却产生了另一个hashCode值，那么就无法获取该对象了。所以如果你的hashCode方法依赖于对象中易变的数据，用户就要当心了，因为此数据发生变化时，hashCode()方法就会生成一个不同的散列码”。

​    如下一个例子：

```java
1. public class Person {  
2.     private String name;  
3.     private int age;  
4.     public Person(String name,int age){  
5.         this.name = name;  
6.         this.age = age;  
7.     }  
8.     public String getName() {  
9.         return name;  
10.     }  
11.     public void setName(String name) {  
12.         this.name = name;  
13.     }  
14.     public int getAge() {  
15.         return age;  
16.     }  
17.     public void setAge(int age) {  
18.         this.age = age;  
19.     }  
20.     @Override  
21.     public int hashCode() {  
22.         return name.hashCode()*37+age;    //hashCode的返回值依赖于对象中的易变数据  
23.     }  
24.     @Override  
25.     public boolean equals(Object obj) {  
26.         return this.name.equals(((Person)obj).name) && this.age== ((Person)obj).age;  
27.     }  
28. }  
```

此时我们继续测试：        

```java
1. Person p1 = new Person("lly", 18);  
2. System.out.println(p1.hashCode());  
3. HashMap<Person, Integer> hashMap = new HashMap<Person, Integer>();  
4. hashMap.put(p1, 1);  
5. p1.setAge(13);//改变依赖的一个值  
6. System.out.println(hashMap.get(p1));    //此时还是返回为null,这是因为我们p1的hashCode值已经改变了  
```

所以，在设计hashCode方法和equals方法的时候，如果对象中的数据易变，则最好在hashCode方法中不要依赖于该字段。

# 3、Override和Overload的区别

Override（重写）:    

​    在<font color=red>子类</font>中定义与<font color=red>父类</font>具有<font color=red>完全相同</font>的名称和参数的方法，通过子类创建的实例对象调用这个方法时，将调用子类中的定义方法，这相当于把父类中定义的那个完全相同的方法给覆盖了，是子类与父类之间多态性的一种体现。特点如下：

  （1）<font color=red>子类方法的访问权限只能比父类的更大，不能更小（可以相同）</font>；

  （2）<font color=red>如果父类的方法是private类型，那么，子类则不存在覆盖的限制，相当于子类中增加了一个全新的方法</font>；

  （3）子类覆盖的方法所抛出的异常必须和父类被覆盖方法的所抛出的异常一致，或者是其子类；即子类的异常要少于父类被覆盖方法的异常；

Overload（重载）:

​    （参考文章：[http://developer.51cto.com/art/201106/266705.htm](http://developer.51cto.com/art/201106/266705.htm)）  

  <font color=red>同一个类</font>中可以有多个名称相同的方法，但方法的<font color=red>参数个数</font>和<font color=red>参数类型或者参数顺序</font>不同；

​    关于重载函数<font color=red>返回类型能否不一样</font>，需分情况：

（1）<font color=red>如果几个Overloaded的方法的参数列表不一样（个数或类型），它们的返回者类型当然也可以不一样；</font>

（2）<font color=red>两个方法的参数列表完全一样，则不能通过让其返回类型的不同来实现重载。</font>

（3）<font color=red>不同的参数顺序也是可以实现重载的</font>；如下：    

```java
1. public String getName(String str,int i){  
2.      return null;  
3.  }  
4.  public String getName(int i,String str){  
5.      return null;  
6.  }  
```

​    我们可以用反证法来说明这个问题，因为我们有时候调用一个方法时也可以不定义返回结果变量，即不要关心其返回结果，例如，我们调用map.remove(key)方法时，虽然remove方法有返回值，但是我们通常都不会定义接收返回结果的变量，这时候假设该类中有两个名称和参数列表完全相同的方法，仅仅是返回类型不同，java就无法确定编程者倒底是想调用哪个方法了，因为它无法通过返回结果类型来判断。

​    所以，Overloaded重载的方法是可以改变返回值的类型；只能通过<font color=red>不同的参数个数、不同的参数类型、不同的参数顺序</font>来实现重载。

# 4、ArrayList、Vector、LinkedList区别

​    ArrayList、Vector、LinkedList都实现了List接口，其关系图如下：

![img](http://img.blog.csdn.net/20160426092919035?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

三者都可以添加<font color=red>null</font>元素对象，如下示例：    

```java
1. ArrayList<String> arrayList = new ArrayList<String>();  
2. arrayList.add(null);  
3. arrayList.add(null);  
4. System.out.println(arrayList.size());    //输出为2  
5.   
6. LinkedList<String> linkedList = new LinkedList<String>();  
7. linkedList.add(null);  
8.   
9. Vector<String> vectorList = new Vector<String>();  
10. vectorList.add(null);  
```

**ArrayList和Vector相同点：**

ArrayList和Vector两者在功能上基本完全相同，<font color=red>其底层都是通过new出的Object[]数组实现</font>。所以当我们能够<font color=red>预估到数组大小的时候，我们可以指定数组初始化的大小</font>，这样可以减少后期动态扩充数组大小带来的消耗。如下：

ArrayList<String> list= new ArrayList<String>(20);

Vector<String> list2 = new Vector<String>(15);

由于这两者的<font color=red>数据结构为数组，所以在获取数据方面即get()的时候比较高效</font>，而在<font color=red>add()插入或者remove()的时候，由于需要移动元素，效率相对不高</font>。（其实对于我们平常使用来说，由于一般使用add(String element)都是让其加在数组末尾，所以并不需要移动元素，效率还是很好的，如果使用add(int index, String element)指定了插入位置，此时就需要移动元素了。）

**ArrayList和Vector区别：**

ArrayList的所有方法都不是同步的，而Vector的大部分方法都加了synchronized同步，所以，就线程安全来说，<font color=red>ArrayList不是线程安全的，而Vector是线程安全的</font>，也因此Vector效率方面相较ArrayList就会<font color=red>更低</font>，所以如果我们本身程序就是安全的，ArrayList是更好的选择。

大多数的Java程序员使用ArrayList而不是Vector,因为同步完全可以由程序员自己来控制。

**LinkedList:**

LinkedList其<font color=red>底层是通过双向循环链表</font>实现的，所以在<font color=red>大量增加或删除元素时（即add和remove操作），由于不需要移动元素有更好的性能</font>，但是在获取数据（get操作）方面要差。

所以，在三者的使用选择上，<font color=red>LinkedList适合于有大量的增加/删除操作和较少随机读取操作</font>，ArrayList适合于<font color=red>大规模随机读取数据，而较少插入和删除元素情景下使用</font>，Vector在要求线程安全的情况下使用。

# 5、String、StringBuffer、StringBuilder区别

**String（since JDK1.0）：**

​    字符串常量，不可更改，因为其内部定义的是一个final类型的数组来保存值的，如下：

> private final char value[];

​    所以，当我们每次去“更改”String变量的值的时候（包括重新赋值或者使用String内部的一些方法），其实是重新新建了一个String对象（new String）来保存新的值，然后让我们的变量指向新的对象。因此，当<font color=red>我们需要频繁改变字符串的时候，使用String会带来较大的开销</font>。

定义String的方法有两种：

（1）String str = "abc";

（2）String str2 = new String("def");

第一种方式创建的String对象“abc”是存放在<font color=red>字符串常量池</font>中，创建过程是，首先在字符串常量池中查找有没有"abc"对象，如果有则将str直接指向它，如果没有就在字符串常量池中创建出来“abc”，然后在将str指向它。当有另一个String变量被赋值为abc时，直接将字符串常量池中的地址给它。如下：        

```java
1. String a = "abc";  
2. String b = "abc";  
3. System.out.println(a == b);    //打印 true  
```

也就是说通过第一种方式创建的字符串在字符串常量池中，是可共享的。同时，也是不可更改的，体现在：     

```java
1. String a = "abc";  
2. String b = "abc";  
3. b = b + "def";  
```

此时，字符串常量池中存在了两个对象“abc”和“abcdef”。

第二种创建方式其实分为两步：    

1. String s = "def";      
2. String str2 = new String(s);  

第一步就是上面的第一种情况；第二步在堆内存中new出一个String对象，<font color=red>将str2指向该堆内存地址，新new出的String对象内容，是在字符串常量池中找到的或创建出“def”对象，相当于此时存在两份“def”对象拷贝，一份存在字符串常量池中，一份被堆内存的String对象私有化管理着。所以使用String str2 = new String("def");这种方式创建对象，实际上创建了两个对象。</font>

**StringBuffer（since JDK1.0）和StringBuilder（since JDK1.5）：**

​    StringBuffer和StringBuilder在功能上基本完全相同，它们都继承自AbstractStringBuilder，而AbstractStringBuilder是使用非final修饰的字符数组实现的，如：char[] value;  ，所以，可以对StringBuffer和StringBuilder对象进行改变，每次改变还是再原来的对象上发生的，不会重新new出新的StringBuffer或StringBuilder对象来。所以，<font color=red>当我们需要频繁修改字符串内容的时候，使用StringBuffer和StringBuilder是很好地选择。</font>

​    两者的核心操作都是append和insert，append是直接在字符串的末尾追加，而insert(int index,String str)是在指定位置出插入字符串。 <font color=red>StringBuffer和StringBuilder的最主要区别就是线程安全方面，由于在StringBuffer内大部分方法都添加了synchronized同步，所以StringBuffer是线程安全的，而StringBuilder不是线程安全的</font>。因此，当我们<font color=red>处于多线程的环境下时，我们需要使用StringBuffer，如果我们的程序是线程安全的使用StringBuilder在性能上就会更优一点。</font>

​    三者的效率比较：

​    如上所述，

​    （1）当我们需要频繁的对字符串进行更改的时候，使用 StringBuffer或StringBuilder是优先选择，对于 StringBuffer和StringBuilder来说，只要程序是线程安全的，我们尽量使用StringBuilder来处理，要求线程安全的话只能使用StringBuffer。平常情况下使用字符串（不常更改字符串内容），String可以满足需求。

​    （2）有一种情况下使用String和 StringBuffer或StringBuilder的效率是差不多的，如下：        

```java
1. String a = "abc" + "def";    //速度很快  
2. StringBuilder sb = new StringBuilder();  
3. sb.append("abc").append("def");  
```

​    对于第一条语句，Java在编译的时候直接把a编译成 a = "abcdef"，但是当我们拼接的字符串是其他已定义的字符串对象时，就不会自动编译了，如下：        

```java
1. String a = "abc";  
2.         String b = "def";  
3.         String c = a + b;  
```

根据String源码中的解释，这种情况下是使用concatenation操作符（+），<font color=red>内部是新创建StringBuffer或StringBuilder对象，利用其append方法进行字符串追加，然后利用toString方法返回String串</font>。所以此时的效率也是不高的。

# 6、Map、Set、List、Queue、Stack的特点与用法

Java集合类用来保存<font color=red>对象集合</font>，对于基本类型，必须要使用其包装类型。Java集合框架分为两种：

（1）Collection

​    以单个对象的形式保存，其关系图如下：

​    ![img](http://img.blog.csdn.net/20160426093713180?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

其中，Statck类为Vector的子类。由于Collection类继承Iterable类，所以，所有Collection的实现类都可以通过foreach的方式进行遍历。

（2）Map

​    以<key,value>键值对的形式保存数据。Map常用类的结构关系如下：

![img](http://img.blog.csdn.net/20160426093814600?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

**List：**

​    List集合里面存放的元素<font color=red>有序、可重复</font>。List集合的有序体现在它默认是按照我们的添加顺序设置索引值（即我们可以通过get(索引值index)的方式获取对象）；可重复，是由于我们给每个元素设置了索引值，可以通过索引值找到相应的对象。

​    关于List集合下的具体实现类ArrayList、Vector、LinkedList可以参考上面的第四点总结。对于Statck，它是Vector的子类，模拟了栈后进先出的数据结构。

**Queue：**

​    接口，模拟了队列先进先出的数据结构。

**Set：**

​    Set里面的元素<font color=red>无序、不可重复</font>。由于无序性，我们不能通过get方式获取对象（因为set没有索引值）。如下：        

```java
1. Set<String> set = new HashSet<String>();  
2. set.add("ddd");  
3. set.add("4444");  
4. set.add("555");  
5. set.add("777");  
6. for(String s : set){  
7.    System.out.println(s);  
8. }  
```

​    打印结果如下：

​     	ddd

​        777

​        4444

​        555

​    而对于不可重复性，Set的所有具体实现类其内部都是通过Map的实现类来保存对象的。如HashSet内部就是通过HashMap来保存数据的，如下源码：     

```java
1. // Dummy value to associate with an Object in the backing Map--一个没有实际意义的Object对象  
2.  private static final Object PRESENT = new Object();      
3.  public HashSet() {  
4.      map = new HashMap<>();  
5.  }  
6.  public boolean add(E e) {  
7.      return map.put(e, PRESENT)==null;  
8.  }  
```

​    可以看到，new出一个HashSet的时候，里面new出了一个HashMap对象，在使用HashSet进行add添加数据的时候，<font color=red>HashSet将我们需要保存的数据作为HashMap 的key值保存了起来，而key值是不允许重复的，相当于HashSet的元素也是不可重复的</font>。

**Map：**

​    Map里面的元素通过<key,value>键值对的形式保存，不允许重复（具体分析可参考第二点总结内容）。其实Map<key,value>有点像Java中的Model对象类，如下使用：    

```java
1. class Person{  
2.     private String name;  
3.     private int age;  
4.     //set、get方法省略  
5. }  
6. List<Person> persons ;  
7. //也可使用如下方式  
8. List<Map<String,String>> persons ;   
9. Map<String,String> map = new HashMap<String,String>();  
10. map.put("name","lly");  
11. map.put("age","18");  
12. persons .add(map);  
```

