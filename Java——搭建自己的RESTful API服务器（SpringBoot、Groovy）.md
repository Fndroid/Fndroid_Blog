# Java——搭建自己的RESTful API服务器（SpringBoot、Groovy）

这又是一篇JavaWeb相关的博客，内容涉及：

  * **SpringBoot：微框架，提供快速构建服务的功能**
  * **SpringMVC：Struts的替代者**
  * **MyBatis：数据库操作库**
  * **Groovy：能与Java结合的高级语言，底层为Java**
  * **Maven：用于简化jar包导入和打包**
  * **log4j：日志管理**

我们要做的是一个简单的接口，根据URL请求得到对应的数据，数据格式可以是JSON或者Xml

效果如下：

![](http://images2015.cnblogs.com/blog/852227/201611/852227-20161126151405018-281214771.png)

可以看到，这里使用了Get方法，请求了当前服务器中所有书本信息，并得到了一个JSON格式的结果。

如果需要得到Xml格式，只需要设置请求头的Accept字段为text/xml或者application/xml即可：

![](http://images2015.cnblogs.com/blog/852227/201611/852227-20161126151659956-197370601.png)



接着，开始我们的项目：

这里使用的是社区版的IDEA，原因很简单，因为我们根本不需要配置服务器，SpringBoot自带了Tomcat的支持，所以运行项目只需要运行一个main方法即可。

步骤如下：

  1. **创建并配置项目**
  2. **编写项目代码（MyBatis、SpringMVC）**
  3. **配置Log**
  4. **打包**

**① 创建并配置项目**

_**a.**_ 创建一个Maven项目（省略）

_**b.**_ 修改pom.xml

    
    
      1 <?xml version="1.0" encoding="UTF-8"?>
      2 <project xmlns="http://maven.apache.org/POM/4.0.0"
      3          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      4          xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
      5     <modelVersion>4.0.0</modelVersion>
      6 
      7     <groupId>com.fndroid.javaweb</groupId>
      8     <artifactId>springboottest</artifactId>
      9     <version>1.0-SNAPSHOT</version>
     10 
     11     <!-- 依赖starter-parent-->
     12     <parent>
     13         <groupId>org.springframework.boot</groupId>
     14         <artifactId>spring-boot-starter-parent</artifactId>
     15         <version>1.4.2.RELEASE</version>
     16     </parent>
     17 
     18     <dependencies>
     19         <!-- 这是一个web项目-->
     20         <dependency>
     21             <groupId>org.springframework.boot</groupId>
     22             <artifactId>spring-boot-starter-web</artifactId>
     23             <exclusions>
     24                 <exclusion>
     25                     <artifactId>log4j-over-slf4j</artifactId>
     26                     <groupId>org.slf4j</groupId>
     27                 </exclusion>
     28             </exclusions>
     29         </dependency>
     30 
     31         <!-- 从parent项目中出去logging这个包，因为我们使用的是log4j -->
     32         <dependency>
     33             <groupId>org.springframework.boot</groupId>
     34             <artifactId>spring-boot-starter</artifactId>
     35             <exclusions>
     36                 <exclusion>
     37                     <groupId>org.springframework.boot</groupId>
     38                     <artifactId>spring-boot-starter-logging</artifactId>
     39                 </exclusion>
     40             </exclusions>
     41         </dependency>
     42 
     43         <!-- 引入log4j支持 -->
     44         <dependency>
     45             <groupId>org.springframework.boot</groupId>
     46             <artifactId>spring-boot-starter-log4j</artifactId>
     47             <version>1.3.8.RELEASE</version>
     48         </dependency>
     49         <dependency>
     50             <groupId>com.jayway.jsonpath</groupId>
     51             <artifactId>json-path</artifactId>
     52             <scope>test</scope>
     53         </dependency>
     54         <!-- groovy支持-->
     55         <dependency>
     56             <groupId>org.codehaus.groovy</groupId>
     57             <artifactId>groovy-all</artifactId>
     58             <version>2.4.7</version>
     59         </dependency>
     60         <!-- 使用Mybatis-->
     61         <dependency>
     62             <groupId>org.mybatis.spring.boot</groupId>
     63             <artifactId>mybatis-spring-boot-starter</artifactId>
     64             <version>1.1.1</version>
     65         </dependency>
     66         <!-- jdbc驱动-->
     67         <dependency>
     68             <groupId>mysql</groupId>
     69             <artifactId>mysql-connector-java</artifactId>
     70             <version>6.0.5</version>
     71             <scope>runtime</scope>
     72         </dependency>
     73         <dependency>
     74             <groupId>log4j</groupId>
     75             <artifactId>log4j</artifactId>
     76             <version>1.2.17</version>
     77         </dependency>
     78         <!-- 支持生成xml -->
     79         <dependency>
     80             <groupId>com.fasterxml.jackson.dataformat</groupId>
     81             <artifactId>jackson-dataformat-xml</artifactId>
     82         </dependency>
     83     </dependencies>
     84 
     85     <properties>
     86         <java.version>1.8</java.version>
     87     </properties>
     88 
     89 
     90     <build>
     91         <plugins>
     92             <!-- 用于生成jar文件-->
     93             <plugin>
     94                 <groupId>org.springframework.boot</groupId>
     95                 <artifactId>spring-boot-maven-plugin</artifactId>
     96             </plugin>
     97         </plugins>
     98     </build>
     99 
    100     <repositories>
    101         <repository>
    102             <id>spring-releases</id>
    103             <url>https://repo.spring.io/libs-release</url>
    104         </repository>
    105     </repositories>
    106     <pluginRepositories>
    107         <pluginRepository>
    108             <id>spring-releases</id>
    109             <url>https://repo.spring.io/libs-release</url>
    110         </pluginRepository>
    111     </pluginRepositories>
    112 </project>

这里引入了一些依赖，对应的作用在代码中已经注释出来了。注意：使用parent标签引入父类的所有依赖中，如果有需要的，可以使用exclusion除去，参考31-41行代码。

同时注意到，这里还引入了groovy的支持，这就使得我们的编译器可以创建和编写Groovy代码。

最后的plugin是用于打包jar，必须要添加，才能使用maven命令对项目进行打包发布。

_**c.**_ 创建application.yml文件

这里创建的可以是properties，但是yml文件更为直观，而且IDEA能高亮显示，并且官方的一些demo都是使用yml配置：

    
    
    mybatis:
      mapperLocations: classpath:mybatis/*-mapper.xml
      config: classpath:mybatis/mybatis-conf.xml
      typeAliasesPackage: com.fndroid
      checkConfigLocation: false
    
    spring:
      datasource:
        url: jdbc:mysql://localhost:3306/books?serverTimezone=UTC&useSSL=false
        username: root
        password: root
        driver-class-name: com.mysql.cj.jdbc.Driver

这里配置了mybatis的一些属性，SpringBoot虽然会默认配置属性，但是当检查到用户配置的时候，会优先使用用户配置的信息。

这个配置文件会在Application定义中用到。

_**d.**_ 在resources目录下创建mybatis文件夹，在用于放置mybatis的配置文件以及mapper文件

_**e.**_
在src/main/java目录下创建对应的包，分别为dao、controller、service、entity等分别用于存放SpringMVC对应的类

配置完毕后目录结构：

![](http://images2015.cnblogs.com/blog/852227/201611/852227-20161126212939440-625130930.png)

这里先不用管Application和log4j.properties这两个文件，后面用到的时候会创建。至此项目就基本搭建完毕了，可以开始编写代码了。



**② 编写项目代码**

先说明一点，这里用的是Groovy，相比Java代码会更加简单，而且Groovy底层是编译为Java运行的。

根据功能，现在希望通过下面两个URL得到对应的结果：

获取所有书本信息：http://localhost:8080/books

根据id获取对应书本信息：http://localhost:8080/book/2 （获取id为2的书本信息）

_**a.**_ 根据数据表，创建对应的实体类Book。在entity包中创建一个名为Book的groovy脚本：

    
    
     1 package com.fndroid.entity
     2 
     3 import javax.xml.bind.annotation.XmlRootElement
     4 
     5 @XmlRootElement(name = 'book')
     6 class Book {
     7     int id
     8     String title
     9     String description
    10     String pub_time
    11     String author
    12 
    13     Book() {
    14     }
    15 
    16     Book(int id, String title, String description, String pub_time, String author) {
    17         this.id = id
    18         this.title = title
    19         this.description = description
    20         this.pub_time = pub_time
    21         this.author = author
    22     }
    23 }

这里没有对类进行修饰，Groovy默认会设定为public，而字段则默认是private，并且，Groovy会默认为private的字段生成setter和getter方法，所以也就不用我们自己写了。

_**b.**_ 创建controller，在controller对应的包中创建BookController：

    
    
     1 package com.fndroid.controller
     2 
     3 import com.fndroid.entity.Book
     4 import com.fndroid.services.BookServices
     5 import org.springframework.web.bind.annotation.PathVariable
     6 import org.springframework.web.bind.annotation.RequestMapping
     7 import org.springframework.web.bind.annotation.RestController
     8 
     9 import javax.annotation.Resource
    10 
    11 @RestController
    12 class BookController {
    13 
    14     @Resource
    15     BookService service;
    16 
    17     @RequestMapping('/books')
    18     List<Book> getBooks() {
    19         service.getBooks()
    20     }
    21 
    22     @RequestMapping('/book/{id}')
    23     Book getBook(@PathVariable(name = 'id')int id) {
    24         service.getBook(id)
    25     }
    26 }

这是SpringMVC的知识，@RestController表明这个类为RESTful的Controller。

使用@Resource表明通过注入取得BookServices的对象。

@RequestMapping则是表明请求的路径，根据路径执行对应的方法。

@PathVariable用于获取路径中名为id的值，用作查询

可以看到这里的方法有返回值类型而没有return语句，这是Groovy的特性，默认会将最后一个语句作为返回值。

_**c.**_ 创建Services，并定义对应方法：

上面Controller使用到两个BookService的方法，需要我们对其进行编写：

    
    
     1 package com.fndroid.services
     2 
     3 import com.fndroid.entity.Book
     4 import com.fndroid.dao.IBookDao
     5 import org.springframework.stereotype.Service
     6 
     7 import javax.annotation.Resource
     8 @Service
     9 class BookService {
    10     @Resource
    11     IBookDao bookDao;
    12 
    13     List<Book> getBooks() {
    14         bookDao.getBooks()
    15     }
    16 
    17     Book getBook(int id){
    18         bookDao.getBook(id)
    19     }
    20 }

@Service表明此类为SpringMVC的服务层，并且通过注解注入了IBookDao的对象。

_**d.**_ 创建Dao接口IBookDao：

    
    
     1 package com.fndroid.dao
     2 
     3 import com.fndroid.entity.Book
     4 import org.springframework.stereotype.Repository
     5 
     6 @Repository
     7 interface IBookDao {
     8     List<Book> getBooks()
     9     Book getBook(int id)
    10 }

@Repository表明此类为实体Bean类

接口只需要定义对应的方法即可。

_**e.**_ 根据接口编写mapper文件，在mybatis文件夹中创建一个book-mapper.xml文件：

    
    
     1 <?xml version="1.0" encoding="UTF-8" ?>
     2 <!DOCTYPE mapper
     3         PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
     4         "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
     5 <mapper namespace="com.fndroid.dao.IBookDao">
     6     <select id="getBooks" resultType="com.fndroid.entity.Book">
     7         select * from book
     8     </select>
     9 
    10     <select id="getBook" resultType="com.fndroid.entity.Book" parameterType="Integer">
    11         select * from book where id = #{id}
    12     </select>
    13 </mapper>

这里要注意，mapper元素的namespace需要定义为我们Dao对应的全路径名，接着定义两个select分别对应我们的两个方法，id对应方法名，resultType对应返回值，parameterType对应参数，接着在select中编写SQL语句即可。

注意：这里getBooks虽然会返回多个行，但是resultType依然是Book而不是List，因为MyBatis会帮我们组装成List。

_**f.**_ 定义mybatis-conf.xml

一般情况下对于MyBatis的配置如别名等会声明在此文件中：

    
    
     1 <?xml version="1.0" encoding="UTF-8" ?>
     2 <!DOCTYPE configuration
     3         PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
     4         "http://mybatis.org/dtd/mybatis-3-config.dtd">
     5 <configuration>
     6     <settings>
     7         <setting name="logImpl" value="LOG4J"/>
     8     </settings>
     9 
    10     <typeAliases>
    11         <typeAlias type="com.fndroid.entity.Book" alias="Book"/>
    12     </typeAliases>
    13 </configuration>

如果这里定义了Book的别名，则上面的mapper文件可以直接使用"Book"代替"com.fndroid.entity.Book"

_**g.**_ 创建程序的入口Application类:

    
    
     1 package com.fndroid
     2 
     3 import org.apache.ibatis.session.SqlSessionFactory
     4 import org.apache.tomcat.jdbc.pool.DataSource
     5 import org.mybatis.spring.SqlSessionFactoryBean
     6 import org.mybatis.spring.annotation.MapperScan
     7 import org.mybatis.spring.boot.autoconfigure.MybatisProperties
     8 import org.springframework.boot.SpringApplication
     9 import org.springframework.boot.autoconfigure.SpringBootApplication
    10 import org.springframework.boot.context.properties.ConfigurationProperties
    11 import org.springframework.context.annotation.Bean
    12 import org.springframework.context.annotation.Primary
    13 import org.springframework.core.io.support.PathMatchingResourcePatternResolver
    14 import org.springframework.jdbc.datasource.DataSourceTransactionManager
    15 import org.springframework.transaction.PlatformTransactionManager
    16 
    17 @SpringBootApplication
    18 @MapperScan('com.fndroid.dao')
    19 class Application {
    20 
    21     static void main(String[] args) {
    22         SpringApplication.run(Application.class, args)
    23     }
    24 
    25     @Bean
    26     @ConfigurationProperties(prefix = 'spring.datasource')
    27     DataSource dataSource() {
    28         new DataSource()
    29     }
    30 
    31     @Bean
    32     SqlSessionFactory sqlSessionFactory() throws Exception {
    33         def sqlSessionFactoryBean = new SqlSessionFactoryBean()
    34         sqlSessionFactoryBean.setDataSource(dataSource())
    35         def resolve = resolver()
    36         def mybatisProperties = this.mybatisProperties()
    37         sqlSessionFactoryBean.setConfigLocation(resolve.getResource(mybatisProperties.getConfigLocation()))
    38         sqlSessionFactoryBean.setMapperLocations(resolve.getResources(mybatisProperties.mapperLocations[0]))
    39         sqlSessionFactoryBean.getObject()
    40     }
    41 
    42     @Bean
    43     @Primary
    44     @ConfigurationProperties(prefix = 'mybatis')
    45     MybatisProperties mybatisProperties() {
    46         new MybatisProperties()
    47     }
    48 
    49     @Bean
    50     PathMatchingResourcePatternResolver resolver(){
    51         new PathMatchingResourcePatternResolver()
    52     }
    53 
    54     @Bean
    55     PlatformTransactionManager transactionManager() {
    56         new DataSourceTransactionManager(dataSource())
    57     }
    58 }

16行@SpringBootApplication注解表明此类为SpringBoot的入口，并且启动自动配置和组件扫描（扫描controller和service等注解声明的类）等功能。

17行@MapperScan表明从dao包下面寻找mapper（这里的mapper指的是对应的Dao类）

21-23行定义了一个main方法，这个方法就是程序的入口，这里调用SpringApplication的静态方法run来启动一个Spring程序

25-29行通过注解@Bean注入了一个DataSource类型的对象，因为DataSource的属性需要表明数据库的连接信息，所以需要添加@ConfigurationProperties注解，这个注解会将我们先前定义的application.yml文件中，对应的属性配置给这个DataSource对象

31-40行通过@Bean注解来注入SqlSessionFactory对象，这个对象会被Spring容器调用进行数据库操作，我们要将application.yml中配置的mapper和conf路径设置到SqlSessionFactoryBean中，最后通过getObject方法返回一个SqlSessionFactory的对象

42-47行则使用@Bean注解取得application.yml文件中对应mybatis的配置信息

54-57行则是将DataSource设置给PlatformTranscationManager，这就可以让Spring来处理JDBC的事务了



**③ 配置Log**

其实项目到这里已经可以运行了，但是实际上运行起来控制台并不会有详细的运行信息输出，原因是我们在pom.xml中已经除去了默认的logging，也就是logback。如果需要使用logback，很简单，只需要在resource中添加一个logback的配置文件就可以了。而这里用的是log4j，所以需要在resource中创建log4j.properties文件，这里不能用yml，因为log4j不支持。

    
    
     1 # 全局配置
     2 log4j.rootLogger=INFO, stdout, file
     3 # mybatis的配置
     4 log4j.logger.com.fndroid.dao.IBookDao=TRACE
     5 # 控制台输出配置
     6 log4j.appender.stdout=org.apache.log4j.ConsoleAppender
     7 log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
     8 log4j.appender.stdout.layout.ConversionPattern=%d %5p [%t] - %m%n
     9 # 文件输出配置
    10 log4j.appender.file=org.apache.log4j.FileAppender
    11 log4j.appender.file.layout=org.apache.log4j.PatternLayout
    12 log4j.appender.file.layout.ConversionPattern=%d %5p [%t] - %m%n
    13 log4j.appender.file.File=D:/log.log4j
    14 log4j.appender.file.ImmediateFlush=true

第2行声明根Logger输入的日志类型为INFO，输出对象为控制台和文件

第4行则是表明对于对应的Dao类，我们希望显示查询语句和查询结果，所以这里是TRACE

第6-8配置控制台输出，包括显示布局和格式

第10-14则是文件输出，包括显示布局、格式和输出路径等



到这里，项目已经可以在终端使用maven命令：mvn spring-boot:run
来运行项目，或者直接使用IDEA运行对应的main方法，可以看到，控制台打印了容器开启的信息，而D盘中也生成了对应的log.log4j文件：

下面是启动成功的信息：

![](http://images2015.cnblogs.com/blog/852227/201611/852227-20161126221214503-1415510759.png)

使用调试工具或者浏览器访问URL：http://localhost:8080/books

浏览器会显示对应的xml结果文件：

![](http://images2015.cnblogs.com/blog/852227/201611/852227-20161126221439659-1455165878.png)

返回控制台，可以看到SQL语句也被输出了：

![](http://images2015.cnblogs.com/blog/852227/201611/852227-20161126221523018-213391261.png)

这就证明我们的Log配置没有问题了

注意：如果需要重启，要先关闭前一个Application，否则会提示端口被占用。



**④ 打包**

对于SpringBoot项目，打包非常简单，只需要使用终端输入：mvn packag
命令即可在项目的target目录下生成一个jar文件，只需要在服务器环境下使用java -jar xxx.jar命令即可启动这个SpringBoot项目。

![](http://images2015.cnblogs.com/blog/852227/201611/852227-20161126222430878-1127089462.png)



有误请指出，感谢！

