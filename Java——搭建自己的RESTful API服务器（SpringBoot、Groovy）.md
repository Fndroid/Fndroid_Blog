# Java������Լ���RESTful API��������SpringBoot��Groovy��

������һƪJavaWeb��صĲ��ͣ������漰��

  * **SpringBoot��΢��ܣ��ṩ���ٹ�������Ĺ���**
  * **SpringMVC��Struts�������**
  * **MyBatis�����ݿ������**
  * **Groovy������Java��ϵĸ߼����ԣ��ײ�ΪJava**
  * **Maven�����ڼ�jar������ʹ��**
  * **log4j����־����**

����Ҫ������һ���򵥵Ľӿڣ�����URL����õ���Ӧ�����ݣ����ݸ�ʽ������JSON����Xml

Ч�����£�

![](http://images2015.cnblogs.com/blog/852227/201611/852227-20161126151405018-281214771.png)

���Կ���������ʹ����Get�����������˵�ǰ�������������鱾��Ϣ�����õ���һ��JSON��ʽ�Ľ����

�����Ҫ�õ�Xml��ʽ��ֻ��Ҫ��������ͷ��Accept�ֶ�Ϊtext/xml����application/xml���ɣ�

![](http://images2015.cnblogs.com/blog/852227/201611/852227-20161126151659956-197370601.png)



���ţ���ʼ���ǵ���Ŀ��

����ʹ�õ����������IDEA��ԭ��ܼ򵥣���Ϊ���Ǹ�������Ҫ���÷�������SpringBoot�Դ���Tomcat��֧�֣�����������Ŀֻ��Ҫ����һ��main�������ɡ�

�������£�

  1. **������������Ŀ**
  2. **��д��Ŀ���루MyBatis��SpringMVC��**
  3. **����Log**
  4. **���**

**�� ������������Ŀ**

_**a.**_ ����һ��Maven��Ŀ��ʡ�ԣ�

_**b.**_ �޸�pom.xml

    
    
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
     11     <!-- ����starter-parent-->
     12     <parent>
     13         <groupId>org.springframework.boot</groupId>
     14         <artifactId>spring-boot-starter-parent</artifactId>
     15         <version>1.4.2.RELEASE</version>
     16     </parent>
     17 
     18     <dependencies>
     19         <!-- ����һ��web��Ŀ-->
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
     31         <!-- ��parent��Ŀ�г�ȥlogging���������Ϊ����ʹ�õ���log4j -->
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
     43         <!-- ����log4j֧�� -->
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
     54         <!-- groovy֧��-->
     55         <dependency>
     56             <groupId>org.codehaus.groovy</groupId>
     57             <artifactId>groovy-all</artifactId>
     58             <version>2.4.7</version>
     59         </dependency>
     60         <!-- ʹ��Mybatis-->
     61         <dependency>
     62             <groupId>org.mybatis.spring.boot</groupId>
     63             <artifactId>mybatis-spring-boot-starter</artifactId>
     64             <version>1.1.1</version>
     65         </dependency>
     66         <!-- jdbc����-->
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
     78         <!-- ֧������xml -->
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
     92             <!-- ��������jar�ļ�-->
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

����������һЩ��������Ӧ�������ڴ������Ѿ�ע�ͳ����ˡ�ע�⣺ʹ��parent��ǩ���븸������������У��������Ҫ�ģ�����ʹ��exclusion��ȥ���ο�31-41�д��롣

ͬʱע�⵽�����ﻹ������groovy��֧�֣����ʹ�����ǵı��������Դ����ͱ�дGroovy���롣

����plugin�����ڴ��jar������Ҫ��ӣ�����ʹ��maven�������Ŀ���д��������

_**c.**_ ����application.yml�ļ�

���ﴴ���Ŀ�����properties������yml�ļ���Ϊֱ�ۣ�����IDEA�ܸ�����ʾ�����ҹٷ���һЩdemo����ʹ��yml���ã�

    
    
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

����������mybatis��һЩ���ԣ�SpringBoot��Ȼ��Ĭ���������ԣ����ǵ���鵽�û����õ�ʱ�򣬻�����ʹ���û����õ���Ϣ��

��������ļ�����Application�������õ���

_**d.**_ ��resourcesĿ¼�´���mybatis�ļ��У������ڷ���mybatis�������ļ��Լ�mapper�ļ�

_**e.**_
��src/main/javaĿ¼�´�����Ӧ�İ����ֱ�Ϊdao��controller��service��entity�ȷֱ����ڴ��SpringMVC��Ӧ����

������Ϻ�Ŀ¼�ṹ��

![](http://images2015.cnblogs.com/blog/852227/201611/852227-20161126212939440-625130930.png)

�����Ȳ��ù�Application��log4j.properties�������ļ��������õ���ʱ��ᴴ����������Ŀ�ͻ��������ˣ����Կ�ʼ��д�����ˡ�



**�� ��д��Ŀ����**

��˵��һ�㣬�����õ���Groovy�����Java�������Ӽ򵥣�����Groovy�ײ��Ǳ���ΪJava���еġ�

���ݹ��ܣ�����ϣ��ͨ����������URL�õ���Ӧ�Ľ����

��ȡ�����鱾��Ϣ��http://localhost:8080/books

����id��ȡ��Ӧ�鱾��Ϣ��http://localhost:8080/book/2 ����ȡidΪ2���鱾��Ϣ��

_**a.**_ �������ݱ�������Ӧ��ʵ����Book����entity���д���һ����ΪBook��groovy�ű���

    
    
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

����û�ж���������Σ�GroovyĬ�ϻ��趨Ϊpublic�����ֶ���Ĭ����private�����ң�Groovy��Ĭ��Ϊprivate���ֶ�����setter��getter����������Ҳ�Ͳ��������Լ�д�ˡ�

_**b.**_ ����controller����controller��Ӧ�İ��д���BookController��

    
    
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

����SpringMVC��֪ʶ��@RestController���������ΪRESTful��Controller��

ʹ��@Resource����ͨ��ע��ȡ��BookServices�Ķ���

@RequestMapping���Ǳ��������·��������·��ִ�ж�Ӧ�ķ�����

@PathVariable���ڻ�ȡ·������Ϊid��ֵ��������ѯ

���Կ�������ķ����з���ֵ���Ͷ�û��return��䣬����Groovy�����ԣ�Ĭ�ϻὫ���һ�������Ϊ����ֵ��

_**c.**_ ����Services���������Ӧ������

����Controllerʹ�õ�����BookService�ķ�������Ҫ���Ƕ�����б�д��

    
    
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

@Service��������ΪSpringMVC�ķ���㣬����ͨ��ע��ע����IBookDao�Ķ���

_**d.**_ ����Dao�ӿ�IBookDao��

    
    
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

@Repository��������Ϊʵ��Bean��

�ӿ�ֻ��Ҫ�����Ӧ�ķ������ɡ�

_**e.**_ ���ݽӿڱ�дmapper�ļ�����mybatis�ļ����д���һ��book-mapper.xml�ļ���

    
    
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

����Ҫע�⣬mapperԪ�ص�namespace��Ҫ����Ϊ����Dao��Ӧ��ȫ·���������Ŷ�������select�ֱ��Ӧ���ǵ�����������id��Ӧ��������resultType��Ӧ����ֵ��parameterType��Ӧ������������select�б�дSQL��伴�ɡ�

ע�⣺����getBooks��Ȼ�᷵�ض���У�����resultType��Ȼ��Book������List����ΪMyBatis���������װ��List��

_**f.**_ ����mybatis-conf.xml

һ������¶���MyBatis������������Ȼ������ڴ��ļ��У�

    
    
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

������ﶨ����Book�ı������������mapper�ļ�����ֱ��ʹ��"Book"����"com.fndroid.entity.Book"

_**g.**_ ������������Application��:

    
    
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

16��@SpringBootApplicationע���������ΪSpringBoot����ڣ����������Զ����ú����ɨ�裨ɨ��controller��service��ע���������ࣩ�ȹ��ܡ�

17��@MapperScan������dao������Ѱ��mapper�������mapperָ���Ƕ�Ӧ��Dao�ࣩ

21-23�ж�����һ��main����������������ǳ������ڣ��������SpringApplication�ľ�̬����run������һ��Spring����

25-29��ͨ��ע��@Beanע����һ��DataSource���͵Ķ�����ΪDataSource��������Ҫ�������ݿ��������Ϣ��������Ҫ���@ConfigurationPropertiesע�⣬���ע��Ὣ������ǰ�����application.yml�ļ��У���Ӧ���������ø����DataSource����

31-40��ͨ��@Beanע����ע��SqlSessionFactory�����������ᱻSpring�������ý������ݿ����������Ҫ��application.yml�����õ�mapper��conf·�����õ�SqlSessionFactoryBean�У����ͨ��getObject��������һ��SqlSessionFactory�Ķ���

42-47����ʹ��@Beanע��ȡ��application.yml�ļ��ж�Ӧmybatis��������Ϣ

54-57�����ǽ�DataSource���ø�PlatformTranscationManager����Ϳ�����Spring������JDBC��������



**�� ����Log**

��ʵ��Ŀ�������Ѿ����������ˣ�����ʵ����������������̨����������ϸ��������Ϣ�����ԭ����������pom.xml���Ѿ���ȥ��Ĭ�ϵ�logging��Ҳ����logback�������Ҫʹ��logback���ܼ򵥣�ֻ��Ҫ��resource�����һ��logback�������ļ��Ϳ����ˡ��������õ���log4j��������Ҫ��resource�д���log4j.properties�ļ������ﲻ����yml����Ϊlog4j��֧�֡�

    
    
     1 # ȫ������
     2 log4j.rootLogger=INFO, stdout, file
     3 # mybatis������
     4 log4j.logger.com.fndroid.dao.IBookDao=TRACE
     5 # ����̨�������
     6 log4j.appender.stdout=org.apache.log4j.ConsoleAppender
     7 log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
     8 log4j.appender.stdout.layout.ConversionPattern=%d %5p [%t] - %m%n
     9 # �ļ��������
    10 log4j.appender.file=org.apache.log4j.FileAppender
    11 log4j.appender.file.layout=org.apache.log4j.PatternLayout
    12 log4j.appender.file.layout.ConversionPattern=%d %5p [%t] - %m%n
    13 log4j.appender.file.File=D:/log.log4j
    14 log4j.appender.file.ImmediateFlush=true

��2��������Logger�������־����ΪINFO���������Ϊ����̨���ļ�

��4�����Ǳ������ڶ�Ӧ��Dao�࣬����ϣ����ʾ��ѯ���Ͳ�ѯ���������������TRACE

��6-8���ÿ���̨�����������ʾ���ֺ͸�ʽ

��10-14�����ļ������������ʾ���֡���ʽ�����·����



�������Ŀ�Ѿ��������ն�ʹ��maven���mvn spring-boot:run
��������Ŀ������ֱ��ʹ��IDEA���ж�Ӧ��main���������Կ���������̨��ӡ��������������Ϣ����D����Ҳ�����˶�Ӧ��log.log4j�ļ���

�����������ɹ�����Ϣ��

![](http://images2015.cnblogs.com/blog/852227/201611/852227-20161126221214503-1415510759.png)

ʹ�õ��Թ��߻������������URL��http://localhost:8080/books

���������ʾ��Ӧ��xml����ļ���

![](http://images2015.cnblogs.com/blog/852227/201611/852227-20161126221439659-1455165878.png)

���ؿ���̨�����Կ���SQL���Ҳ������ˣ�

![](http://images2015.cnblogs.com/blog/852227/201611/852227-20161126221523018-213391261.png)

���֤�����ǵ�Log����û��������

ע�⣺�����Ҫ������Ҫ�ȹر�ǰһ��Application���������ʾ�˿ڱ�ռ�á�



**�� ���**

����SpringBoot��Ŀ������ǳ��򵥣�ֻ��Ҫʹ���ն����룺mvn packag
���������Ŀ��targetĿ¼������һ��jar�ļ���ֻ��Ҫ�ڷ�����������ʹ��java -jar xxx.jar������������SpringBoot��Ŀ��

![](http://images2015.cnblogs.com/blog/852227/201611/852227-20161126222430878-1127089462.png)



������ָ������л��

