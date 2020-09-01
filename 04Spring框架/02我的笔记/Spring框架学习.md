# 介绍

**组成**

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901083836.png" alt="image-20200601193351274" style="zoom:80%;" />

有七大核心框架。

spring Boot（构建一切）是一个快速开发的脚手架，基于spring Boot可以快速开发单个微服务。Spring Cloud（协调一切）是基于spring Boot实现的。

学习Spring Boot需要完全掌握Spring和SpringMVC框架。

# 2 IOC

## 2.1 理论推导

传统写业务的方式，需要写：

+ UserDao接口
+ UserDaoImpl实现类
+ UserService业务接口（用户只能操作这个）
+ UserService业务接口实现类

### 2.1.1 创建简单工程

**项目结构**

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901083837.png" alt="image-20200601213655808" style="zoom:80%;" />

![image-20200626102940420](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901083838.png)

首先建立`maven`工程，修改`pom`文件

```html
<!--  导入核心包  -->
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>5.2.6.RELEASE</version>
    </dependency>
</dependencies>
```

**1、UserDao接口**

```javascript
public interface UserDao {
    void getUser();
}
```

**2、UserDao接口实现类**

```java
public class UserDaoImpl implements UserDao{
    public void getUser() {
        System.out.println("默认获取用户数据");
    }
}
```

**3、UserService接口**

```java
public interface UserService {
    void getUser();
}
```

**4、UserService接口实现类**

```java
public class UserServiceImpl implements UserService {

    private UserDao userDao = new UserDaoImpl();
    public void getUser() {
        userDao.getUser();
    }
}
```

**5、测试类**

```java
public class MyTest {
    public static void main(String[] args) {

        //用户实际调用的是业务层Service，Dao层他们不需要接触
        UserService userService = new UserServiceImpl();
        userService.getUser();
    }
}
```

+ 控制台输出

```java
默认获取用户数据
```

**推导**

当我们需要在`dao`层新加一个实现接口:

```java
public class UserDaoMysqlImpl implements UserDao{
    public void getUser() {
        System.out.println("----->mysql");
    }
}
```

用户要调用时，程序员需要到 `UserServiceImpl`中做如下的改写：

```java
public class UserServiceImpl implements UserService {

//    private UserDao userDao = new UserDaoImpl();
    private UserDao userDao = new UserDaoMysqlImpl(); //程序员需要改变这一个
    public void getUser() {
        userDao.getUser();
    }
}
```

当遇到的实现类比较多的时候，上面的改变就十分麻烦。

因此，我们需要在`UserServiceImpl`这样更改代码：

```java
public class UserServiceImpl implements UserService {

    private UserDao userDao;

    //利用set进行动态实现值的注入。
    public void setUserDao(UserDao userDao){
        this.userDao = userDao;
    }

    public void getUser() {
        userDao.getUser();
    }
}
```

然后在`main`函数中这样书写，就可以对`UserDaoImpl`和`UserDaoMysqlImpl`实现调用。

```java
public class MyTest {
    public static void main(String[] args) {


        UserService userService = new UserServiceImpl();
        ((UserServiceImpl)userService).setUserDao(new UserDaoImpl());
//        ((UserServiceImpl)userService).setUserDao(new UserDaoMysqlImpl());  //也可以同上面一样被调用
        userService.getUser();
    }
}
```

### 2.1.2 总结

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901083839.png" alt="image-20200601220528893" style="zoom:67%;" />

在我们之前的业务中，用户的需求可（对Dao层添加许多实现类）能会影响我们原来的代码，所以我们需要根据用户的需求去修改原代码，如果程序代码量比较大，修改的成本代价十分昂贵。

因此，我们通过`set`方法进行动态实现值的注入，就可以解决这些困难。

之前，程序是主动创建对象，控制权在程序员手上，使用set注入之后，程序不在具有主动性，而是变成了被动的接收对象。所以，系统的耦合性大大降低，程序员不用再去管理对象的创建了，而是将精力更加关注在业务的实现上。这是IOC的原型。

## 2.2 IOC本质

参考文档官网地址：https://docs.spring.io/spring/docs/5.2.6.RELEASE/spring-framework-reference/core.html#spring-core

在程序中经常去调用别的对象，这个时候就会对调用的对象产生依赖，我们程序的**耦合性**就会变高。`IOC`思想就是基于这种思想提出的。

控制反转`IOC`（Inversion of Control）是一种设计思想，DI（Dependency Injection）(依赖注入)是实现IOC的一种方法。在没有`IOC`的程序中，我们使用面向对象编程，对象的创建与对象间的依赖关系完全硬编码在程序中，对象的创建由程序自己控制，而控制反转则是将对象的创建转移给第三方。

（控制反转，把对象创建和对象 之间的调用过程，交给Spring进行管理。）

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901083840.png" alt="image-20200601220854960" style="zoom:80%;" />

`IOC`是Spring框架的核心内容，我们可以使用XML配置，也可以使用注解，新版本的Spring也可以零配置实现`IOC`。`IOC`意味着将你设计好的对象交给容器控制，而不是传统的在你的对象内部直接(New)控制。

spring初始化时先读取配置文件，根据配置文件或元数据创建和组织对象存入容器中，程序使用时再从IOC容器中取得需要的对象。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901083841.png" alt="image-20200601221427667" style="zoom: 67%;" />

在采用XML方式配置`Bean`的时候，Bean的定义信息实现分离，而采用注解的方式可以将两者合在一体，`Bean`的定义信息直接以注解的形式定义在类中，从而实现了零配置的目的。

## 2.3 `HelloSpring`

### 2.3.1 `IOC`简单项目

**1、项目结构**

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901083842.png" alt="image-20200601231352205" style="zoom:80%;" />

**2、创建实体类**

```java
public class Hello {

    private String str;

    public String getStr() {
        return str;
    }

    //set方法十分重要，它是IOC对象的核心，不能省略
    public void setStr(String str) {
        this.str = str;
    }

    @Override
    public String toString() {
        return "Hello{" +
                "str='" + str + '\'' +
                '}';
    }
}
```

**3、创建xml配置文件**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

        <!--
        1、使用spring来创建对象，在Spring中被称为Bean
        2、类型 变量名 = new 类型（）
            Hello hello = new Hello();
            id = 变量名
            class = new的对象
            property 相当于给对象的属性设置一个值
			value：具体的值，基本数据类型
			ref:引用Spring容器中创建好的对象
        -->
    <bean id="hello" class="com.xiaolun.pojo.Hello">
        <property name="str" value="spring"></property>
    </bean>
</beans>
```

**4、创建测试类**

```java
public class MyTest {
    public static void main(String[] args) {
        //获取spring的上下文对象
        ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
        //我们的对象现在都在spring中管理了，如果我们要使用，直接到那里拿出来即可
        Hello hello = (Hello) context.getBean("hello"); //hello为beans.xml文件中配置的id属性
        System.out.println(hello.toString());
    }
}
```

+ 控制台输出

```java
Hello{str='spring'}
```

+ `Hello`对象是由`Spring`来创建的，它的属性也是通过`Spring`来设置的。

### 2.2.2 简单工程的修改

**1、编写XML文件**

```xml
<bean id="Impl" class="com.xiaolun.dao.UserDaoImpl"></bean>
<bean id="MysqlImpl" class="com.xiaolun.dao.UserDaoMysqlImpl"></bean>
<bean id="UserServiceImpl" class="com.xiaolun.service.UserServiceImpl">
    <property name="userDao" ref="Impl"></property>
</bean>
```

**2、测试类**

```java
public class MyTest {
    public static void main(String[] args) {

        ApplicationContext context = new ClassPathXmlApplicationContext("beans2.xml");
        UserServiceImpl userServiceImpl = (UserServiceImpl) context.getBean("UserServiceImpl");
        userServiceImpl.getUser();
    }
}
```

+ 控制台出现报错信息

```java
java.io.FileNotFoundException: class path resource [beans2.xml] cannot be opened because it does not exist
```

重要原因是项目结构有问题，在这里未解决。

### 2.3.3 总结

控制：谁来控制对象的创建，传统应用程序的对象是由程序本身控制创建的，使用Spring后，对象由Spring来创建的。

反转：程序本身不创建对象，而变成被动的接收对象。

依赖注入：通过set方法来进行注入的。

IOC是一种编程思想，由主动的编程变成被动的接收。我们可以通过 `ClassPathXmlApplicationContext`去浏览一下底层的源码。

到了现在，要实现不同的操作，我们不需要在程序中改动了，只需要在XML配置文件中进行修改。所谓IOC,就是：对象由Spring来创建，管理和装配。

## 2.4 IOC创建对象的方式

### 2.4.1 无参构造

1、程序默认使用无参构造器创建对象，所以我们在写有参构造器的时候，应该也需要将无参构造器添加上。

### 2.4.2 有参构造

**1、 创建实体类**

```java
public class User {
    private String name;

    public User(String name) { //有参构造器
        this.name = name;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
    public void show(){
        System.out.println("name="+name);
    }
}
```

**2、XML配置文件**

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

        <!--方式1：通过下标赋值-->
<!--        <bean id="user" class = "com.xiaolun.pojo.User">-->
<!--            <constructor-arg index="0" value="xiaolun"></constructor-arg>-->
<!--        </bean>-->

        <!--方式2：通过类型赋值，不建议使用-->
<!--        <bean id="user" class = "com.xiaolun.pojo.User">-->
<!--            <constructor-arg type="java.lang.String" value="xiaolun"></constructor-arg>-->
<!--        </bean>-->

        <!--方式3：直接通过参数名赋值-->
            <bean id="user" class = "com.xiaolun.pojo.User">
                <constructor-arg name="name" value="xiaolun"></constructor-arg>
            </bean>

</beans>
```

**3、测试类**

```java
public class MyTest {
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
        User user = (User) context.getBean("user");
        user.show();
    }
}
```

+ 控制台输出

```java
name=xiaolun
```

+ 当我们在xml文件中写多个实体类的时候，在配置加载的时候，容器中管理的对象就已经初始化了。

## 2.5 Spring配置说明

### 2.5.1 别名

**1、XML文件配置**

```xml
 <bean id="user"  class = "com.xiaolun.pojo.User">
            <constructor-arg name="name" value="xiaolun"></constructor-arg>
        </bean>
        <!--设置别名-->
        <alias name="user" alias="user_alias"></alias>
```

**2、测试类**

```java
 public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
        User user = (User) context.getBean("user_alias"); //别名
        user.show();
 }
```

### 2.5.2 import

**1、XML文件结构**

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901083843.png" alt="image-20200602085521019" style="zoom: 80%;" />

**2、applicationContext.xml文件**

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

<!--将所有的xml文件导入这个总包中-->
<import resource="beans.xml"></import>
<import resource="beans2.xml"></import>
</beans>
```

**总结**

`import`一般用于团队开发中使用，可以将多个配置文件，导入合并成一个，在使用的时候，直接使用总的配置就行了。

## 2.6 尚硅谷相关内容介绍

1. `IOC`容器包含的内容

+ `IOC`底层原理
+ `IOC`容器的两种实现方式：通过两个接口(`BeanFactory,ApplicationContext`)
+ `IOC`操作，`Bean`管理（基于`XML`/注解方式）

### 2.6.1 `IOC`底层原理

使用到了`xml`文件解析，工厂模式，反射。

1. **原始方式创建对象**

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901083844.png" alt="image-20200626095914714" style="zoom:80%;" />

可以发现，上面两个类之间的耦合度太高了。

2. **工厂模式创建对象**

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901083845.png" alt="image-20200626100123396" style="zoom:80%;" />

可以发现，`UserService`类和`UserDao`类之间进行了解耦，但是`UserService`类和工厂`UserFactory`之间耦合度还是很大。

3. **`IOC`过程**

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901083846.png" alt="image-20200626100508721" style="zoom:80%;" />

可以发现，耦合度进一步降低，加入`class=""（UserDao类发生变化）`路径发生变化，在`UserFactory`中的`classValue`的值获得的还是正确的路径。

### 2.6.2 `IOC`接口

`IOC`思想基于`IOC`容器完成，`IOC`容器底层就是对象工厂。

Spring提供`IOC`容器实现的两种方式，`BeanFactory,ApplicationContext`。

 **`BeanFactory`**

该接口是Spring内部的使用接口，不提供开发人员进行使用。

在加载配置文件的时候不会创建对象，只有在获取对象（使用）的时候才会去创建对象。

```java
//ApplicationContext在这一步创建对象。
ApplicationContext context = new ClassPathXmlApplicationContext("beans2.xml");

//BeanFactory在这一步创建对象
UserServiceImpl userServiceImpl = (UserServiceImpl) context.getBean("UserServiceImpl");
```

 **`ApplicationContext`**

是`BeanFactory`的子接口，提供更为强大的功能，提供给开发人员进行使用。

在加载配置文件的时候就会把配置文件中的对象创建。

### 2.6.3 `IOC`操作，`Bean`管理

`Bean`管理指的就是两个操作，一个是`Spring`创建对象，另一个是`Spring`注入属性。

**基于xml方式**

1. 创建对象

```xml
<!--创建UserDaoImpl对象
id:唯一标识
class:类全路径
-->
<bean id="Impl" class="com.xiaolun.dao.UserDaoImpl"></bean>
```

也就是在Spring的配置文件中，使用bean标签，就可以实现对象的创建。

注意：

在创建对象的时候，默认执行无参构造方法来创建对象，因此，当我们在实体类中定义有参构造方法的时候，我们需要将无参构造也要加上。

2. 注入属性

在原始的方式中，我们有两种方式来实现，分别是：一种使用set方式进行注入，一种是使用有参构造方法进行注入。这两种方式都是Spring推荐的。

方式1：配置文件中的**set方式**注入属性：

```xml
<!--set方式注入
使用property属性完成属性注入
name:类里面属性的值。
value:向属性注入的值。
-->
<bean id="hello" class="com.xiaolun.pojo.Hello">
        <property name="str" value="spring"></property>
    </bean>
```

方式2：配置文件中的**有参构造方式**注入属性参考2.4小节即可。

### 2.6.4 FactoryBean

Spring中有两种类型的bean,一种是普通的bean，一种是工厂bean(FactoryBean)。其中普通bean就是上文我们在xml配置文件中书写的bean，如下：

```xml
<bean id="Impl" class="com.xiaolun.dao.UserDaoImpl"></bean>
```

在xml文件中class属性就是它的返回类型，在测试类中接收，但是工厂bean却可以与返回类型不一致。我们需要在对应的类上重写一些方法。步骤如下：

1. 创建类，让这个类作为工厂bean，实现接口FactoryBean。
2. 实现接口中的方法，在实现的方法中定义返回的bean类型。

1. **实体类**

```java
//实体类MyBean
public class MyBean implements FactoryBean<User> {

    //返回对象的类型
    public User getObject() throws Exception {
        //定义的是MyBean类型，返回的为User类型。
        return new User("xiaohei"); //将会在控制台输出
    }

    public Class<?> getObjectType() {
        return null;
    }

    //是否是一个单例
    public boolean isSingleton() {
        return false;
    }
}
```

```java
//实体类User
public class User {
    private String name;
    
    public User(String name) { //有参构造器
        this.name = name;
    }

    public String getName() {
        return name;
    }
}
```

2. **测试类**

```java
public class MyTest {
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
//        MyBean bean = context.getBean("mybean", MyBean.class);
//        System.out.println(bean);
        User bean = context.getBean("mybean", User.class);  //返回的是User类型的对象
        System.out.println(bean.getName());
    }
}
```

+ 使用注释的方法，会报错

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901083847.png" alt="image-20200626111547760" style="zoom:80%;" />

+ 使用上文未注释方法，控制台输出为

```ini
xiaohei
```



# 3 DI

## 3.1 构造器注入

前文已讲述。

依赖：bean对象的创建依赖于容器。注入：bean对象的所有属性，由容器注入。也就是把底层类作为参数传递给上层类，实现上对下的控制。例子：老板对员工说：不要来找我，我会去找你的。

## 3.2 Set方式注入

### 3.2.1 创建项目

**1、实体类**

1.  Student类

```java
public class Student { 
    private String name;
    private Address adddress; //引用对象
    private String[] books;
    private List<String> hobbys;
    private Map<String,String> card;
    private Set<String> games;
    private String wife; //设置为null
    private Properties info; //配置类

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Address getAdddress() {
        return adddress;
    }

    public void setAdddress(Address adddress) {
        this.adddress = adddress;
    }

    public String[] getBooks() {
        return books;
    }

    public void setBooks(String[] books) {
        this.books = books;
    }

    public List<String> getHobbys() {
        return hobbys;
    }

    public void setHobbys(List<String> hobbys) {
        this.hobbys = hobbys;
    }

    public Map<String, String> getCard() {
        return card;
    }

    public void setCard(Map<String, String> card) {
        this.card = card;
    }

    public Set<String> getGames() {
        return games;
    }

    public void setGames(Set<String> games) {
        this.games = games;
    }

    public String getWife() {
        return wife;
    }

    public void setWife(String wife) {
        this.wife = wife;
    }

    public Properties getInfo() {
        return info;
    }

    public void setInfo(Properties info) {
        this.info = info;
    }

    @Override
    public String toString() {
        return "Student{" +
                "name='" + name + '\'' +
                ", adddress=" + adddress.toString()+
                ", books=" + Arrays.toString(books) +
                ", hobbys=" + hobbys +
                ", card=" + card +
                ", games=" + games +
                ", wife='" + wife + '\'' +
                ", info=" + info +
                '}';
    }
}

```

2. Address引用类

```java
public class Address {
    private String address;

    public String getAddress() {
        return address;
    }

    public void setAddress(String address) {
        this.address = address;
    }

    @Override
    public String toString() {
        return "Address{" +
                "address='" + address + '\'' +
                '}';
    }
}
```

**2、XML配置文件**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

        <bean id="address"  class = "com.xiaolun.pojo.Address">
            <property name="address" value="河南"></property>
        </bean>

        <bean id="student"  class = "com.xiaolun.pojo.Student">
            <!--第一种，普通值注入，value -->
           <property name="name" value="xiaolun"></property>

            <!--第二种，bean注入，ref -->
            <property name="adddress" ref="address"></property>

            <!--第三种，数组注入-->
            <property name="books">
                <array>
                    <value>《红楼梦》</value>
                    <value>《西游记》</value>
                    <value>《三国演义》</value>
                </array>
            </property>

            <!--第四种，List注入-->
            <property name="hobbys">
                <list>
                    <value>听歌</value>
                    <value>看电影</value>
                </list>
            </property>

            <!--第五种，Map注入-->
            <property name="card">
               <map>
                   <entry key="身份证" value="123456"></entry>
                   <entry key="银行卡" value="51354"></entry>
               </map>
            </property>

            <!--第六种，Set注入-->
            <property name="games">
                <set>
                    <value>魂斗罗</value>
                    <value>穿越火线</value>
                </set>
            </property>

            <!--第七种，null注入-->
            <property name="wife">
               <null></null>
            </property>

            <!--第八种，Properties注入-->
            <property name="info">
                <props>
                    <prop key="学号">263977</prop>
                    <prop key="性别">男性</prop>
                    <prop key="username">root</prop>
                    <prop key="password">123</prop>
                </props>
            </property>

        </bean>
</beans>
```

**3、测试类**

```java
public class MyTest {
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
        Student student = (Student) context.getBean("student");
        System.out.println(student.toString());
    }
}
```

+ 控制台输出结果

```java
Student{
	name='xiaolun',
	adddress=Address{address='河南'}, 
	books=[《红楼梦》, 《西游记》, 《三国演义》], 
	hobbys=[听歌, 看电影], 
	card={
		身份证=123456, 
		银行卡=51354
		}, 
	games=[魂斗罗, 穿越火线], 
	wife='null', 
	info={
		学号=263977, 
		性别=男性, 
		password=123, 
		username=root
		}
	}
```

## 6.3 拓展方式注入

**1、实体类**

```java
public class User {
    private String name;
    private int age;

    //p命名空间使用
    public User() {
    }

    //c命名空间使用
    public User(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}
```

**2、XML文件**

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xmlns:c="http://www.springframework.org/schema/c"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

        <!--p（property）命名空间注入，可以直接注入属性的值(需要添加get/set方法，需要添加无参数的构造方法)-->
        <bean id="user" class="com.xiaolun.pojo.User" p:name="xiaolun" p:age="18">
        </bean>

        <!--c（constructs）命名空间注入，可以直接注入属性的值（添加有参构造器）-->
        <bean id="user2" class="com.xiaolun.pojo.User" c:name="xiaolun" c:age="18">
        </bean>
</beans>
```

+ `xmlns:p/c`分别对应的是p/c空间的添加的第三方的xml文件，这样做可以简化xml配置方式。

**3、测试类**

```java
public class MyTest {
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("userbeans.xml");
        User user = context.getBean("user2", User.class);  //添加上class就不使用类型的强制转化了
        System.out.println(user.toString());
    }
}
```

+ 控制台输出

```java
User{name='xiaolun', age=18}
```

# 4 Bean

## 4.1 Bean的作用域

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901083848.png" alt="image-20200602103455970" style="zoom:150%;" />

| 作用域类型    | 使用范围       | 描述                                                         |
| ------------- | -------------- | ------------------------------------------------------------ |
| singleton     | 所有Spring应用 | 默认值，IOC容器只存在单例。                                  |
| prototype     | 所有Spring应用 | 每当从IOC容器汇总取出一个Bean，则创建一个新的Bean。（多实例对象） |
| session       | Spring Web应用 | HTTP会话。                                                   |
| application   | Spring Web应用 | Web工程生命周期。                                            |
| request       | Spring Web应用 | Web工程单次请求(request)。                                   |
| globalSession | Spring Web应用 | 在一个全局的HTTP Session种，一个Bean定义对应一个实例。实践中基本不使用。 |

1. 在Spring里面，设置创建的bean实例是单实例还是多实例。Spring的默认机制是单实例对象。原型模式是多实例模式。而其余的request、session、application这些只能在web开发中使用到。
2. 单例模式和原型模式区别：

当设置scope属性值为`singleton`的时候，加载spring配置文件的时候就会创建单实例对象；当设置scope属性值为`prototype`的时候，不是在加载spring配置文件的时候创建对象，而是在调用getBean方法的时候去创建一个多实例对象。

**配置XML文件**

```xml
<!--scope属性定义bean的作用域
本文设置成多实例模式
-->
<bean id="mybean" class="com.xiaolun.pojo.MyBean" scope="prototype">
</bean>
```

**测试**

```java
public class MyTest {
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
        MyBean bean = context.getBean("mybean", MyBean.class);
        MyBean bean2 = context.getBean("mybean", MyBean.class);
        System.out.println(bean.hashCode());
        System.out.println(bean2.hashCode());
    }
}
```

+ 控制台输出。可以发现，两个对象不同，即为多实例模式。

```ini
60559178
395629617
```

## 4.2 自动装配Bean

Spring会在上下文中自动寻找，并自动给bean装配属性。

在Spring中有三种装配方式：

+ 在xml文件中显式配置（我们现在使用的）。
+ 在java中显式配置。
+ 隐式的自动装配（本章所讲的，重点掌握）。

### 4.2.1 创建项目

描述：一个人有两个宠物，根据这句话有三个对象进行建模。

**1、实体类**

Cat类

```java
public class Cat {
    public void shout(){
        System.out.println("miao----->miao");
    }
}
```

Dog类

```java
public class Dog {
    public void shout(){
        System.out.println("wang----->wang");
    }
}
```

People类

```java
public class People {
    private Cat cat;
    private Dog dog;
    private String name;

    public Cat getCat() {
        return cat;
    }

    public void setCat(Cat cat) {
        this.cat = cat;
    }

    public Dog getDog() {
        return dog;
    }

    public void setDog(Dog dog) {
        this.dog = dog;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return "People{" +
                "cat=" + cat +
                ", dog=" + dog +
                ", name='" + name + '\'' +
                '}';
    }
}
```

**2、XML配置文件**

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

        <bean id="cat" class="com.xiaolun.pojo.Cat"></bean>
        <bean id="dog" class="com.xiaolun.pojo.Dog"></bean>

            <!--方法1：普通方式注入-->
<!--        <bean id="people" class="com.xiaolun.pojo.People">-->
<!--            <property name="name" value="xiaolun" ></property>-->
<!--            <property name="dog" ref="dog" ></property>-->
<!--            <property name="cat" ref="cat" ></property>-->
<!--        </bean>-->

        <!--方法2：使用byName方式注入
            系统会自动在容器上下文中寻找，和自己对象set方法后面的参数值对应的<bean>中的id属性的值
        -->
<!--        <bean id="people" class="com.xiaolun.pojo.People" autowire="byName">-->
<!--        </bean>-->

        <!--方法3：使用byType方式注入
        系统会自动在容器上下文中寻找，和自己对象属性类型想相对应的<bean>，且保证xml文件中<bean>（class="com.xiaolun.pojo.Cat"）类型全局唯一。
        此时，可以将上面的<bean id="cat" class="com.xiaolun.pojo.Cat"></bean>中的 id="cat" 给去掉。
        -->
        <bean id="people" class="com.xiaolun.pojo.People" autowire="byType">
        </bean>
</beans>
```

**3、测试**

```java
public class MyTest {
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
        People people = context.getBean("people", People.class);
        people.getCat().shout();
        people.getDog().shout();
    }
}
```

+ 控制台输出

```java
miao----->miao
wang----->wang
```

**4、总结**

+ 使用`byName`关键字进行自动装配的时候，需要保证`bean`的`id`唯一，并且这个`bean`需要和自动注入的属性的`set`方法的值一致。
+ 使用`byType`关键字进行自动装配的时候，需要保证所有的`bean`的`class`唯一，并且这个`bean`需要和自动注入的属性的类型一样。

### 4.2.2 使用注解来实现自动装配-基于注解的属性注入

使用注解须知：

+ 导入约束
+ 配置注解的支持

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">
	<!--开启注解的支持-->
    <context:annotation-config/>

</beans>
```

#### 4.2.2.1 @Autowired使用

1. **beans.xml文件**

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

        <context:annotation-config/>

        <bean id="cat" class="com.xiaolun.pojo.Cat"></bean>
        <bean id="dog" class="com.xiaolun.pojo.Dog"></bean>
        <bean id="people" class="com.xiaolun.pojo.People" >
        </bean>
</beans>
```

+ 可以发现配置文件十分简介。

2. **People类**

```java
public class People {

    @Autowired  //添加注解（添加到属性这里，可以省去setxx的方法!!!）
    private Cat cat;

    @Autowired
    @Qualifier(value = "dog")
    private Dog dog;

    private String name;

    public Cat getCat() {
        return cat;
    }

    public void setCat(Cat cat) {
        this.cat = cat;
    }

    public Dog getDog() {
        return dog;
    }

    public void setDog(Dog dog) {
        this.dog = dog;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return "People{" +
                "cat=" + cat +
                ", dog=" + dog +
                ", name='" + name + '\'' +
                '}';
    }
}
```

+ 测试类不变，可以发现经过测试能够正常输出。

**总结**

1、使用`Autowired`注解我们可以不用编写set方法了，前提是自动装配的属性在容器中了，命名符合`byName`。

2、@Nullable：使用该注解标记某个字段，说明这个字段可以为`null`。

```java
public People(@Nullable String name){
    this.name = name;
}
```

3、`@Autowired(required = false)`,显式定义该对象，说明该对象可以为`null`。

4、 采用@Autowired如果存在多个实现类的情况下，@Qualifier注解通过资源名称确定唯一性。（`@Qualifier(value = "xxx")`(xxx对应beans.xml文件中的id值)，这样的话就可以指定一个唯一的bean对象注入。）

#### 4.2.2.2 @Resource使用

```java
    @Resource(name = "cat")
    private Cat cat;

    @Resource
    private Dog dog;
```

**小结**

@Autowired和@Resource注解的区别

+ 前者通过`byType`的方式实现，要求必须有这个对象。
+ 后者默认通过`byName`的方式实现，如果找不到名字，就通过`byType`实现，如果二者都不存在的情况下，就报错。

## 4.3 Bean的生命周期

**加载逻辑**

(根据我们的内容)转换BeanName（它能够识别的内容） → 从缓存中加载实例→ 实例化Bean →检查parentBeanFactory → 初始化依赖的Bean（和Bean相关的东西都要进行创建成功） → 创建Bean

从对象的创建到销毁的过程即为生命周期。一共包含七个步骤：

1. 通过构建器创建bean实例（无参数构造）。
2. 为bean的属性设置值和其他bean的引用（调用set方法）。
3. 把bean实例传递给bean后置处理器的方法。
4. 调用bean的初始化方法（需要进行配置初始化的方法）。
5. 把bean实例传递给bean后置处理器的方法。
6. 此时，对象已经获取了。
7. 当容器关闭的时候，调用bean销毁的方法（需要进行配置销毁的方法）。

### 4.3.1 创建项目

1. **实体类**

```java
public class MyBean  {
    private String name;

    public MyBean() {
        System.out.println("第一步，执行无参构造方法创建bean实例");
    }

    public void setName(String name) {
        this.name = name;
        System.out.println("第二步，执行set方法设置属性值");
    }

    //创建执行的初始化方法
    public void initMethod(){
        System.out.println("第四步，执行初始化方法");
    }

    //创建执行的销毁方法
    public void destoryMethod(){
        System.out.println("第七步，执行销毁的方法");
    }
}
```

2. **实体类配置**

```xml
<bean id="mybean" class="com.xiaolun.pojo.MyBean" init-method="initMethod" destroy-method="destoryMethod">
</bean>
```

3. **配置后置处理器**

```java
public class MyBeanPost implements BeanPostProcessor {
    public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
        System.out.println("第三步进行初始化之前的方法");
        return bean;
    }

    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        System.out.println("第五步，进行初始化之后的方法");
        return bean;
    }
}
```

4. **后置处理器配置**

```xml
<!--  配置后置处理器
为当前所有的bean都添加后置处理器。
-->
<bean id="myBeanPost" class="com.xiaolun.pojo.MyBeanPost">
</bean>
```

5. **测试类**

```java
public class MyTest {
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
        MyBean bean = context.getBean("mybean", MyBean.class);
        System.out.println("第六步 获取创建bean的实例对象");

        //手动让bean实例销毁
        ((ClassPathXmlApplicationContext) context).close();
    }
}
```

+ 控制台输出

```ini
第一步，执行无参构造方法创建bean实例
第三步进行初始化之前的方法
第四步，执行初始化方法
第五步，进行初始化之后的方法
第六步 获取创建bean的实例对象
第七步，执行销毁的方法
```

# 5 Spring 注解开发

上文的第2/3节（IOC/DI）中，使用的是基于xml的注解的开发。

**开发步骤**

1. 引入依赖

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901083849.png" alt="image-20200626123918187" style="zoom:67%;" />

2. 开启组件扫描

扫描指定位置的注解，有注解的话就创建实例。

3. 创建类，在类上添加创建对象的注解（四个注解中的任何一个都可以）。

4. 测试即可。

## 5.1 @Component注解

1. **XML文件**

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

    <!--开启组件扫描，指定要扫描的包，这个包下的注解就会生效
        这也是这个xml文件存在的原因吧。
    -->
    <context:component-scan base-package="com.xiaolun"></context:component-scan>
    <context:annotation-config/>
    <!--
        1.当有多个包需要扫描时，将包写出来用逗号隔开即可。
        2.扫描包上目录
     -->
</beans>
```

+ 设置扫描包的内容：

```xml
<!--
        1.设置要扫描的注解。
        2.use-default-filters 表示不想使用默认filter，自己设置filter。
        3.context:include-filter:设置要扫描的内容。
    -->
    <context:component-scan base-package="com.xiaolun" use-default-filters="false">
        <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
    </context:component-scan>

    <!--
      1.设置要扫描的注解,要扫描包中的所有内容
      2.context:exclude-filter:设置不想扫描的内容。
  -->
    <context:component-scan base-package="com.xiaolun" >
        <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
    </context:component-scan>
```

+ 可以扫描到`com.xiaolun`下的dao,controller,pojo和service包。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901083850.png" alt="image-20200603094131336" style="zoom:80%;" />

2. **实体类**

```java
@Component //注入bean的方式等价于    <bean id="user" class="com.xiaolun.pojo.User"></bean>
public class User {
    
    @Value("xiaolun")  //等价于<property name = "name" value = "xiaolun">
    private String name;

    public String getName() {
        return name;
    }
}
```

```java
//注意：
@Component(value = "test")
public class User {}
/**
*(value = "test")可以省略不写，此时默认值为（类名称+首字母小写）。
*/
```

3. **测试类**

```java
public class MyTest {
    public static void main(String[] args) {
        //还没有完全使用注解开发（加载配置文件）
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
        //此时的id,为默认类名首字母小写
        User user = context.getBean("user", User.class);
        System.out.println(user.getName());
    }
}
```

+ 控制台输出

```java
xiaolun
```

## 5.2 衍生注解

web开发中，通常使用mvc三层架构模式，对应@Component有几个衍生注解。

+ dao层 (@Repository) 用于持久层，数据访问层，是用来直接访问数据库的。

```java
@Repository
public class UserDao {
}
```

+ service层(@Service) 用于服务层，处理业务逻辑。

```java
@Service
public class UserService {
}
```

+ controller层（@Controller） 用于呈现层（Web层），（Spring-MVC）,暴露给前端的入口。

```java
@Controller
public class UserController {
}
```

这四个注解的功能是一样的，都是代表将某个类注册到Spring中进行装配。这也就是尚硅谷所讲到的，Spring针对Bean管理中创建对象提供的四种注解。

**小结**

+ XML适用于更多场合，维护更方便，注解维护起来相对复杂。
+ 最佳实践：XML管理bean，注解完成属性的注入。

## 5.3 完全注解开发

完全不使用Spring的xml配置了，全部交给Java来做。

1. **实体类**

```java
@Component //该类被注册到容器中，别IOC托管。
public class User {
    @Value("xiaolun") //输入普通类型属性
    private String name;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                '}';
    }
}
```

2. **配置类**

主配置类，代替xml配置文件。

```java
//这个类也会被Spring托管，进而注册到容器中，因为该注解本来就是@Component
@Configuration //代表配置类的注解，替代bean.xml文件。
//@ComponentScan("com.xiaolun.pojo")  //也可以扫描包(显式扫描)，开启注解扫描
@Import(LunConfig2.class) //将第二个配置类融合到这里来
public class LunConfig {

    /**
     * 注册一个bean，相当于之前写的一个bean标签。
     * 方法名字getUser相当于bean标签中的id属性
     * 方法返回值相当于bean标签中中的class属性
     * @return
     */
    @Bean
    public User getUser(){
        return new User();
    }
}
```

配置类2

```java
@Configuration
public class LunConfig2 {
}
```

3. **测试类**

```java
public class MyTest {
    public static void main(String[] args) {
        //如果完全使用配置类的方式去做，只能通过 AnnotationConfig 上下文来获取容器，通过配置类的calss来加载。和之前的配置文件一样。
        ApplicationContext context = new AnnotationConfigApplicationContext(LunConfig.class);
        User user = context.getBean("getUser", User.class);
        System.out.println(user.getName());
    }
}
```

+ 控制台输出结果

```java
xiaolun
```



# 6 AOP

## 6.1 介绍

AOP(Aspect Oriented Programming)面向切面编程，通过预编译方式和运行期间动态代理实现程序功能的统一维护的一种技术。AOP是OOP的延续，利用AOP可以对业务逻辑的各个部分进行隔离，从而使业务逻辑各部分之间的耦合度降低，提高程序的可重用性。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901083851.png" alt="image-20200603183742704" style="zoom:80%;" />

通俗描述，就是不通过修改源代码的方式，在主干功能中添加新功能。比如在登录的主干功能中添加权限判断的功能。

**底层原理**是使用动态代理的方式来增强类中某个方法的功能。具体的话，参考动态代理模式。 

**AOP操作术语**

提供声明式事务，允许用户自定义切面。

+ 横切关注点：跨越应用程序多个模块的方法或功能。即与我们业务逻辑无关，但是我们需要关注的部分，如日志，安全和缓存等。
+ 切面（Aspect）:横切关注点被模块化的特殊对象，它是一个类（是一个动作，即把通知应用到切入点的过程）。
+ 通知（Advice）：切面必须要完成的工作，它是类中的一个点（实际增强的逻辑部分）。

通知有多种类型：

```ini
前置通知（增强方法执行之前会被执行）
后置通知
环绕通知（增强方法执行之前和之后都会被执行）
异常通知（增强方法执行过程中出现异常的通知）
最终通知（类似于finally）
```

+ 目标（Target）:被通知对象。
+ 代理（Proxy）：向目标对象应用通知后创建的对象。
+ 切入点（PointCut）:切面通知执行“地点”的定义（实际真正被增强的方法）。
+ 连接点（JointPoint）:与切入点匹配的执行点(一个类里面需要被增强的方法，我们称之为连接点)。

<img src="设计模式.assets/image-20200603184520834.png" alt="image-20200603184520834" style="zoom: 67%;" />

在AOP中，通过Advice定义横切逻辑，Spring中支持5种类型的Advice。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901083852.png" alt="image-20200603184800080" style="zoom: 50%;" />

#### 6.1.1 准备工作

​		Spring框架一般都是基于AspectJ实现的AOP操作。所谓AspectJ不是Spring的组成部分，是独立的一个AOP框架，一般把AspectJ和Spring框架一起使用，进行AOP操作。

**基于AspectJ实现的AOP操作**

1. 基于xml配置文件实现。
2. 基于注解的方式实现（推荐）。

**切入点表达式**

主要实现的是对一个类中的某个方法做一些增强。

1. 语法结构

```ini
# 格式
exexution（[权限修饰符][返回类型][类全路径][方法名称]([参数列表])）
#举例1 对com.xiaolun.proxy.UserDao类中的add()方法做增强。
#第一个*号表示：任意的修饰符（public/protected等都可以）
#第一个(..)号表示：方法中的所有参数
exexution(*com.xiaolun.proxy.UserDao.add(..))

#举例2 对com.xiaolun.proxy.UserDao类中的所有方法做增强。
exexution(*com.xiaolun.proxy.UserDao.*(..))

#举例3 对com.xiaolun.proxy包中的所有类，以及类中的所有方法做增强。
exexution(*com.xiaolun.proxy.*.*(..))
```

## 6.2 实现AOP

### 6.2.1 原生方式实现

**项目描述**、

只是纯粹的在原来业务基础上实现前/后面添加一句话。

1. **项目结构**

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901083853.png" alt="image-20200603214530434" style="zoom:80%;" />

2. **添加aop依赖**

```html
<dependencies>
    <dependency>
        <groupId>org.aspectj</groupId>
        <artifactId>aspectjweaver</artifactId>
        <version>1.9.5</version>
    </dependency>
</dependencies>
```

3. **业务接口**

```java
public interface UserService {
    void add();
    void delete();
    void update();
    void select();
}
```

4. **业务接口实现类**

```java
// @Component  注意，使用注解方式时，这里要加上的，原生接口实现不加。
public class UserServiceImpl implements UserService {
    public void add() {
        System.out.println("增加了一个用户");
    }

    public void delete() {
        System.out.println("删除了一个用户");
    }

    public void update() {
        System.out.println("更新了一个用户");
    }

    public void select() {
        System.out.println("查询了一个用户");
    }
}
```

5. **创建实现MethodBeforeAdvice接口的类**

```java
public class Log implements MethodBeforeAdvice {

    /**
     *
     * @param method 要执行的目标对象的方法
     * @param args 参数
     * @param target 目标对象
     * @throws Throwable
     */
    public void before(Method method, Object[] args, Object target) throws Throwable {
        System.out.println(target.getClass().getName()+"的---->"+method.getName()+"方法---->被执行了");
    }
}
```

+ 该方法会在切入点之前引用。

6. **创建实现AfterReturningAdvice接口的类**

```java
public class AfterLog implements AfterReturningAdvice {

    //返回值
    public void afterReturning(Object returnValue, Method method, Object[] args, Object target) throws Throwable {
        System.out.println("执行了------>"+ method.getName()+"方法，---->返回结果为"+ returnValue);
    }
}
```

7. **XML文件配置**

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        https://www.springframework.org/schema/aop/spring-aop.xsd">

    <!--注册bean-->
    <bean id="userService" class="com.xiaolun.service.UserServiceImpl"></bean>
    <bean id="log" class="com.xiaolun.log.Log"></bean>
    <bean id="afterLog" class="com.xiaolun.log.AfterLog"></bean>

    <!--方式1:使用原生的API接口-->
    <!--配置aop ：需要导入aop的约束-->
    <aop:config>
        <!--切入点expression:表达式execution（要执行的位置）
        给该类下com.xiaolun.service.UserServiceImpl.*(..)的所有方法中插入方法,(..)表示任意的参数
        执行代码时，具体到这个切入点去执行-->
        <aop:pointcut id="pointcut" expression="execution(* com.xiaolun.service.UserServiceImpl.*(..))" />

        <!--执行环绕增强
        将log这个类切入到pointcut这个方法上面-->
        <aop:advisor advice-ref="log" pointcut-ref="pointcut"></aop:advisor>
        <aop:advisor advice-ref="afterLog" pointcut-ref="pointcut"></aop:advisor>
    </aop:config>
</beans>
```

8. **测试类**

```java
public class MyTest {
    public static void main(String[] args) {
        //ClassPathXmlApplicationContext对应的是resources下的applicationContext.xml文件
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");

        //动态代理代理的是接口 UserService
        UserService userService = context.getBean("userService", UserService.class);
        userService.add();
    }
}
```

+ 控制台输出结果

```java
com.xiaolun.service.UserServiceImpl的---->add方法---->被执行了
增加了一个用户
执行了------>add方法，---->返回结果为null
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901083854.png" alt="image-20200627001119319" style="zoom:67%;" />

### 6.2.2 xml方式实现

1. **项目结构**

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901083855.png" alt="image-20200603220554276" style="zoom:80%;" />



2. **创建自定义类**

```java
public class DiyPointCut {
    public void before(){
        System.out.println("----------方法执行前---------");
    }

    public void after(){
        System.out.println("----------方法执行后---------");
    }
}
```

3. **XML文件配置**

```java
<bean id="diy" class="com.xiaolun.diy.DiyPointCut"></bean>

<!--配置切面-->  
<aop:config>
    
     <!--切入点-->
     <aop:pointcut id="pointTest" expression="execution(* com.xiaolun.service.UserServiceImpl.*(..))"/>
         
    <!--配置切面，ref表示要引用的类（增强类）-->
	<aop:aspect ref="diy">
          <!--增强作用在具体的方法上，method对应diy类中的方法-->
         <aop:before method="before" pointcut-ref="pointTest"></aop:before>
          <aop:after method="after" pointcut-ref="pointTest"></aop:after>
              
     </aop:aspect>
 </aop:config>
```

+ 控制台输出

```java
----------方法执行前---------
增加了一个用户
----------方法执行后---------
```

### 6.2.3 完全注解方式配置实现

```java
//@EnableAspectJAutoProxy 等价于 <aop:aspectj-autoproxy></aop:aspectj-autoproxy>
@Configuration
@ComponentScan(basePackages = {"com.xiaolun"})
@EnableAspectJAutoProxy(proxyTargetClass = true)
public class ConfigAop {
}
```

### 6.2.4 注解方式实现（推荐）

**步骤**：

1. 创建类，在类中定义方法。

分为两种情况，第一种是创建的是一个实体类对象，另一种创建的是一个实现接口的对象（面向接口编程），本文采用后者。业务接口和业务接口实现类参考原生方式。

2. 创建增强类，编写增强逻辑。

```java
/**
 * 1.AnnotationPointCut为增强的类。
 * 2.在增强类上添加该注解@Order(数字类型值)，该值越小表示越小及越高。
 * 这样的话，对于有多个增强类对同一歌方法进行增强，可以设置相应的优先级。
 */
@Component //第3步才加上的。
@Aspect
@Order(1) 
public class AnnotationPointCut {

    //相同切入点抽取
    @Pointcut(value = "execution(* com.xiaolun.service.UserServiceImpl.*(..))")
    public void pointDemo(){

    }

    //在增强类里面，添加通知类型注解和切入点表达式。
    //前置通知(这里使用了公共点进行测试)
    @Before(value = "pointDemo()")
    public void before(){
        System.out.println("----------------Before----------");
    }

    //最终通知（表示在需要增强的方法执行之后进行执行，在@AfterReturning之前）
    @After(value = "pointDemo()")
    public void after(){
        System.out.println("----------------After----------");
    }

    //返回通知（表示需要增强的方法在返回结果之后就执行）
    @AfterReturning(value = "execution(* com.xiaolun.service.UserServiceImpl.*(..))")
    public void AfterReturning(){
        System.out.println("----------------AfterReturning----------");
    }

    //异常通知
    @AfterThrowing( value = "execution(* com.xiaolun.service.UserServiceImpl.*(..))")
    public void AfterThrowing(){
        System.out.println("----------------AfterThrowing----------");
    }

    //环绕通知，在环绕增强中，我们给定一个参数，代表我们要获取的处理接入的点
    @Around(value = "execution(* com.xiaolun.service.UserServiceImpl.*(..))")
    public void around(ProceedingJoinPoint jp) throws Throwable {
        System.out.println("环绕前");

        //执行方法
        jp.proceed();

        System.out.println("环绕后");
    }
}
```

3. 进行通知的配置

（1）在Spring配置文件中，开启注解扫描。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        https://www.springframework.org/schema/aop/spring-aop.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

    <!--采用注解方式创建bean，在这里开启注解扫描-->
    <context:component-scan base-package="com.xiaolun"></context:component-scan>
```

（2）使用注解创建接口实现类（或者实体类对象）和接口实现类的增强类对象。

```java
@Component //接口实现类
public class UserServiceImpl implements UserService {}
```

```java
@Component //增强类对象
@Aspect //生成代理对象
public class AnnotationPointCut {}
```

（3）在增强类中添加注解`@Aspect`。

（4）在Spring配置文件中开启生成代理对象。

```xml
<!--开启Aspect生成代理对象-->
<aop:aspectj-autoproxy></aop:aspectj-autoproxy>
```

4. 测试

```java
//动态代理代理的是接口 UserService,由于使用注解方式创建对象，所以UserServiceImpl的对象名称为userServiceImpl，而不是原生方式实现用的参数UserService（他们在xml文件中配置的是这个）
//假如创建的是一个实体类，我们直接就这样书写：context.getBean("user", User.class);
UserService userService = context.getBean("userServiceImpl", 				UserService.class);
userService.add();
```

+ 控制台输出（注意执行顺序）

```java
环绕前
----------------Before----------
增加了一个用户
环绕后
----------------After----------
----------------AfterReturning----------
```

# 7 事务

事务是数据库操作的基本单元，是逻辑上的一组操作，要么操作都成功，假如有一个失败时所有操作都失败。



