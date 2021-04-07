

# Java复习

### 初识Java

JVM----java虚拟机----与操作系统进行交互

JRE----java运行环境（包含JVM）

JDK----java开发工具包（包含JRE）



### 标识符规则

标识符必须以字母、下划线、$符号开头

其他部分可以是字母、下划线、$、数字

不可是java关键字



### java关键字

| **abstract** | continue | for        | new       | switch       |
| ------------ | -------- | ---------- | --------- | ------------ |
| assert       | default  | goto       | package   | synchronized |
| bololean     | do       | if         | private   | this         |
| break        | double   | implements | protected | throw        |
| byte         | else     | import     | public    | throws       |
| case         | enum     | instanceof | return    | transient    |
| catch        | extends  | int        | short     | try          |
| char         | final    | interface  | static    | void         |
| class        | finally  | long       | strictfp  | volatile     |
| const        | float    | native     | super     | while        |

### 变量类型

- 局部变量：方法或语句块内部定义的变量，生命周期是从声明位置开始到方法或语句块执行完毕为止。
- 成员变量：方法外部、类的内部定义的变量，从属于对象，生命周期伴随对象始终。如果不进行初始化，会自动初始化成该类型的默认初始值。
- 静态变量：使用static定义，从属于类，生命周期伴随类的始终，从类加载到卸载。

### 3类8种基本数据类型（引用数据类型都4字节）

- 数值型：
  - 整数型：byte（1字节）、short（2字节）、int（4字节）、long（8字节）
  - 浮点型：float（4字节）、double（默认）（8字节）
- 字符型：char（2字节）
- 布尔型：boolean（1位）(只表示true和false，没有0的概念)
- 注：浮点数比整型大，不需要强转

### 运算符

1. 如果两个操作数有一个为long类型，则结果也为long
2. 没有long时结果为int，即使操作数全是short，byte类型时，结果也是int
3. 如果两个操作数有一个是double，则结果也是double
4. 只有两个操作数都是float，结果才为float
5. 取余运算结果符号为左边操作数符号，例：7%-3=1,-7%3=-1
6. 位运算符：<<左移运算符，相当于乘2,>>右移运算符，相当于除2
7. &&优先级高于||

### break与continue区别

- break:强行退出循环，不执行循环中剩余语句
- continue:终止某次循环过程，跳过循环体中尚未执行的语句，接着进行下一次循环是否运行的判断

### 方法

注意事项：

1. 实参的数目、数据类型、次序必须喝所掉用的方法生命的形参列表匹配
2. return语句终止方法的运行并指定返回的数据
3. java中进行方法调用中传递参数时，遵循值传递的原则（传递的数据副本）
4. 基本数据类型传递的是该数据的拷贝值
5. 引用类型传递的是该对象引用的copy值，但指向的同一个对象



### 面向对象

#### **内存分析：**

java虚拟机的内存分为栈stack、堆heap、方法区method area

#### **栈：**

1. 描述的是方法执行的内存模型。每个方法被调用都会创建一个栈帧（存储局部变量、操作数、方法出口等）
2. JVM为每个线程创建一个栈，用于存放该线程执行方法的信息（实际参数、局部变量等）
3. 栈属于线程私有，不能实现线程间的共享
4. 栈的内存特性是“先进后出，后进先出”
5. 栈由系统自动分配，速度快，是一个连续的内存空间

#### **堆：**

1. 堆用于存储创建好的对象和数组（数组也是对象）
2. JVM只有一个堆，被所有线程共享
3. 堆是一个不连续的空间，分配灵活，速度慢

#### **方法区（又叫静态区）：**

1. JVM只有一个方法区，被所有线程共享
2. 方法区实际也是堆，只是用于存储类、常量相关的信息
3. 用来存放程序中永远是不变或唯一的内容。（类信息、静态变量、字符串常量等）

![](/home/victor/learn/学习总结/java内存分析.png)

#### **构造器**

1. 通过new调用
2. 构造器虽然有返回值，但是不能定义返回值类型（返回值的类型肯定是本类），不能在构造器使用return返回某个值
3. 如果没有定义构造器，编译器自动定义一个无参构造，如果定义了，就不会自动添加了
4. 构造器方法必须与类名一致
5. 第一行总是super()方法;
6. 可以在第一行使用this调用本类其他构造器，但是super与this只能显示调用一个

#### **垃圾回收机制**

1. 发现无用对象

2. 回收无用对象占用的存储空间

   算法：

   - 引用记数法，缺点：循环引用
   - 引用可达法（常用）

#### **分代垃圾回收机制**

1. 年轻代
2. 年老代
3. 持久代

JVM将内存分为Eden、Survivor、Tenured/Old三个空间，过程：

1. 新创建的对象，绝大多数都会存放到Eden中
2. 当Eden满了（达到一定比例）不能创建新对象，则会触发垃圾回收（GC），将无用对象清理掉，然后剩余对象复制到某个Survivor中，如S1,同时清空Eden区
3. 当Eden再次满了，会将S1中的不能清空的对象存到另外一个Survivor中，如S2,同时将Eden中的不能清空的对象，也复制到S2中，保证Eden和S1,均被清空（同一时刻，Survivor中的区域只有一个在用）
4. 重复多次（默认15次）Survivor中没有被清理的对象，则会复制到年老代Old（Tenured）区中
5. 当Old（Tenured）区满了，则会触发一个一次完整的垃圾回收（FullGC），之前新生代的垃圾回收称为（Minor GC）

- Minor GC：用于清理年轻代区域
- Major GC：用于清理了；老年代区域
- Full GC：用于清理年轻与老年，成本高，会对系统性能产生影响

#### **JVM调优和Full GC**

在对JVM调优的过程中，很大一部分就是对Full GC的调节，如下可能导致Full GC

1. 老年代（Tenured）被写满
2. 持久代（Perm）被写满
3. System.gc()被显示调用（建议程序启动GC启动，发送请求，并不是调用GC）
4. 上一次GC之后堆（Heap）的各区域分配策略动态变化

#### 创建对象的过程

1. 分配对象空间，并将对象成员变量初始化为0或Null
2. 执行属性值得显式初始化
3. 执行构造方法
4. 返回对象的地址给相关的变量

#### 对象的内存空间分布

1. 对象头：包含着对象自身的运行时数据，如HashCode、GC分代年龄等，类型指针，若果是数组，对象头还会有一块区域记录数组长度
2. 实例数据：java中定义的各种字段内容
3. 对其填充：起占位符的作用，保证对象只占有8个字节整数倍的空间

#### this的本质

本质就是“创建好的对象的地址”！由于在构造方法调用前，对象已经创建。因此，在构造方法中也可以使用this代表“当前对象”。

#### static

在类中，用static声明的成员变量为静态成员变量，也称为类变量。类变量的生命周期和类相同，在整个程序执行期间都有效

#### 值传递

- 基本数据类型参数的传值：传递的是值的副本，副本改变不会影响原件
- 引用类型参数的传值：传递的是值得副本，但是引用类型指的是“对象地址”。因此，副本和原参数都指向了同一个“地址”，改变“副本指向的地址的值，也意味着原参数指向对象的值也发生了改变”

#### instanceOf

查看左边是否为右边的子类

#### 方法重写

1. 方法名，参数列表相同
2. 返回数据类型
   1. 父类返回值是void，子类必须void
   2. 父类返回值类型是A类，则子类返回值类型是A类也可以是A类的子类
   3. 父类返回值类型为基本数据类型，则子类也是这个基本数据类型
3. 访问权限，子类大于等于父类，前提父类不是private，因为private在子类中看不见
4. 异常类型，子类重写的抛出异常类型不大于父类抛出的异常类型

#### ==与equals

- ==：代表比较双方是否相同，如果是基本类型则表示值相等，如果是引用类型则表示地址相等即是同一个对象
- equals：是一个方法，提供定义“对象内容相等”的逻辑
- String、Date、File重写了equals方法，使得只比较内容 

#### 访问权限控制符

|  修饰符   | 同一个类 | 同一个包 | 子类 | 所有类 |
| :-------: | :------: | :------: | :--: | :----: |
|  private  |    *     |          |      |        |
|  default  |    *     |    *     |      |        |
| protected |    *     |    *     |  *   |        |
|  public   |    *     |    *     |  *   |   *    |

#### 多态

1. 多态是方法的多态，不是属性的多态（多态与属性无关）
2. 多态的存在要有三个必要条件：继承、方法重写、父类引用指向子类对象
3. 父类引用指向子类对象后，用该父类引用调用子类重写的方法，此时出现多态
4. 调用父类同名方法时，调用的子类重写后的方法。且只能调用左边声明类型中的方法



#### final

1. 修饰变量：
   1. 成员变量：只能显示初始化、代码块中初始化、构造器中初始化，只可赋值一次
   2. 局部变量：被修饰的局部变量变成常量，不可被更改
2. 修饰方法：该方法不可被子类重写，可以重载
3. 修饰类：类不能被继承

#### 抽象类（含有构造器）

1. 有抽象方法的类一定是抽象类，但是抽象类里面可以定义普通方法，有构造器

2. 抽象类不可以被实例化

3. 意义：为子类设计模板，子类必须实现

   注：如果子类不是必须实现父抽象类的所有抽象方法，则这个子类是抽象类。不能与private、static、final一同修饰方法

#### 接口（没有构造器）

jdk7：只能定义全局常量（public static final）、抽象方法（public abstract）

jdk8：除了上面两个还可以定义静态方法、默认方法

注：如果没有实现全部的抽象方法，则这个类是抽象类 。接口与接口之间可以继承，而且是可以多继承

#### 内部类

1. **成员内部类：**
   - **非静态内部类：**
     - **独立生成class文件**
     - **寄存在外部类对象里，因此，如果有一个非静态内部类对象那么一定存在对应的外部类对象。非静态内部类对象单独属于外部类的某个对象**
     - **非静态内部类可以直接访问外部类的成员，但是外部类不能直接访问非静态内部类成员**
     - **不能含有静态方法，静态属性，静态初始化块**
     - **外部类的静态方法、静态代码块不能访问非静态内部类，包括不能使用非静态内部类定义变量、创建实例**
   - **静态内部类：看做外部类的一个静态成员，因此，外部类的方法可以通过：静态内部类的名字直接点出来，通过new静态内部类（）访问静态内部类的实例**
   
2. **匿名内部类：**
   - **使用场景：适合那种只需要使用一次的类**
   - **没有访问修饰符**
   - **没有构造方法，因为连名字都没有**
   
3. **局部内部类：作用域仅在方法中使用**

   **注：在局部内部类中的一个方法要是调用了这个局部内部类中的成员变量时，这个成员变量需要是final的，jdk8以后默认就是final，可以省略final关键字**

#### String方法

- charAt()提取下标相应的字符
- equalsIngnoreCase()比较字符串忽略大小写
- indexOf()字符串是否包含某个字符串，如果有返回下标位置，没有返回-1
- replace(' ','#')空格换成#
- startsWith("")是否以某个字符串开头，endsWith()同理
- substring()提取字符串，截取从某个下标开始到结尾
- toLowerCase()，toUpperCase()转换大小写
- trim()去除字符串首尾的空格
- intern（）不管是什么此字符串就在常量池中

![image-20201102084702201](/home/victor/.config/Typora/typora-user-images/image-20201102084702201.png)

~~~~java
String str1 = "hello";
String str2 = "helloworld";
String str3 = str1 + "world";
System.out.println(str2 == str3);//false

final String str4 = "hello";
String str5 = str4 + "world";
System.out.println(str2 == str5);//true
~~~~



#### Arrays工具类使用

- Arrays.toString()转换成数组字符串   deeptoString()
- Arrays.sort()从小到大排序
- Arrays.binarySearch()查找字符所在的第一个位置

#### 包装类

| 基本数据类型 | 包装类  | 基本数据类型 | 包装类    |
| ------------ | ------- | ------------ | --------- |
| byte         | Byte    | boolean      | Boolean   |
| short        | Short   | char         | Character |
| int          | Integer | long         | Long      |
|              | Float   | double       | Double    |

注：对于Integer类型，如果值在-128-127之间调用valueOf()则取的是同一个对象(有缓存,就是-128-127的数组)，有一个内部类IntegerCache，把-128-127都放在数组里，用的时候取，就不需要new了，前提是使用自动装箱或ValueOf()方法。

Integer与int比Integer会自动拆箱所以一直相等

~~~java
Integer integer1 = new Integer(2);
Integer integer2 = new Integer(2);
Integer integer3 = 2;
Integer integer4 = Integer.valueOf(2);
int ee = 2;
System.out.println(integer1 == integer2);//false
System.out.println(integer2 == integer3);//false


System.out.println(integer4 == integer3);//true
System.out.println(integer4 == ee);//tru


Object o = true ? new Integer(1) : new Double(2.3);
System.out.println(o);//1.0
~~~



**String(不可变字符序列)**

~~~java
String s1="hello"+"java";
String s2="hellojava";
System.out.println(s1==s2);//true

String s3="hello";
String s4="java";
String s5=s3+s4;
System.out.println(s5==s2);//false
~~~

**StringBuilder(可变字符序列)**

空参构造就是创建一个长度为16的char数组

扩容默认是原来的2倍+2

特点：线程不安全，效率高，abstractStringBuilder的子类

~~~java
StringBuilder sBuilder=new StringBuilder("123456");
System.out.println(sBuilder);
sBuilder.setCharAt(0, '0');
System.out.println(sBuilder);
~~~

常用方法：

- append：在数组后面追加
- reverse：倒序
- insert：插入
- setCharAt：替换
- delete：删除区间

**StringBuffer(可变字符序列)**

特点：线程安全，效率低，abstractStringBuilder的子类

效率：StringBuilder>StringBuffer>String



#### 时间类

~~~java
System.out.println(System.currentTimeMillis());//打印1970年到现在的毫秒数
SimpleDateFormat sd = new SimpleDateFormat("yyyy-MM-dd");//定义时间格式
String s = sd.format(new Date(System.currentTimeMillis()));
Date d3 = sd.parse("2020-8-9");
System.out.println(d3);
System.out.println(s);

Date d1=new Date(300);
Date d2=new Date(400);
System.out.println(d2.after(d1));//true

SimpleDateFormat sd2 = new SimpleDateFormat("w");
System.out.println(sd2.format(new Date()));;//今年的第多少周
~~~



#### 日历类(Calendar)

#### 枚举

如果枚举类只有一个对象，则可以作为单例模式的实现方式。

定义的枚举类父类是java.lang.Enum不是Object

values方法：输出枚举类常量的数组

~~~java
 enum Season {
        SPRING,SUMMER,AUTIMN,WINTER//都是pulic static final
    }

    public static void main(String[] args) {
        System.out.println(Season.SPRING);
        Season season=Season.WINTER;

        switch (season){
            case SPRING:
                System.out.println("春天");break;
            case SUMMER:
                System.out.println("夏天");break;
            case AUTIMN:
                System.out.println("秋天");break;
            case WINTER:
                System.out.println("冬天");break;
        }

    }
~~~

比较机制：实现Comparable接口，重写ComparableTo方法。或者使用Compartor的实现类实现一次性比较，Arrays.sort(arr,Compartor)



### 异常机制

本质：当程序出现错误，程序安全退出机制

java采用面向对象的方式来处理异常，处理过程：

1. 抛出异常：在执行一个方法时，如果发生异常，则这个方法生成代表该异常的对象，停止当前执行路径，并把异常对象提交给JRE。
2. 捕获异常：JRE得到该异常后，寻找相应的代码来处理该异常。JRE在方法的调用栈中查找，从生成异常的方法开始回溯，知道找到相应的异常处理代码位置。

![](/home/victor/learn/学习总结/java异常继承关系.jpg)





**Error**

不需要程序员处理，虚拟机JVM处于不可恢复的崩溃状态

**Exception**

- RuntimeException运行时异常：通常是编程错误导致的，所以在编写程序时，并不要求必须使用异常处理机制来处理这类异常，经常需要增加“逻辑处理”来避免，例如：空指针异常、数组角标越界、类型转换异常、算数异常
- CheckedException编译时异常：这类异常在编译时就必须做出处理，否则无法通过编译。例如：IOException



catch中异常如果有子父类关系，则catch父类声明在下方，否则报错

如果父类中被重写的方法没有throws方式处理异常，则子类重写的方法 也不能使用throws，意味着如果子类重写的方法有异常，必须使用try的方式处理



**手动抛出异常**

1.  继承RuntimeException类
2. 写一个全局常量序列号
3. 构造器



### 容器

![/home/victor/learn/学习总结/java容器继承关系.jpg](/home/victor/learn/学习总结/java容器继承关系.jpg)

contains方法会调用对象的equals方法判断是否包含

#### List（可重复，有序）

都实现了list接口，继承自AbstractList

**ArrayList:**（线程不安全）

jdk7是new ArrayList时就创建一个长度为10的数组，而jdk8则是等到有数据添加了在创建一个10的数组，延迟创建节省内存

底层实现：数组

特点：查询效率高，增删效率低，线程不安全

**LinkedList:**（线程不安全）

底层实现：双向链表

特点：查询效率低，增删效率高，线程不安全

**Vector:**（线程安全）

底层实现：数组

效率低



**三者异同**

同：都实现了List接口，继承了AbstractList抽象类，都可以存储有序可重复的数据

异：同上



#### Set(不可重复，无序，null也只能一个)

都实现了set接口，继承自AbstractSet

**HashSet:**（线程不安全）

底层实现：hash表（数组+链表）HashMap

特点：线程不安全，存取速度快。

哈希表边存放的是哈希值。HashSet存储元素的顺序并不是按照存入时的顺序（和List显然不同） 是按照哈希值来存的所以取数据也是按照哈希值取得。

元素的哈希值是通过元素的hashcode方法通过Hash算法来获取的, HashSet首先判断两个元素的哈希值，如果哈希值一样，接着会比较equals方法 如果 equls结果为true ，HashSet就视为同一个元素。如果equals 为false就不是同一个元素。

**LinkedHashSet：**父类是HashSet

按照添加的顺序遍历，在添加的时候弄一个双向链表，所以可以按添加顺序遍历 。遍历效率高一些

**TreeSet：**（可以进行排序，线程不安全）需要添加同一类型对象，因为需要比较大小

有序，查询速度比List快

底层实现：红黑树的数据结构，TreeMap

特点：TreeSet是一个不同步的非线程安全的二叉树

既然TreeSet可以自然排序,那么TreeSet必定是有排序规则的。

方式一：元素自身具备比较性

元素自身具备比较性，需要元素实现Comparable接口，重写compareTo方法，也就是让元素自身具备比较性，这种方式叫做元素的自然排序也叫做默认排序。

方式二：容器具备比较性

当元素自身不具备比较性，或者自身具备的比较性不是所需要的。那么此时可以让容器自身具备。需要定义一个类实现接口Comparator，重写compare方法，并将该接口的子类实例对象作为参数传递给TreeMap集合的构造方法。



#### Map(k,v键值对)

都实现了map接口，继承自AbstractMap，AbstractMap父类为Object

**HashMap:**（效率高于TreeMap，线程不安全，效率高，允许key或value为null）

底层实现：hash表（数组+链表）

![](/home/victor/learn/学习总结/HashMap.jpg)

HashMap的位桶默认为16,就是数组的长度，可以扩容

当超出临界值(加载因子0.75*当前长度)时扩容为原来的二倍

HashMap在存储一个元素时，当对应链表长度大于8,链表转换为红黑树，大大提高查找效率

当数组的某一个索引位置上的元素以链表形式存在的数据个数 > 8且当前数组的长度 > 64时，此时这个索引位置上的所有数据改为红黑树存储



**TreeMap:**（按照key的递增排序）

底层实现：红黑树

~~~java
public class day6_treeMap {
    public static void main(String[] args) {
        emp emp1 = new emp(1, 200);
        emp emp2 = new emp(2, 300);
        emp emp3 = new emp(1, 100);
        emp emp4 = new emp(3, 400);
        emp emp5 = new emp(2, 100);

        TreeMap<emp, Integer> map = new TreeMap<>();
        map.put(emp1, 1);
        map.put(emp2, 2);
        map.put(emp3, 4);
        map.put(emp4, 2);
        map.put(emp5, 4);

        System.out.println(map);
    }

}


class emp implements Comparable<emp> {
    int id;
    double salary;

    public emp(int id, double salary) {
        this.id = id;
        this.salary = salary;
    }

    @Override
    public String toString() {
        return "emp{" +
                "id=" + id +
                ", salary=" + salary +
                '}';
    }

    @Override
    public int compareTo(emp o) {
        if (this.id > o.id) {
            return 1;
        } else if (this.id < o.id) {
            return -1;
        } else if (this.salary > o.salary) {
            return 1;
        } else {
            return -1;
        }
    }
}
~~~

**HashTable:**（线程安全，效率低。不允许key或value为null），父类是Dictionary



#### 迭代器

~~~java
public static void main(String[] args) {
        List<String> list = new ArrayList<>();
        list.add("1");
        list.add("2");

        for (Iterator<String> iterator=list.iterator();iterator.hasNext();){
            String temp= iterator.next();
            System.out.println(temp);
        }
    }
~~~



### 多线程

**并发：**指两个或多个事件在同一时段内发生

**并行：**指两个或多个事件在同一时刻发生（同时发生）

**进程：**指一个内存中运行的程序，每个进程都有一个独立的内存空间，一个应用程序可以同时运行多个进程；进程也是程序的一次执行过程，是系统运行的基本单位；系统运行一个程序即是一个进程从创建、运行到消亡的过程。

**线程：**线程是进程中的一个执行单元，负责当前进程中程序的执行，一个进程中至少有一个线程。一个进程中是可以有多个线程的，这个应用程序也可以称之为多线程程序。简而言之：一个程序运行后至少有一个进程，一个进程中可以包含多个线程。

#### **继承Thread类实现多线程程序**

1. 创建一个Thread类的子类
2. 在Thread子类中重写run方法，设置线程任务
3. 创建Thread类的子类对象
4. 调用Thread类中的start方法，开启线程，来执行run方法

注：多次启动一个线程是非法的，特别是当线程已经结束执行后，不能再重新启动。java程序属于抢占式调度，哪个线程优先级高，哪个优先执行。

~~~java
public class day7_thread01 extends Thread {
    @Override
    public void run() {
        for (int i = 0; i < 300; i++) {
            System.out.println("run" + i);
        }
    }
}

class test {
    public static void main(String[] args) {
        day7_thread01 thread01 = new day7_thread01();
        // thread01.run();如果这样执行就是单线程程序，只有一个栈空间，执行run
        thread01.start();//这样会开辟新的栈空间来执行run方法，多线程程序

        for (int i = 0; i < 300; i++) {
            System.out.println("main" + i);
        }
    }
}
~~~

**Thread类常用方法**

| 方法                   | 作用                                 |
| ---------------------- | ------------------------------------ |
| Thread.currentThread() | 获取当前线程，是静态方法，直接调用   |
| 线程.getName()         | 获取线程名称                         |
| 线程.setName()         | 给线程设置名称                       |
| 线程.sleep()           | 按毫秒数暂停，结束后继续运行         |
| 线程.yield()           | 释放当前cpu执行权限                  |
| thread1.join()         | 有异常，让thread1执行到结束          |
| 线程.stop()            | 强制线程生命周期结束，不推荐，已过时 |
| 线程.isAlive()         | 判断是否存活                         |
| 线程.sleep()           | 线程睡眠，有异常                     |

**线程的优先级**

分别有三个优先级：1、5、10

涉及的方法：

getPriority()

setPriority(int )



#### **实现Runnable接口实现多线程**

1. 创建Runnable接口的实现类

2. 重写run方法

3. 创建Runnable接口的实例对象

4. 创建Thread类对象，构造方法中传递Runnable接口的实现类对象

5. 调用start方法，开启新的线程，执行run方法

   注：一个线程对象只能调用一次start（）方法

~~~java
public class day7_thread02 implements Runnable {

    @Override
    public void run() {
        for (int i = 0; i < 500; i++) {
            System.out.println(Thread.currentThread().getName() + "---" + i);
        }
    }
}

class test2 {
    public static void main(String[] args) {
        day7_thread02 thread02 = new day7_thread02();
        Thread thread = new Thread(thread02);
        thread.start();
        
        
        Thread thread1 = new Thread(new Runnable() {//匿名内部类实现
            @Override
            public void run() {
                for (int i = 0; i < 500; i++) {
                    System.out.println(Thread.currentThread().getName() + "---" + i);
                }
            }
        });
        thread1.start();


        for (int i = 0; i < 500; i++) {
            System.out.println(Thread.currentThread().getName() + "---" + i);
        }
    }
}
~~~

#### 实现Runnable接口好处

1. 避免了单继承的局限性
2. 增强了程序的扩展性，降低了耦合性。设置线程任务与开启线程进行了分离
3. 更适合有数据共享的情况

#### 实现Callable接口实现创建多线程

~~~java
//1.创建一个Callable的实现类
public class ThreadNew implements Callable {

    //2.实现call方法
    @Override
    public Object call() throws Exception {
        int num = 0;
        for (int i = 0; i < 20; i++) {
            if(i % 2 == 0) {
                System.out.println(Thread.currentThread().getName()+"----"+i);
                num += i;
            }
        }
        return num;
    }
}

class ThreadNewTest {
    public static void main(String[] args) {
        //3.创建一个Callable的实现类对象
        ThreadNew threadNew = new ThreadNew();

        //4.将这个实现类对象作为参数传递给FutureTask构造器中
        FutureTask futureTask = new FutureTask(threadNew);

        //5.将这个FutureTask对象作为参数丢给Thread构造器中，开启线程
        new Thread(futureTask).start();

        try {
            //6.获取call方法中的返回值
            System.out.println("总和为："+futureTask.get());
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }
    }
}

//1.可以有返回值
//2.可以抛出异常，被外面的方法捕获
//3.支持泛型
~~~

#### 线程安全问题

让线程在访问共享数据的时候，无论是否失去了CPU执行权限，都让其他的线程等待，等待当前完成，其他线程在访问。

#### 解决线程安全问题

1. **同步代码块**：把会引起线程安全问题的代码放在同步代码块中

   **原理**：创建一个锁对象，假如线程0拿到了CPU资源，则执行0的run方法，拿走锁对象，当线程0睡眠时，线程1拿到CPU资源，可是发现没有锁对象，所以不能执行，处于阻塞状态。当线程0跑完run方法归还锁对象，其他的再争抢CPU资源运行

   任何一个对象都可以是锁对象 

   synchronized (o)   o为锁对象 ,锁在几个线程中必须只有一个锁对象，使用extends需要声明为static

   ~~~java
   Object o=new Object();
   
   @Override
   public void run() {
       synchronized (o){
           while (tickets > 0) {
               try {
                   Thread.sleep(100);
               } catch (InterruptedException e) {
                   e.printStackTrace();
               }
               System.out.println(Thread.currentThread().getName() + "---" + tickets);
               try {
                   Thread.sleep(100);
               } catch (InterruptedException e) {
                   e.printStackTrace();
               }
               tickets--;
           }
       }
   }
   ~~~

2. **同步方法**：把访问了共享数据的代码抽取出来，放到一个方法中，在这个方法上添加synchronized关键字

   **原理**：和第一种方法一样，里面的方法只能一个线程访问，锁对象就是实现了Runnable的这个类的对象，就是这里面的thread03这个对象，实现Runnable时使用

   ~~~java
    @Override
       public void run() {
           mai();
       }
   
   
       public synchronized void mai() {
           while (tickets > 0) {
               try {
                   Thread.sleep(100);
               } catch (InterruptedException e) {
                   e.printStackTrace();
               }
               System.out.println(Thread.currentThread().getName() + "---" + tickets);
               try {
                   Thread.sleep(100);
               } catch (InterruptedException e) {
                   e.printStackTrace();
               }
               tickets--;
           }
       }
   
   
   class test3 {
       public static void main(String[] args) {
           day7_thread03 thread03 = new day7_thread03();
           new Thread(thread03).start();
           new Thread(thread03).start();
           new Thread(thread03).start();
       }
   }
   ~~~

3. **静态方法**：把二的方法变成静态，和第二种方式是一样的

   **原理**：这个的锁对象就不是thread03了，因为静态属性属于类，而不是对象，锁对象产生的时候，thread03还没有产生，所以锁对象是本类，也就是实现Runnable接口的这个类.class，使用继承Thread时使用

   ~~~java
   private static int tickets = 100;
   
   
       @Override
       public void run() {
           mai();
       }
   
   
       public static synchronized void mai() {
           while (tickets > 0) {
               try {
                   Thread.sleep(100);
               } catch (InterruptedException e) {
                   e.printStackTrace();
               }
               System.out.println(Thread.currentThread().getName() + "---" + tickets);
               try {
                   Thread.sleep(100);
               } catch (InterruptedException e) {
                   e.printStackTrace();
               }
               tickets--;
           }
       }
   ~~~

4. **Lock锁**：new一个实例了Lock接口的ReentrantLock的对象，然后在出现线程安全问题的代码前面调用lock方法上锁，安全问题结束时调用unlock方法释放锁，lock需要手动加同步与释放

   **原理**：

   ~~~java
   private int tickets = 100;
   
   Lock lock=new ReentrantLock();
   
   @Override
   public void run() {
       lock.lock();
       while (tickets > 0) {
           try {
               Thread.sleep(100);
           } catch (InterruptedException e) {
               e.printStackTrace();
           }
           System.out.println(Thread.currentThread().getName() + "---" + tickets);
           try {
               Thread.sleep(100);
           } catch (InterruptedException e) {
               e.printStackTrace();
           }
           tickets--;
       }
       lock.unlock();
   }
   ~~~

#### 线程状态

![](/home/victor/learn/学习总结/线程状态图.jpg)

![image-20201031094921769](/home/victor/.config/Typora/typora-user-images/image-20201031094921769.png)

**等待唤醒**

~~~java
 public static void main(String[] args) {
        Object o = new Object();

        new Thread(new Runnable() {
            @Override
            public void run() {
                synchronized (o) {
                    System.out.println("需要包子");
                    try {
                        o.wait();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    System.out.println("开吃");
                }
            }
        }).start();


        new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    Thread.sleep(5000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                synchronized (o) {
                    System.out.println("做好包子了");
                    o.notify();
                }
            }
        }).start();
    }
~~~

#### 线程通信

**调用wait与notify方法的细节：**

1. wait与notify必须由同一个锁对象调用，因为对应的锁对象可以通过notify唤醒同一个锁对象调用的wait方法后的线程
2. wait与notify属于Object类的方法，因为锁对象可以是任意对象，而任意对象的所属类都继承自Object类
3. wait与notify必须要在同步代码块或者同步函数中使用，因为必须要通过锁对象调用这俩方法
4. 这俩方法调用者必须是同步代码块中的同步监视器

**sleep与wait的异同**

相同点：一旦执行方法，使当前线程进入阻塞状态

不同点：

1. sleep声明在Thread类中，wait声明在Object类中
2. sleep可以在任何场景中调用，wait只能在同步方法或同步代码块中调用
3. 如果两个方法都是用在同步代码块或同步方法中，sleep不会释放锁，wait释放锁

#### 线程池

**概念**：容纳多个线程的容器，其中的线程可以反复使用，省去了频繁创建现成的操作，无需消耗更多资源

JDK1.5内置线程池，不需要自己创建

1. 降低资源消耗。减少了创建和销毁线程的次数，每个工作线程都可以被重复利用，可执行多个任务。
2. 提高响应速度。当任务到达时，任务可不需要等到线程创建就能立即执行
3. 提高线程的可管理性。可以根据系统的承受能力，调整线程池中工作现成的数目，防止因为消耗过多的内存，把服务器搞崩溃。

**线程池使用步骤：**

1. 使用线程池的工厂类Executors里边提供的静态方法newFixedThreadPool生产一个指定的线程数量的线程池
2. 创建一个类，实现Runnable接口，重写run方法，设置线程任务
3. 调用ExecutorService中的方法submit，传递线程任务（实现类），开启线程，执行run方法
4. 调用ExecutorService中的方法shutdown销毁线程池（不建议执行）

~~~java
public class day8_ThreadPool {
    public static void main(String[] args) {
        ExecutorService executorService = Executors.newFixedThreadPool(3);
        executorService.execute(new RunnableImpl());
        executorService.execute(new RunnableImpl());
        executorService.execute(new RunnableImpl());//使用完自动归还
        
        executorService.shutdown();//关闭线程池
    }
}


class RunnableImpl implements Runnable{

    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName());
    }
}
~~~

### Lambda表达式

#### 格式：

- 一些参数

- 一个箭头

- 一段代码

  （）：接口中抽象方法的的参数列表，没有参数就空着，有多个用，分离

     -> ：传递，把参数传递给方法体

     {}  ：重写接口的抽象方法的方法体

  ~~~java
  new Thread(new Runnable() {
              @Override
              public void run() {
                  System.out.println(Thread.currentThread().getName());
              }
          }).start();
  
  
          new Thread(()->{
              System.out.println(Thread.currentThread().getName());
          }).start();
  ~~~

  ~~~java
  public class day9_lambada04 {
      public static void main(String[] args) {
  
          /*Calus(1, 3, new Calculator() {
              @Override
              public int caul(int a, int b) {
                  return a+b;
              }
          });*/
          
          Calus(1, 8, (int a, int b) -> a + b);
      }
      public static void Calus(int a, int b, Calculator calculator) {
          int caul = calculator.caul(a, b);
          System.out.println("结果是：" + caul);
      }
  }
  
  interface Calculator {
      int caul(int a, int b);
  }
  ~~~

#### 可省略的内容

1. （参数列表）：括号中参数列表的数据类型可以省略

2. （参数列表）：括号中的参数如果只有一个，那么类型和（）都可以省略

3. {一些代码}：如果{}中的代码只有一行，无论是否有返回值都可以省略{}和return和分号

   注：要省略{}，return，分号要一起省略

#### 使用前提

1. 使用lambda必须具有接口，且接口只有一个抽象方法
2. 使用lambda必须有上下文推断，也就是说：方法的参数或局部变量类型必须为Lambda对应的接口类型，才能使用Lambda作为该接口实例



### File类

#### **常用方法**

- 获取功能方法

  ~~~java
  File f=new File("a.txt");
  System.out.println("绝对路径："+f.getAbsolutePath());
  System.out.println("构造路径："+f.getPath());
  System.out.println("文件名："+f.getName());
  System.out.println("文件长度："+f.length()+"字节");
  ~~~

- 判断功能

  ~~~java
  File f1=new File("/home/victor/victor");
  System.out.println("是否存在："+f1.exists());
  System.out.println("是否是文件夹："+f1.isDirectory());
  System.out.println("是否是文件："+f1.isFile());
  ~~~

- 创建删除功能

  ~~~java
  f.createNewFile();//创建文件
  f.delete();//删除文件、文件夹
  f.mkdir();//创建文件表示的目录
  f.mkdirs();//递归创建目录
  ~~~

- 遍历

  ~~~java
  String[] list = f1.list();//获取目录中文件或文件夹的名称String数组
  for (String s : list) {
      System.out.println(s);
  }
  
  
  File[] files = f1.listFiles();//获取目录中文件或文件夹的File数组
  for (File file : files) {
      System.out.println(file);
  }
  ~~~

#### **文件过滤器**

- FileFilter(File pathname) 参数为文件File类型

- FileNameFilter(File dir,String name）dir为目录名称，name为名字

  可以使用new File(dir,name) 组合成为整个File

~~~java
public class demo03_file {
    public static void main(String[] args) {
        File victor = new File("/home/victor/victor");
        files(victor);
    }

    public static void files(File file) {
        for (File file1 : file.listFiles(new demo())) {//可以使用Lambda表达式优化，因为接口中只有一个抽象方法
            if (file1.isDirectory()) {
                files(file1);
            } else {
                System.out.println(file1.getName());
            }

        }
    }
}


class demo implements FileFilter {//实现接口，重写判断方法
    @Override
    public boolean accept(File pathname) {
        if (pathname.isDirectory()){
            return true;
        }
        return pathname.getName().endsWith(".html");
    }
}
~~~



### I/O流

|        | 输入流      | 输出流       |
| ------ | ----------- | ------------ |
| 字节流 | InputStream | OutputStream |
| 字符流 | Reader      | Writer       |

这四个类都是抽象类

#### 字节流

以File开头的流为节点流

**FileOutputStram**

~~~java
//不进行续写
{
FileOutputStream fos = new FileOutputStream("src/day09_IO/a.txt");
fos.write(98);
fos.write(49);
fos.write(48);
fos.write(48);

byte[] a={-12,-78,-23,-34,78};//两个负数会合并成为汉字
fos.write(a);


byte[] b={78,23,34};
fos.write(b,0,1);

byte[] bytes = "你好".getBytes();
fos.write(bytes);

fos.close();
}

//允许续写
FileOutputStream fos = new FileOutputStream("src/day09_IO/b.txt",true);
fos.write("你好我是victor\n".getBytes());//\n表示换行
fos.close();
~~~

**FileInputStream**

~~~java
//读取一个字节
public static void main(String[] args) throws IOException {
        FileInputStream fis = new FileInputStream("src/day09_IO/b.txt");
        int len=0;
        while ((len=fis.read())!=-1){//内部指针会向后移，所以必须定义变量，不然缺少字节
            System.out.println(len);
        }

        fis.close();
    }

//读取到字节数组
public static void main(String[] args) throws IOException {
        FileInputStream fileInputStream = new FileInputStream("src/day09_IO/b.txt");
        byte[] b=new byte[1024];
        int read = 0;//返回值为读取的有效字节个数
        while ((read=fileInputStream.read(b))!=-1){
            System.out.println(new String(b,0,read));
        }

        fileInputStream.close();
    }

~~~

**拷贝文件**

~~~java
public static void main(String[] args) throws IOException {
        String pathCopy = "src/day09_IO/b.txt";
        String pathWrite = "src/day09_IO/c.txt";

        copy(pathCopy, pathWrite);
    }


    public static void copy(String pathCopy, String pathWrite) throws IOException {
        FileInputStream fileInputStream = new FileInputStream(pathCopy);
        FileOutputStream fileOutputStream = new FileOutputStream(pathWrite);
        byte[] bytes = new byte[1024];

        int len = 0;

        while ((len = fileInputStream.read(bytes)) != -1) {
            fileOutputStream.write(bytes, 0, len);
        }

        fileOutputStream.close();
        fileInputStream.close();
    }
~~~



#### 字符流

**Reader**

~~~java
//单个读取
public static void main(String[] args) throws IOException {
        FileReader fileReader = new FileReader("src/day09_IO/b.txt");
        int read = 0;
        while ((read = fileReader.read()) != -1) {
            System.out.print((char) read);
        }

        fileReader.close();
    }


//读取到字符数组
public static void main(String[] args) throws IOException {
        FileReader fileReader = new FileReader("src/day09_IO/b.txt");

        char[] chars = new char[1024];
        int len = 0;//len为读取到数组的字符个数，如果没有则是-1 

        while ((len = fileReader.read(chars)) != -1) {
            System.out.println(new String(chars,0,len));
        }

        fileReader.close();
    }
    
~~~

**Writer**

**flush与close的区别**：flush之后还可以添加数据，close之后流就关闭了，就从内存中消失了，就不能用了。close之后自动将内存缓冲区中的数据刷新到文件中。

~~~java
//写单个字符串
public static void main(String[] args) throws IOException {
        FileWriter fileWriter = new FileWriter("src/day09_IO/b.txt", true);//第二个参数为是否可追加
        fileWriter.write("hello");//把数据写入到内存缓冲区中
        fileWriter.flush();//把内存缓冲区中的数据刷新到文件中
        fileWriter.close();
    }


//从字符数组中写数据
public static void main(String[] args) throws IOException {
        FileWriter fileWriter = new FileWriter("src/day09_IO/b.txt", true);
        char[] b = {'q', 'c', 'b'};
        fileWriter.write(b);
        fileWriter.flush();

        fileWriter.write(b, 0, 1);
        fileWriter.flush();
        fileWriter.close();
    }
~~~



### 属性集

#### Hashtable子类Properties

唯一一个和IO流相结合的集合，是双列集合，key与value默认都是字符串

- store方法：把集合中的临时数据，持久化写入硬盘
- load方法：把硬盘中的文件（键值对），读取到集合使用

~~~java
public static void main(String[] args) {
        Properties properties = new Properties();
        properties.setProperty("111","victor");
        properties.setProperty("222","flank");

        Set<String> strings = properties.stringPropertyNames();
        for (String string : strings) {
         System.out.println(string+"===="+properties.getProperty(string));
        }
    }
~~~

#### store方法

store(OutputStream out,String comments):直接输出流，不能写中文，comments用于说明，解释，都不能使用中文解释，一般为空字符串

store(Writer writer,String comments):字符输出，可以写中文

~~~java
 public static void main(String[] args) throws IOException {
        Properties properties = new Properties();
        properties.setProperty("111", "victor");
        properties.setProperty("222", "flank");
        properties.setProperty("333", "赵丽颖");

        /*FileOutputStream fileOutputStream = new FileOutputStream("src/day09_IO/properties.txt");
        properties.store(fileOutputStream, "save data");//使用字节流*/

        FileWriter fileWriter = new FileWriter("src/day09_IO/properties.txt");//使用字符流
        properties.store(fileWriter, "save data2");

        
        //fileOutputStream.close();

        fileWriter.close();
    }
~~~

#### load方法

load(InputStream instream):字节输入流，不能读取包含汉字的键值对

load(Reader reader):字符输入流，可以读取包含汉字的键值对

~~~java
public static void main(String[] args) throws IOException {
        Properties properties = new Properties();
        /*FileReader fileReader = new FileReader("src/day09_IO/properties.txt");
        properties.load(fileReader);*///字符输入流

        FileInputStream fileInputStream = new FileInputStream("src/day09_IO/properties.txt");
        properties.load(fileInputStream);//字节输入流

        Set<String> strings = properties.stringPropertyNames();
        for (String s : strings) {
            System.out.println(s + "=" + properties.getProperty(s));
        }

        fileInputStream.close();
        //fileReader.close();
    }
~~~



### 缓冲流

也叫高效流，是上面常用流的加强，增加效率 ,内部有个默认的缓冲区大小为8*1024

**字节缓冲流**:BufferdInputStream,BufferedOutputStream

**字符缓冲流**:BufferedReader,,BuffererWriter

#### BufferdInputStream

~~~java
public static void main(String[] args) throws IOException {
        FileInputStream fileInputStream = new FileInputStream("src/day09_IO/b.txt");
        BufferedInputStream bufferedInputStream = new BufferedInputStream(fileInputStream,1024);
        int len = 0;
        while ((len = bufferedInputStream.read()) != -1) {
            System.out.println(len);
        }

        bufferedInputStream.close();
    }
~~~

#### BufferedOutputStream

~~~java
public static void main(String[] args) throws IOException {
        FileOutputStream fileOutputStream = new FileOutputStream("src/day09_IO/c.txt");
        BufferedOutputStream bufferedOutputStream = new BufferedOutputStream(fileOutputStream);
        bufferedOutputStream.write(78);
        bufferedOutputStream.flush();
        bufferedOutputStream.close();
    }
~~~

#### BufferedReader

~~~java
public static void main(String[] args) throws IOException {
        FileReader fileReader = new FileReader("src/day09_IO/c.txt");
        BufferedReader bufferedReader = new BufferedReader(fileReader);
        String len=null;
        while ((len=bufferedReader.readLine())!=null){
            System.out.println(len);
        }

        bufferedReader.close();
    }
~~~



#### BuffererWriter

~~~java
public static void main(String[] args) throws IOException {
        FileWriter fileWriter = new FileWriter("src/day09_IO/c.txt", true);
        BufferedWriter bufferedWriter = new BufferedWriter(fileWriter);
        bufferedWriter.write("你好，哈哈哈哈");
        bufferedWriter.newLine();//换行
        bufferedWriter.flush();
        bufferedWriter.close();
    }
~~~



### 转换流

可以指定编码任意格式

#### 字符编码与字符集

ASCII，GBK，Unicode

#### InputStreamReader与OutputStreamWriter

~~~java
public static void main(String[] args) throws IOException {
        OutputStream outputStream = new FileOutputStream("src/day09_IO/c.txt");
        OutputStreamWriter outputStreamWriter = new OutputStreamWriter(outputStream, "GBK");
        System.out.println(outputStreamWriter.getEncoding());
        outputStreamWriter.write("妹妹你好");
        outputStreamWriter.close();

        System.out.println(new File("src/day09_IO/c.txt").length());

        InputStreamReader inputStreamReader = new InputStreamReader(new FileInputStream("src/day09_IO/c.txt"), "GBK");
        int len = 0;
        char[] chars = new char[1024];
        while ((len = inputStreamReader.read(chars)) != -1) {
            System.out.println(new String(chars, 0, len));
        }

        while ((len = inputStreamReader.read()) != -1) {
            System.out.println((char) len);
        }
    
        inputStreamReader.close();
    }
~~~



### 序列化流与反序列化流

要想实现序列化与反序列化必须实现 Serializable 接口，如果类的属性有引用自定义类型也是需要实现Serializable

**ObjectOutputStream**把对象以流的形式吸入到文件中保存，叫写对象，也叫对象的序列化

**ObjectInputStream**把文件中保存的对象以流的形式取出来，叫做读对象，也叫对象的反序列化

被**static**关键字修饰的不可以被序列化，因为序列化与反序列化都是针对对象而言

**transient**：瞬态关键字

被transient修饰的也不能被序列化

~~~java
public static void main(String[] args) throws IOException, ClassNotFoundException {
        ObjectOutputStream objectOutputStream = new ObjectOutputStream(
                new FileOutputStream("src/day09_IO/student.txt"));
        objectOutputStream.writeObject(new Student(1, "victor"));
        objectOutputStream.close();


        FileInputStream fileInputStream = new FileInputStream("src/day09_IO/student.txt");
        ObjectInputStream objectInputStream = new ObjectInputStream(fileInputStream);
        Object o = objectInputStream.readObject();
        System.out.println(o);
        fileInputStream.close();
    }
~~~

注：当一个类实现了序列化接口，就代表在此类中生成了一个序列号，然后序列化之后在文件中又生成了同样的序列号，当反序列化的时候就可以对比两个序列号如果一致则反序列化成功，如果不一致则会报序列号冲突的异常，所以在序列化之后不能更改原来类，或者在本类中定义private static final long serialVersionUID = 12L;这样序列号无论如何都是12。



### 标准的输入流与输出流

~~~java
System.in      //标准的输入流，键盘输入
System.out    //标准的输出流，控制台输出
~~~





### 打印流

#### PrintStream

为其他输出流添加了功能，是他们能够方便的打印各种数据表示形式

特点：

1. 只负责数据的输出，不负责数据的读取
2. 与其他输出流不同，永远不会跑出IOException异常
3. 有特有的方法：print，println   

~~~java
public static void main(String[] args) throws IOException {
        PrintStream printStream = new PrintStream("src/day09_IO/c.txt");
        printStream.println("hello");
        System.setOut(printStream);//设置打印目的地
        System.out.println("nihao");
        printStream.flush();
        printStream.close();
    }
~~~

### RandomAccessFile，随机存取文件流

继承Object，实现了DataInput，DataOutput，即可以做输入也可以输出

### NIO非阻塞IO

更加高效的实现读写



# 网络编程

### 网络通信协议

#### UDP（用户数据报协议）

UDP是无连接协议，数据的发送端与接收端不建立连接，就是发送短不管接收端是否存在都对发数据，同样接受到数据也不会有反馈

**特点**：消耗资源小，通信效率高，通常用于音频、视频、普通数据的传输，视频会议都是用UDP，因为偶尔丢失一两个数据包不会产生太大影响

数据被限制在64Kb以内，，超出不能发送

#### TCP/IP协议

传输控制协议，面向链接的协议，数据交互之前先建立逻辑链接

形成高效的四层体系结构：物理链路层、IP层、应用层

客户端与服务器的三次握手：

1. 客户端向服务器发送链接请求，等待服务器确认
2. 服务器向客户端回送响应，通知收到了链接请求
3. 客户端向服务器发送确认信息，确认链接

**特点**：可以保证数据的安全

#### 端口号

当使用网络软件一打开，操作系统就会给网络软件分配一个随机的端口号或者软件向操作系统要指定的端口号

由两个字节组成，取值范围：0-65535

注：1024之前的不能使用，已被系统分配，端口号不能重复

标志着正在计算机上运行的进程

端口号与ip地址的组合得出一个网络套接字：Socket



### TCP通信程序

#### 概述

TCP可以实现两台计算机的数据交互，通信的两端严格区分客户端与服务端

**两端通信时步骤**

1. 服务端程序，需要事先启动，等待客户端链接
2. 客户端主动链接服务端，连接成功才能通信。服务端不可以主动链接客户端

**在Java中，提供两个类实现TCP通信程序**

1. 客户端：创建Socket对象，向服务端发出链接请求，服务端响应，建立连接开始通信，使用IO字节流通信
2. 服务端：创建ServerSocket对象，相当于开启一个服务，等待客户端链接

**服务端应该明确两件事**

1. 多个客户端与服务器交互，服务器必须明确是和哪个客户端交互，在服务器端有个方法可以拿到客户端duix
2. 服务器使用客户端的流与客户端交互

#### 文件上传

**服务器**

~~~java
 public static void main(String[] args) throws IOException {
        FileOutputStream fos = new FileOutputStream("src/day10_network/2.png");
        ServerSocket serverSocket = new ServerSocket(8080);

        Socket socket = serverSocket.accept();
        InputStream is = socket.getInputStream();

        byte[] bytes = new byte[1024];
        int len = 0;

        while ((len = is.read(bytes)) != -1) {
            fos.write(bytes, 0, len);
        }


        OutputStream os = socket.getOutputStream();
        os.write("上传成功".getBytes());


        fos.close();
        socket.close();
        serverSocket.close();

    }
~~~

**客户端**

~~~java
public static void main(String[] args) throws IOException {
        FileInputStream fileInputStream = new FileInputStream("/home/victor/图片/1.png");
        Socket socket = new Socket("127.0.0.1", 8080);
        OutputStream os = socket.getOutputStream();

        byte[] bytes = new byte[1024];
        int len = 0;
        while ((len = fileInputStream.read(bytes)) != -1) {
            os.write(bytes, 0, len);
        }

        socket.shutdownOutput();//上传不会有终止符号，所以使用这条语句来关闭流终止循环并加上终止符号

        InputStream is = socket.getInputStream();
        byte[] bytes2 = new byte[1024];
        is.read(bytes2);
        System.out.println(new String(bytes2));

        fileInputStream.close();
        socket.close();

    }
~~~

注：可以使用多线程进行优化，上传一个创建一个线程，如果是BS架构那么客户端就是浏览器

### URL编程





# 函数式接口

有且只有一个==抽象==方法的接口，成为函数式接口，可以包含其他方法

~~~java
@FunctionalInterface//用于检测是否为函数式接口
public interface MyInterface {
    public abstract void test1();
}
~~~

函数式接口使用：一般作为方法的参数和返回值类型

匿名内部类会生成class文件。lambda不会，而且延时加载

~~~java
public class demo01 {

    public static void show(MyInterface myInterface) {
        myInterface.test1();
    }

    public static void main(String[] args) {
        show(new MyInterface() {
            @Override
            public void test1() {
                System.out.println("匿名内部类");
            }
        });

        show(() -> {
            System.out.println("Lambda表达式");
        });
    }
}
~~~

### 函数式编程

#### 使用lambda进行性能优化

~~~java
public class demo02_lambada {
    public static void log(int level, MyInterface2 m2) {//第二个参数是一个函数式接口
        if (level == 1) {
            System.out.println(m2.test());
        }
    }


    public static void main(String[] args) {
        String a = "java";
        String b = "hrllo";

        log(2, () -> a + b);
        //如果不使用Lambda表达式会使资源浪费，
        // 因为如果第一个参数不满足，不会执行lambda表达式
        
        //如果使用直接返回加字符串的话，那么就会导致不管第一个参数满不满足都会进行加法
    }
}

~~~

# 反射

#### 概念：

将类的各个组成部分封装为其他对象，这就是反射机制

#### 优点：

1. 可以在程序运行过程中操作对象
2. 解耦合

![](/home/victor/learn/学习总结/java代码经历的阶段.jpg)

#### **获取class对象的方式：**

1. Class.forName("全类名")：将字节码文件加载到内存，返回Class对象

2. 类名.class：通过类名的属性class获取

3. 对象.getClass()：这个方法在Object类中定义（所以对象都有这个方法）

4. 使用类加载器的loadClass("全类名")：获取class对象

   ~~~java
   
   public static void main(String[] args) throws ClassNotFoundException {
           //1.
           Class cls = Class.forName("day11_reflect.Person");
           System.out.println(cls);
   
           //2
           Class<Person> cls2 = Person.class;
           System.out.println(cls2);
   
           //3
           Person person = new Person();
           Class<? extends Person> cls3 = person.getClass();
           System.out.println(cls3);
       
       	System.out.println(cls == cls2);//true
       	System.out.println(cls == cls3);//true
       }
   ~~~

   **注：**同一个字节码文件在程序运行中只会被加载一次，所以无论用上面哪个方法获取都是同一个对象



#### Class对象功能

- 获取功能
  - 获取成员变量
    - getFields()：获取public修饰的及父类中的所有成员变量
    - getFiels(String name)：获取public修饰的某个成员变量
    - getDeclaredFields()：获取所有成员变量
    - getDeclaredField(String name)获取某个成员变量
  - 获取构造方法
    - getConstructors()
    - getConstructor(Class<?> ...paramterTypes)    参数为形参类型无参构造就不需要
    - getDeclaredConstructor(Class<?> ...paramterTypes)
    - getDeclaredConstructors()
  - 获取成员方法
    - getMethods()：获取所有的public方法及父类的所有public方法
    - getMethod(String name,Class<?> ...paramterTypes)
    - getDeclaredMethods()：获得自己的所有方法
    - getDeclaredMethod(String name,Class<?> ...paramterTypes)
  - 获取类名
    - getName()
  - 获取父类
    - getSuperclass()
  - 获取带有泛型的父类
    - getGenericSuperclass()
  - 获取实现的接口
    - getInterfaces()
  - 获取所在的包
    - getPackage()



#### Field：成员变量

操作：

- 设置值：set(Object obj,Object value)    obj为要操作哪个实例对象的成员变量
- 获取值：get（Object obj）
- 忽略访问权限修饰符的安全检查：成员变量.setAccessible(true)    暴力反射
- 获取权限修饰符、数据类型、变量名

#### Constructor：构造方法

创建对象：构造方法.instance（Object....），如果是无参可以直接Class对象.instance

#### Method：方法

执行方法：方法.invoke(Object obj,Class<?> ...paramterTypes)  obj为方法所属实例对象

拿到方法名：方法.getName()

获取注解、权限修饰符、返回值类型、形参类型列表、抛出的异常等



#### 读取配置文件并执行方法

~~~properties
ClassName=day11_reflect.Person
method=eat
~~~

~~~java
public static void main(String[] args) throws Exception {

        Properties properties = new Properties();

   
        ClassLoader classLoader = demo05_case.class.getClassLoader();

        InputStream resource = classLoader.getResourceAsStream("demo.properties");
        //配置文件需要放在与当前类类所属包的同一级目录，否则当前类的类加载器找不到

        properties.load(resource);


        Class<?> aClass = Class.forName(properties.getProperty("ClassName"));

        Method method = aClass.getMethod(properties.getProperty("method"));

        method.invoke(aClass.newInstance());


    }
~~~

注：所有基本数据类型、数组、枚举、接口、注解等都有Class对象



# 注解

#### 作用：

- 编写文档：【生成doc文档】
- 代码分析：【使用反射】
- 编译检查：【Override】

#### JDK内置注解

- @Override：重写

- @Deprecated：该注解标注的内容，已过时
- @SuppressWarnings（“all”）：警告压制

#### 自定义注解

格式：

- 元注解：描述注解的注解

  - @Target：描述注解能够作用的位置

  - @Retention：描述注解被保留的阶段

    ​		1、RetentionPolicy.SOURCE：注解只保留在源文件，当Java文件编译成class文件的时候，注解被遗弃；
    ​		2、RetentionPolicy.CLASS：注解被保留到class文件，但jvm加载class文件时候被遗弃，这是默认的生命周期；
    ​		3、RetentionPolicy.RUNTIME：注解不仅被保存到class文件中，jvm加载class文件之后，仍然存在；

  - @Documented：描述注解是否被抽取到api文档中

  - @Inherited：描述注解是否被子类继承

- public @interface xxxxxx

本质：就是一个接口，继承了Annotation的接口

属性：接口中可以定义的成员方法

要求：

- 属性的返回值类型：
  - 基本数据类型
  - Class
  - String
  - 枚举
  - 注解
  - 以上类型的数组
- 之所以把抽象方法叫做属性，是因为使用时需要赋值，所以命名时要像个属性，如果定义属性时，使用default给了默认值，使用时不需要赋值。
- 如果只有一个属性需要赋值，并且名字是value，赋值时不需要value=，直接写值

**自定义注解**

~~~java
//              作用于方法上        成员变量上               类上
@Target({ElementType.METHOD, ElementType.FIELD, ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)//自定义注解一般保留在Runtime阶段
@Documented
@Inherited
public @interface demo01 {

    int num();

    String name();

    Season season();

    demo02 d();

    String[] name2();

    int age() default 18;

}
~~~

**使用自定义注解**

~~~java
@demo01(num = 1,name="victor",season = Season.SPRING,d=@demo02,name2 = {"123","456"})
public class Test {
    
}
~~~

#### 注解的解析（获取注解定义的属性值）

~~~java
@demo01(num = 1,name="victor",season = Season.SPRING,d=@demo02,name2 = {"123","456"})
public class Test {
    public static void main(String[] args) {
        demo01 annotation = Test.class.getAnnotation(demo01.class);
        String name = annotation.name();
        int num = annotation.num();
        int age = annotation.age();
        Season season = annotation.season();
        demo02 d = annotation.d();

        System.out.println(num+" "+name+" "+age+" "+season+" "+d.ggg());
    }
}

~~~



# JDBC

### 本质

官方定义的一套操作所有关系性数据库的规则（接口），各个数据库实现这套接口，提供数据库驱动jar包。

### 快速入门

1. 导入驱动jar包
2. 注册驱动
3. 获取数据库链接对象 connection
4. 定义sql
5. 获取执行sql语句的对象  statement
6. 执行sql
7. 处理结果
8. 释放资源

### 常用对象

#### DriverManager(驱动管理对象)

1. 注册驱动

   DriverManager内部有注册驱动的方法叫registerDriver(Driver driver)

   写代码使用：Class.forName("com.jdbc.mysql.Driver")是把类加载到内存

   那如何实现的注册驱动呢，是因为com.jdbc.mysql.Driver这个类里面有个静态方法，只要类一加载就会注册驱动

   ~~~java
   static {
           try {
               DriverManager.registerDriver(new Driver());
           } catch (SQLException var1) {
               throw new RuntimeException("Can't register driver!");
           }
       }
   ~~~

   

2. 获取数据库链接

   ~~~java
   DriverManager.getConnection("jdbc:mysql://localhost:3306/test01", "root", "123456");
   
   //如果是本地+3306  可以简写为"jdbc:mysql:///test01"
   ~~~

   

#### Connection（数据库链接对象）

1. 获取执行sql的对象
   - createStatement()
   - prepareStatement(String sql)
2. 使用connection管理事务
   - 开启事务：setAutoCommit(boolean autoCommit)   设置false则开启事务
   - 提交事务：commit()
   - 回滚事务：rollback（）

#### Statement（执行sql对象）

```java
statement.execute("")    //可以执行所有语句
statement.executeUpdate("")   //执行DML（增删改），DDL（库，表）
statement.executeQuery("select * from stu");   //执行SQL查询语句     返回ResultSet结果集
```

#### ResultSet（结果集对象，封装查询结果）

- boolean next()：游标向下移动一行
- getXXX：获取数据
  - XXX：数据类型
  - 参数：
    - int：代表列的编号，从1开始
    - String：代表列名称
- 注：next（）方法如果有下一行返回TRUE，并移动指向下一行。如果没有则返回FALSE

#### PreparedStatement（增强版Statement）

1. 防止sql注入问题

2. 预编译sql：参数使用？作为占位符使用

   ~~~java
   public static void main(String[] args) throws Exception {
           Connection connection = JDBCUtils.getConnection();
           String sql = "select * from stu where id =?";
           PreparedStatement preparedStatement = connection.prepareStatement(sql);
           preparedStatement.setInt(1, 1);
           ResultSet resultSet = preparedStatement.executeQuery();
           while (resultSet.next()) {
               System.out.println(resultSet.getObject(1));
               System.out.println(resultSet.getObject(2));
               System.out.println(resultSet.getObject(3));
           }
   
   
           JDBCUtils.close(resultSet, preparedStatement, connection);
       }
   ~~~

   



### 封装工具类

~~~java
public class JDBCUtils {
    public static String url;
    public static String username;
    public static String password;

    static {
        Properties properties = new Properties();
        try {
            properties.load(JDBCUtils.class.getClassLoader().getResourceAsStream("jdbc.properties"));
            Class.forName(properties.getProperty("driver"));
            url = properties.getProperty("url");
            username = properties.getProperty("username");
            password = properties.getProperty("password");
        } catch (IOException e) {
            e.printStackTrace();
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
    }


    public static Connection getConnection() {
        Connection connection = null;
        try {
            connection = DriverManager.getConnection(url, username, password);
        } catch (SQLException throwables) {
            throwables.printStackTrace();
        }
        return connection;
    }

    public static void close(Statement statement, Connection connection) {
        if (statement != null) {
            try {
                statement.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }

        if (connection != null) {
            try {
                connection.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
    }


    public static void close(ResultSet resultSet, Statement statement, Connection connection) {
        if (resultSet != null) {
            try {
                resultSet.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
        if (statement != null) {
            try {
                statement.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }

        if (connection != null) {
            try {
                connection.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
    }
}

~~~



### 控制事务

- 开启事务：在执行sql之前开启事务
- 提交事务：当所有sql执行完提交事务
- 回滚事务：在catch中回滚



### 数据库连接池

#### 概念

一个存放数据库链接的容器

#### 优点

- 节约资源
- 用户访问高效

#### 实现

1. 标准接口：DataSource     javax.sql包
   1. 获取链接：getConnection()
   2. 归还链接：Connection.close()，如果连接对象是连接池中获取的，则调用close方法是归还连接，而不是关闭连接。
2. 一般不自己实现，由数据库厂商实现
   1. C3P0：
   2. Druid：



#### C3P0

 步骤

1. 导入jar包

2. 定义配置文件

   - 名称：c3p0.properties或c3p0-config.xml
   - 路径：类路径下，也就是src下

3. 创建核心对象（数据库连接池对象）ComboPooledDataSource

4. 获取链接：getConnection()

5. 归还链接：connection.close();

   ~~~java
    public static void main(String[] args) throws Exception {
           ComboPooledDataSource dataSource = new ComboPooledDataSource();
   
           dataSource.setDriverClass("com.mysql.jdbc.Driver");
           dataSource.setJdbcUrl("jdbc:mysql://localhost:3306/test01");
           dataSource.setUser("root");
           dataSource.setPassword("123456");
   
           Connection connection = dataSource.getConnection();
           String sql = "select * from stu";
           PreparedStatement preparedStatement = connection.prepareStatement(sql);
           ResultSet resultSet = preparedStatement.executeQuery();
           while (resultSet.next()) {
               System.out.println(resultSet.getString(2));
           }
   
           connection.close();
       }
   ~~~

   

#### Druid

步骤

1. 导入jar包

2. 定义配置文件：是properties，任意名称，任意位置

3. 获取数据库连接处对象：通过工厂来获取，DruidDataSourceFactory

4. 获取链接：getConnection()

5. 归还：close

   ~~~properties
   driverClassName=com.mysql.jdbc.Driver
   url=jdbc:mysql://localhost:3306/test01
   username=root
   password=123456
   
   initialSize=5
   
   //最大链接
   maxActive=10
   //超时时间
   maxWait=3000
   ~~~

   ~~~java
   public static void main(String[] args) throws Exception {
   
           Properties properties = new Properties();
           properties.load(demo03_druid.class.getClassLoader().getResourceAsStream("druid.properties"));
   
   
           DataSource dataSource = DruidDataSourceFactory.createDataSource(properties);
   
           Connection connection = dataSource.getConnection();
           String sql = "select * from stu";
           PreparedStatement preparedStatement = connection.prepareStatement(sql);
           ResultSet resultSet = preparedStatement.executeQuery();
           while (resultSet.next()) {
               System.out.println(resultSet.getString(2));
           }
   
           connection.close();
       }
   ~~~



#### Druid工具类

~~~java
public class DruidUtils {

    public static DataSource dataSource;

    static {
        Properties properties = new Properties();
        try {
            properties.load(demo03_druid.class.getClassLoader().
                    getResourceAsStream("druid.properties"));
            dataSource = DruidDataSourceFactory.createDataSource(properties);
        } catch (IOException e) {
            e.printStackTrace();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    public static Connection getConnection() throws SQLException {
        return dataSource.getConnection();
    }

    public static void close(Statement statement, Connection connection) {
        if (statement != null) {
            try {
                statement.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }


        if (connection != null) {
            try {
                connection.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
    }


    public static void close(ResultSet resultSet, Statement statement, Connection connection) {
        if (resultSet != null) {
            try {
                resultSet.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }

        close(statement, connection);
    }


    public static DataSource getDataSource() {
        return dataSource;
    }
}
~~~