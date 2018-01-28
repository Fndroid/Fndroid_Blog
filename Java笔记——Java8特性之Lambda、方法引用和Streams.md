# Java笔记——Java8特性之Lambda、方法引用和Streams

Java8已经推出了好一段时间了，而掌握Java8的新特性也是必要的，如果要进行Spring开发，那么可以发现Spring的官网已经全部使用Java8来编写示例代码了，所以，不学就看不懂。

这里涉及三个重要特性：

  * Lambda
  * 方法引用
  * Streams

**① Lambda**

最早了解Lambda是在C#中，而从Java8开始，Lambda也成为了新的特性，而这个新的特性的目的，就是为了 **消除单方法接口实现的匿名内部类** 。

在Java8以前的版本中，定义一个Thread是这样的：

    
    
    1 final int i = 0;
    2 new Thread(new Runnable() {
    3     @Override
    4     public void run() {
    5         System.out.println("i = " + i);
    6     }
    7 }).start();

而Lambda是这样的：

    
    
    1 int i = 0;
    2 new Thread(() -> System.out.println("i = " + i));

首先不看Lambda本身的写法，可以发现，对于i值的访问，在Lambda中已经不需要声明i为final了。

其次，要明白一个重要的道理： **Lambda要求实现的接口中只有一个方法**
，像上面的Runnable接口就只有一个run方法，如果一个接口中有多于一个方法，则不能写成Lambda的形式。

最后来看标准的Lambda表达式的结构：

![](http://images2015.cnblogs.com/blog/852227/201611/852227-20161121212747425-378685369.png)

结构很简单，小括号表示参数列表，大括号表示方法体，中间使用一个 "->" 隔开即可。

这里的 **参数体和方法体分别指的是接口中方法的参数体和方法体** 。

接着我们看一个比较比较复杂的：

    
    
    1 ArrayList<Integer> integers = new ArrayList<>();
    2 integers.add(6);
    3 integers.add(2);
    4 integers.add(5);
    5 integers.sort((o1, o2) -> o1 - o2);
    6 System.out.println(integers);

可以看到，创建了一个ArrayList，里面的元素是Integer类型，接着调用sort()方法对其进行排序，sort方法接受一个Comparator接口的实现类，这个接口中有且仅有一个方法compare，所以如果使用匿名内部类的写法，如下所示：

    
    
    1 integers.sort(new Comparator<Integer>() {
    2     @Override
    3     public int compare(Integer o1, Integer o2) {
    4         return o1 - o2;
    5     }
    6 });

可以发现，这里参数列表和方法体都很明白了，要注意的是，这里的方法体中不带{}，原因就是 **当方法体只有一个语句的时候，{}可以省略** 。

**另外，return关键字也被省略了，原因是编译器会认为，既然只有一个语句，那么这个语句执行的结果就应该是返回值，所以return也就不需要了。同理，当参数只有一个的时候，小括号也是可以省略的**
。

明白这种对应的关系，Lambda就算是掌握了。

**② 方法引用**

方法引用包括几种情况：

  * **静态方法引用**
  * **构造方法引用**
  * **类成员方法引用**
  * **对象方法引用**

要注意这里的方法引用实际上是某些Lambda表达式的更简洁写法，原因就是在这些情况下，编译器能够智能的推断出参数体中的值究竟是方法的传入参数还是调用者。

先定义一个Car类：

    
    
     1 import java.util.function.Supplier;
     2 
     3 public class Car {
     4     // 通过Supplier获取Car实例
     5     public static Car create(Supplier<Car> supplier) {
     6         return supplier.get();
     7     }
     8 
     9     // 静态方法，一个入参Car对象
    10     public static void collide(final Car car) {
    11         System.out.println("Collide " + car.toString());
    12     }
    13 
    14     // 一个入参Car
    15     public void follow(final Car car) {
    16         System.out.println("Following car " + car.toString());
    17     }
    18 
    19     // 不带入参
    20     public void repair() {
    21         System.out.println("Repaired car " + this.toString());
    22     }
    23 }

**_\--构造方法引用_**

Supplier接口的定义：

    
    
    @FunctionalInterface
    public interface Supplier<T> {
    
        /**
         * Gets a result.
         *
         * @return a result
         */
        T get();
    }

这个接口被FunctionalInterface注解声明了，这个注解是一个新的注解，表明这个接口是一个函数式接口，只有一个抽象方法。

所以我们调用静态方法create创建Car对象的时候，代码应该是如下所示的：

    
    
    1 Car.create(new Supplier<Car>() {
    2     @Override
    3     public Car get() {
    4         return new Car();
    5     }
    6 });

但是我们才说了Lambda表达式，所以更简单的写法就是：

    
    
    1 Car.create(()->new Car());

可以看到，Car类存在一个不带参数的构造方法，所以 **编译器不需要根据参数列表猜测构造方法的参数（** 因为都是空的），所以就有一个更加简单的写法：

    
    
    1 Car.create(Car::new);

**实际上，如果Lambda的参数个数和类的构造方法个数一致，也可以改写为上面的形式，只要是没有歧义即可。**

**_\--静态方法引用_**

这里开始会涉及一些Streams内容，但是可以先忽略，后面会详细说。

我们创建一个Car对象，接着将其添加进一个List中：

    
    
    1 final Car car = Car.create(Car::new);
    2 final List<Car> cars = Arrays.asList(car); 

Java8中给Iterable接口添加了forEach方法方便我们遍历集合类型。

现在假设我们要给List中的每个Car对象调用一次Car.collide(Car
car)静态方法，那么可以使用forEach方法，而forEach方法需要传入一个Consumer，恰好，这个Consumer接口也带有FunctionalInterface注解，所以我们一步一步的来看：

    
    
    1 cars.forEach(new Consumer<Car>() {
    2     @Override
    3     public void accept(Car car) {
    4         Car.collide(car);
    5     }
    6 });

写成Lambda：

    
    
    1 cars.forEach(c -> Car.collide(c));

就是对传进来的Car对象执行静态方法，很简单。但是实际上，对于静态方法，编译器也不需要推断调用者（类名），当传入参数和静态方法所需参数个数一致时，就不存在歧义：

![](http://images2015.cnblogs.com/blog/852227/201611/852227-20161122112840831-903568648.png)

所以这里可以直接使用方法引用：

    
    
    1 cars.forEach(Car::collide);

**_\--类成员方法引用_**

类的成员方法不能是静态的，而这个情况其实和静态方法类似，区别是，Lambda表达式的参数个数需要等于所调用方法的入参个数加一。

为什么要加一？

因为类的成员方法不能通过类名直接调用，只能通过对象来调用，也就是 **Lambda表达式的第一个参数，是方法的调用者**
，从第二个开始的参数个数要和需要调用方法的入参个数一致即可。如下图所示：

![](http://images2015.cnblogs.com/blog/852227/201611/852227-20161122113021893-430112219.png)

对于上面的例子，如果要对List中的每个对象执行一次它的repair方法：

    
    
    1 cars.forEach(c -> c.repair());

根据上图，这里参数只有一个，而repair方法没有入参，所以不存在歧义，即可以改写为对应的方法引用：

    
    
    1 cars.forEach(Car::repair);

_**\--对象方法引用**_

与类方法引用不同的是， **对象方法引用方法的调用者是一个外部的对象** 。如下图：

![](http://images2015.cnblogs.com/blog/852227/201611/852227-20161122112608190-1509652118.png)

对于上面例子，可以再创建一个Car的对象police，并让police调用follow方法跟踪List中的每个Car：

    
    
    1 final Car police = Car.create(Car::new);
    2 cars.forEach((car1) -> police.follow(car1));

改成对象方法引用：

    
    
    1 cars.forEach(police::follow);

至此，方法引用也完成了。

**③ Streams**

Streams的思想很简单，就是遍历。

一个流的生命周期分为三个阶段：

  1. **生成**
  2. **操作、变换（可以多次）**
  3. **消耗（只有一次）**

**_\--生成_**

生成Stream对象

    
    
    1 // 1. 对象
    2 Stream stream = Stream.of("a", "b", "c");
    3 // 2. 数组
    4 String [] strArray = new String[] {"a", "b", "c"};
    5 stream = Stream.of(strArray);
    6 stream = Arrays.stream(strArray);
    7 // 3. 集合
    8 List<String> list = Arrays.asList(strArray);
    9 stream = list.stream();

生成DoubleSteam、IntSteram或LongStream对象（这是目前支持的三个数值类型Stream对象）

    
    
    1 IntStream.of(new int[]{1, 2, 3}); // 根据数组生成
    2 IntStream.range(1, 3); // 按照范围生成，不包括3
    3 IntStream.rangeClosed(1, 3); // 按照范围生成，包括3

等等。。。

_**\--变换**_

一个流可以经过多次的变换，变换的结果仍然是一个流。

常见的变换：map (mapToInt, flatMap 等)、 filter、 distinct、 sorted、 peek、 limit、 skip、
parallel、 sequential、 unordered

_**\--消耗**_

一个流对应一个消耗操作。

常见的消耗操作：forEach、 forEachOrdered、 toArray、 reduce、 collect、 min、 max、 count、
anyMatch、 allMatch、 noneMatch、 findFirst、 findAny、 iterator

例子：

定义一个学生类：

    
    
     1 public class Student {
     2     public enum Sax{
     3         FEMALE, MALE
     4     }
     5 
     6     private String name;
     7     private int age;
     8     private Sax sax;
     9     private int height;
    10 
    11     public Student(String name, int age, Sax sax, int height) {
    12         this.name = name;
    13         this.age = age;
    14         this.height = height;
    15         this.sax = sax;
    16     }
    17 
    18     public String getName() {
    19         return name;
    20     }
    21 
    22     public int getAge() {
    23         return age;
    24     }
    25 
    26     public Sax getSax() {
    27         return sax;
    28     }
    29 
    30     public int getHeight() {
    31         return height;
    32     }
    33 
    34     @Override
    35     public String toString() {
    36         return "Student{" +
    37                 "name='" + name + '\'' +
    38                 ", age=" + age +
    39                 ", sax=" + sax +
    40                 ", height=" + height +
    41                 '}';
    42     }
    43 }

在main方法中创建示例数据：

    
    
    1 List<Student> students = Arrays.asList(
    2         new Student("Fndroid", 22, Student.Sax.MALE, 180),
    3         new Student("Jack", 20, Student.Sax.MALE, 170),
    4         new Student("Liliy", 18, Student.Sax.FEMALE, 160)
    5 );

下面实现几个查询：

1.输出所有性别为MALE的学生:

循环：

    
    
    1 for (Student student : students) {
    2     if (student.getSax() == Student.Sax.MALE) {
    3         System.out.println(student);
    4     }
    5 }

使用Stream：

    
    
    1 students.stream() // 打开流
    2         .filter(student -> student.getSax() == Student.Sax.MALE) // 进行过滤
    3         .forEach(System.out::println); // 输出

2.求出所有学生的平均年龄：

    
    
    1 OptionalDouble averageAge = students.stream()
    2         .mapToInt(Student::getAge) // 将对象映射为整型
    3         .average(); // 根据整形数据求平均值
    4 System.out.println("所有学生的平均年龄为：" + averageAge.orElse(0));

可以看到这里的average方法得到一个OptionalDouble类型的值，这也是Java8的新增特性，OptionalXXX类用于减少空指针异常带来的崩溃，可以通过orElse方法获得其值，如果值为null，则取默认值0。

3.输出每个学生姓名的大写形式：

    
    
    1 List<String> names = students.stream()
    2         .map(Student::getName) // 将Student对象映射为String（姓名）
    3         .map(String::toUpperCase) // 将姓名转为小写
    4         .collect(Collectors.toList()); // 生成列表
    5 System.out.println("所有学生姓名的大写为：" + names);

4.按照年龄从小到大排序：

    
    
    1 List<Student> sortedStudents = students.stream()
    2         .sorted((o1, o2) -> o1.getAge() - o2.getAge()) // 按照年龄排序
    3         .collect(Collectors.toList()); // 生成列表
    4 System.out.println("按年龄排序后列表为：" + sortedStudents);

5.判断是否存在名为Fndroid的学生：

    
    
    boolean isContain = students.stream()
            .anyMatch(student -> student.getName().equals("Fndroid")); // 查询任意匹配项是否存在
    System.out.println("是否包含姓名为Fndroid的学生：" + isContain);

6.将所有学生按照性别分组：

    
    
    1 Map<Student.Sax, List<Student>> groupBySax = students.stream()
    2         .collect(Collectors.groupingBy(Student::getSax)); // 根据性别进行分组
    3 System.out.println(groupBySax.get(Student.Sax.FEMALE));

7.求出每个学生身高比例：

    
    
    1 double sumHeight = students.stream().mapToInt(Student::getHeight).sum(); // 求出身高总和
    2 DecimalFormat formator = new DecimalFormat("##.00"); // 保留两位小数
    3 List<String> percentages = students.stream()
    4         .mapToInt(Student::getHeight) // 将Student对象映射为身高整型值
    5         .mapToDouble(value -> value / sumHeight * 100) // 求出比例
    6         .mapToObj(per -> formator.format(per) + "%") // 组装为字符串
    7         .collect(Collectors.toList()); 
    8 System.out.println("所有学生身高比例：" + percentages);



