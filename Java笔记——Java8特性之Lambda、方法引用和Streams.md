# Java�ʼǡ���Java8����֮Lambda���������ú�Streams

Java8�Ѿ��Ƴ��˺�һ��ʱ���ˣ�������Java8��������Ҳ�Ǳ�Ҫ�ģ����Ҫ����Spring��������ô���Է���Spring�Ĺ����Ѿ�ȫ��ʹ��Java8����дʾ�������ˣ����ԣ���ѧ�Ϳ�������

�����漰������Ҫ���ԣ�

  * Lambda
  * ��������
  * Streams

**�� Lambda**

�����˽�Lambda����C#�У�����Java8��ʼ��LambdaҲ��Ϊ���µ����ԣ�������µ����Ե�Ŀ�ģ�����Ϊ�� **�����������ӿ�ʵ�ֵ������ڲ���** ��

��Java8��ǰ�İ汾�У�����һ��Thread�������ģ�

    
    
    1 final int i = 0;
    2 new Thread(new Runnable() {
    3     @Override
    4     public void run() {
    5         System.out.println("i = " + i);
    6     }
    7 }).start();

��Lambda�������ģ�

    
    
    1 int i = 0;
    2 new Thread(() -> System.out.println("i = " + i));

���Ȳ���Lambda�����д�������Է��֣�����iֵ�ķ��ʣ���Lambda���Ѿ�����Ҫ����iΪfinal�ˡ�

��Σ�Ҫ����һ����Ҫ�ĵ��� **LambdaҪ��ʵ�ֵĽӿ���ֻ��һ������**
���������Runnable�ӿھ�ֻ��һ��run���������һ���ӿ����ж���һ������������д��Lambda����ʽ��

���������׼��Lambda���ʽ�Ľṹ��

![](http://images2015.cnblogs.com/blog/852227/201611/852227-20161121212747425-378685369.png)

�ṹ�ܼ򵥣�С���ű�ʾ�����б������ű�ʾ�����壬�м�ʹ��һ�� "->" �������ɡ�

����� **������ͷ�����ֱ�ָ���ǽӿ��з����Ĳ�����ͷ�����** ��

�������ǿ�һ���ȽϱȽϸ��ӵģ�

    
    
    1 ArrayList<Integer> integers = new ArrayList<>();
    2 integers.add(6);
    3 integers.add(2);
    4 integers.add(5);
    5 integers.sort((o1, o2) -> o1 - o2);
    6 System.out.println(integers);

���Կ�����������һ��ArrayList�������Ԫ����Integer���ͣ����ŵ���sort()���������������sort��������һ��Comparator�ӿڵ�ʵ���࣬����ӿ������ҽ���һ������compare���������ʹ�������ڲ����д����������ʾ��

    
    
    1 integers.sort(new Comparator<Integer>() {
    2     @Override
    3     public int compare(Integer o1, Integer o2) {
    4         return o1 - o2;
    5     }
    6 });

���Է��֣���������б�ͷ����嶼�������ˣ�Ҫע����ǣ�����ķ������в���{}��ԭ����� **��������ֻ��һ������ʱ��{}����ʡ��** ��

**���⣬return�ؼ���Ҳ��ʡ���ˣ�ԭ���Ǳ���������Ϊ����Ȼֻ��һ����䣬��ô������ִ�еĽ����Ӧ���Ƿ���ֵ������returnҲ�Ͳ���Ҫ�ˡ�ͬ��������ֻ��һ����ʱ��С����Ҳ�ǿ���ʡ�Ե�**
��

�������ֶ�Ӧ�Ĺ�ϵ��Lambda�����������ˡ�

**�� ��������**

�������ð������������

  * **��̬��������**
  * **���췽������**
  * **���Ա��������**
  * **���󷽷�����**

Ҫע������ķ�������ʵ������ĳЩLambda���ʽ�ĸ����д����ԭ���������Щ����£��������ܹ����ܵ��ƶϳ��������е�ֵ�����Ƿ����Ĵ���������ǵ����ߡ�

�ȶ���һ��Car�ࣺ

    
    
     1 import java.util.function.Supplier;
     2 
     3 public class Car {
     4     // ͨ��Supplier��ȡCarʵ��
     5     public static Car create(Supplier<Car> supplier) {
     6         return supplier.get();
     7     }
     8 
     9     // ��̬������һ�����Car����
    10     public static void collide(final Car car) {
    11         System.out.println("Collide " + car.toString());
    12     }
    13 
    14     // һ�����Car
    15     public void follow(final Car car) {
    16         System.out.println("Following car " + car.toString());
    17     }
    18 
    19     // �������
    20     public void repair() {
    21         System.out.println("Repaired car " + this.toString());
    22     }
    23 }

**_\--���췽������_**

Supplier�ӿڵĶ��壺

    
    
    @FunctionalInterface
    public interface Supplier<T> {
    
        /**
         * Gets a result.
         *
         * @return a result
         */
        T get();
    }

����ӿڱ�FunctionalInterfaceע�������ˣ����ע����һ���µ�ע�⣬��������ӿ���һ������ʽ�ӿڣ�ֻ��һ�����󷽷���

�������ǵ��þ�̬����create����Car�����ʱ�򣬴���Ӧ����������ʾ�ģ�

    
    
    1 Car.create(new Supplier<Car>() {
    2     @Override
    3     public Car get() {
    4         return new Car();
    5     }
    6 });

�������ǲ�˵��Lambda���ʽ�����Ը��򵥵�д�����ǣ�

    
    
    1 Car.create(()->new Car());

���Կ�����Car�����һ�����������Ĺ��췽�������� **����������Ҫ���ݲ����б�²⹹�췽���Ĳ�����** ��Ϊ���ǿյģ������Ծ���һ�����Ӽ򵥵�д����

    
    
    1 Car.create(Car::new);

**ʵ���ϣ����Lambda�Ĳ�����������Ĺ��췽������һ�£�Ҳ���Ը�дΪ�������ʽ��ֻҪ��û�����弴�ɡ�**

**_\--��̬��������_**

���￪ʼ���漰һЩStreams���ݣ����ǿ����Ⱥ��ԣ��������ϸ˵��

���Ǵ���һ��Car���󣬽��Ž�����ӽ�һ��List�У�

    
    
    1 final Car car = Car.create(Car::new);
    2 final List<Car> cars = Arrays.asList(car); 

Java8�и�Iterable�ӿ������forEach�����������Ǳ����������͡�

���ڼ�������Ҫ��List�е�ÿ��Car�������һ��Car.collide(Car
car)��̬��������ô����ʹ��forEach��������forEach������Ҫ����һ��Consumer��ǡ�ã����Consumer�ӿ�Ҳ����FunctionalInterfaceע�⣬��������һ��һ����������

    
    
    1 cars.forEach(new Consumer<Car>() {
    2     @Override
    3     public void accept(Car car) {
    4         Car.collide(car);
    5     }
    6 });

д��Lambda��

    
    
    1 cars.forEach(c -> Car.collide(c));

���ǶԴ�������Car����ִ�о�̬�������ܼ򵥡�����ʵ���ϣ����ھ�̬������������Ҳ����Ҫ�ƶϵ����ߣ�������������������;�̬���������������һ��ʱ���Ͳ��������壺

![](http://images2015.cnblogs.com/blog/852227/201611/852227-20161122112840831-903568648.png)

�����������ֱ��ʹ�÷������ã�

    
    
    1 cars.forEach(Car::collide);

**_\--���Ա��������_**

��ĳ�Ա���������Ǿ�̬�ģ�����������ʵ�;�̬�������ƣ������ǣ�Lambda���ʽ�Ĳ���������Ҫ���������÷�������θ�����һ��

ΪʲôҪ��һ��

��Ϊ��ĳ�Ա��������ͨ������ֱ�ӵ��ã�ֻ��ͨ�����������ã�Ҳ���� **Lambda���ʽ�ĵ�һ���������Ƿ����ĵ�����**
���ӵڶ�����ʼ�Ĳ�������Ҫ����Ҫ���÷�������θ���һ�¼��ɡ�����ͼ��ʾ��

![](http://images2015.cnblogs.com/blog/852227/201611/852227-20161122113021893-430112219.png)

������������ӣ����Ҫ��List�е�ÿ������ִ��һ������repair������

    
    
    1 cars.forEach(c -> c.repair());

������ͼ���������ֻ��һ������repair����û����Σ����Բ��������壬�����Ը�дΪ��Ӧ�ķ������ã�

    
    
    1 cars.forEach(Car::repair);

_**\--���󷽷�����**_

���෽�����ò�ͬ���ǣ� **���󷽷����÷����ĵ�������һ���ⲿ�Ķ���** ������ͼ��

![](http://images2015.cnblogs.com/blog/852227/201611/852227-20161122112608190-1509652118.png)

�����������ӣ������ٴ���һ��Car�Ķ���police������police����follow��������List�е�ÿ��Car��

    
    
    1 final Car police = Car.create(Car::new);
    2 cars.forEach((car1) -> police.follow(car1));

�ĳɶ��󷽷����ã�

    
    
    1 cars.forEach(police::follow);

���ˣ���������Ҳ����ˡ�

**�� Streams**

Streams��˼��ܼ򵥣����Ǳ�����

һ�������������ڷ�Ϊ�����׶Σ�

  1. **����**
  2. **�������任�����Զ�Σ�**
  3. **���ģ�ֻ��һ�Σ�**

**_\--����_**

����Stream����

    
    
    1 // 1. ����
    2 Stream stream = Stream.of("a", "b", "c");
    3 // 2. ����
    4 String [] strArray = new String[] {"a", "b", "c"};
    5 stream = Stream.of(strArray);
    6 stream = Arrays.stream(strArray);
    7 // 3. ����
    8 List<String> list = Arrays.asList(strArray);
    9 stream = list.stream();

����DoubleSteam��IntSteram��LongStream��������Ŀǰ֧�ֵ�������ֵ����Stream����

    
    
    1 IntStream.of(new int[]{1, 2, 3}); // ������������
    2 IntStream.range(1, 3); // ���շ�Χ���ɣ�������3
    3 IntStream.rangeClosed(1, 3); // ���շ�Χ���ɣ�����3

�ȵȡ�����

_**\--�任**_

һ�������Ծ�����εı任���任�Ľ����Ȼ��һ������

�����ı任��map (mapToInt, flatMap ��)�� filter�� distinct�� sorted�� peek�� limit�� skip��
parallel�� sequential�� unordered

_**\--����**_

һ������Ӧһ�����Ĳ�����

���������Ĳ�����forEach�� forEachOrdered�� toArray�� reduce�� collect�� min�� max�� count��
anyMatch�� allMatch�� noneMatch�� findFirst�� findAny�� iterator

���ӣ�

����һ��ѧ���ࣺ

    
    
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

��main�����д���ʾ�����ݣ�

    
    
    1 List<Student> students = Arrays.asList(
    2         new Student("Fndroid", 22, Student.Sax.MALE, 180),
    3         new Student("Jack", 20, Student.Sax.MALE, 170),
    4         new Student("Liliy", 18, Student.Sax.FEMALE, 160)
    5 );

����ʵ�ּ�����ѯ��

1.��������Ա�ΪMALE��ѧ��:

ѭ����

    
    
    1 for (Student student : students) {
    2     if (student.getSax() == Student.Sax.MALE) {
    3         System.out.println(student);
    4     }
    5 }

ʹ��Stream��

    
    
    1 students.stream() // ����
    2         .filter(student -> student.getSax() == Student.Sax.MALE) // ���й���
    3         .forEach(System.out::println); // ���

2.�������ѧ����ƽ�����䣺

    
    
    1 OptionalDouble averageAge = students.stream()
    2         .mapToInt(Student::getAge) // ������ӳ��Ϊ����
    3         .average(); // ��������������ƽ��ֵ
    4 System.out.println("����ѧ����ƽ������Ϊ��" + averageAge.orElse(0));

���Կ��������average�����õ�һ��OptionalDouble���͵�ֵ����Ҳ��Java8���������ԣ�OptionalXXX�����ڼ��ٿ�ָ���쳣�����ı���������ͨ��orElse���������ֵ�����ֵΪnull����ȡĬ��ֵ0��

3.���ÿ��ѧ�������Ĵ�д��ʽ��

    
    
    1 List<String> names = students.stream()
    2         .map(Student::getName) // ��Student����ӳ��ΪString��������
    3         .map(String::toUpperCase) // ������תΪСд
    4         .collect(Collectors.toList()); // �����б�
    5 System.out.println("����ѧ�������Ĵ�дΪ��" + names);

4.���������С��������

    
    
    1 List<Student> sortedStudents = students.stream()
    2         .sorted((o1, o2) -> o1.getAge() - o2.getAge()) // ������������
    3         .collect(Collectors.toList()); // �����б�
    4 System.out.println("������������б�Ϊ��" + sortedStudents);

5.�ж��Ƿ������ΪFndroid��ѧ����

    
    
    boolean isContain = students.stream()
            .anyMatch(student -> student.getName().equals("Fndroid")); // ��ѯ����ƥ�����Ƿ����
    System.out.println("�Ƿ��������ΪFndroid��ѧ����" + isContain);

6.������ѧ�������Ա���飺

    
    
    1 Map<Student.Sax, List<Student>> groupBySax = students.stream()
    2         .collect(Collectors.groupingBy(Student::getSax)); // �����Ա���з���
    3 System.out.println(groupBySax.get(Student.Sax.FEMALE));

7.���ÿ��ѧ����߱�����

    
    
    1 double sumHeight = students.stream().mapToInt(Student::getHeight).sum(); // �������ܺ�
    2 DecimalFormat formator = new DecimalFormat("##.00"); // ������λС��
    3 List<String> percentages = students.stream()
    4         .mapToInt(Student::getHeight) // ��Student����ӳ��Ϊ�������ֵ
    5         .mapToDouble(value -> value / sumHeight * 100) // �������
    6         .mapToObj(per -> formator.format(per) + "%") // ��װΪ�ַ���
    7         .collect(Collectors.toList()); 
    8 System.out.println("����ѧ����߱�����" + percentages);



