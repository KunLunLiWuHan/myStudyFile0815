# 1 快速开始

**介绍**

官网：

```http
https://mp.baomidou.com/
```

MyBatis-Plus（简称 MP）是一个 MyBatis的增强工具，在 MyBatis 的基础上只做增强不做改变，为简化开发、提高效率而生。

**步骤**

1. 导入对应的依赖。
2. 研究依赖如何配置。
3. 代码如何编写。
4. 提高扩展技术能力。

1. **创建User表**

```sql
DROP TABLE IF EXISTS user;

CREATE TABLE user
(
	id BIGINT(20) NOT NULL COMMENT '主键ID',
	name VARCHAR(30) NULL DEFAULT NULL COMMENT '姓名',
	age INT(11) NULL DEFAULT NULL COMMENT '年龄',
	email VARCHAR(50) NULL DEFAULT NULL COMMENT '邮箱',
	PRIMARY KEY (id)
);
-- 真实开发中，下面几个字段是必须拥有的：version(乐观锁)、deleted(逻辑删除)、gmt_create(创建时间)、gmt_modified(更改)。

-- 插入数据
INSERT INTO user (id, name, age, email) VALUES
(1, 'Jone', 18, 'test1@baomidou.com'),
(2, 'Jack', 20, 'test2@baomidou.com'),
(3, 'Tom', 28, 'test3@baomidou.com'),
(4, 'Sandy', 21, 'test4@baomidou.com'),
(5, 'Billie', 24, 'test5@baomidou.com');
```

2. **编写pom文件**

```html
  <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>

        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <scope>runtime</scope>
        </dependency>

        <!--03 mybatisplus 是自己开发的，并非官方的-->
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-boot-starter</artifactId>
            <version>3.0.5</version>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
```

说明：我们使用mybatis-plus可以节省大量的代码，尽量不要mybatis和mybatisplus同时导入。

3. **连接数据库**

```properties
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url= jdbc:mysql://localhost:3306/mybatisplus?serverTimezone=Asia/Shanghai & useUnicode=true & characterEncoding=UTF-8
spring.datasource.username=root
spring.datasource.password=123
```

在这里并没有配置相应的数据表，而是实体类的名字需要和数据库中的表明相对应，才能查到相应的表。

4. **连接mybatis**

**目录结构**

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831113203.png" alt="image-20200610223305130" style="zoom:80%;" />

传统的方式为`pojo-dao(连接mybatis，还有十分繁琐的配置mapper.xml文件)-service-controller`，现在的连接步骤如下：

（1）编写pojo

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class User {

    private Long id;
    private String name;
    private Integer age;
    private String email;

}
```

（2）mapper接口

```java
//在对应的mapper上面实现一个基本的接口BaseMapper就可以了
@Repository //代表持久层
public interface UserMapper extends BaseMapper<User> {
    //所有的CRUD已经编写完成了，此时不需要配置一大堆文件了
}
```

`BaseMapper`中传入的泛型为`User`对象，`BaseMapper`接口中封装了许多方法，可以对传入的对象进行操作。所以，在程序中看不到CRUD操作。（并没有在mapper中使用CRUD操作）

（3）测试

```java
@SpringBootTest
class MybatisPlusApplicationTests {

    /**
     * 继承了BaseMapper,所有的方法都来自父类。
     * 我们也可以编写自己的扩展方法
     */
    @Autowired
    private UserMapper userMapper;

    @Test
    void contextLoads() {
        /**
         * 查询所有用户
         * selectList中有一个参数Wrapper，称为条件构造器，为null时，查询全部的用户
         */
        List<User> userList = userMapper.selectList(null);
        for (User user:userList) {
            System.out.println(user);
        }
    }
}
```

在测试之前，需要在启动类上添加扫描文件操作，即：@MapperScan("com.xiaolun.mapper") ，此时控制台输出如下，表示可以将数据库中的数据全部查出来。

```java
User(id=1, name=Jone, age=18, email=test1@baomidou.com)
User(id=2, name=Jack, age=20, email=test2@baomidou.com)
User(id=3, name=Tom, age=28, email=test3@baomidou.com)
User(id=4, name=Sandy, age=21, email=test4@baomidou.com)
User(id=5, name=Billie, age=24, email=test5@baomidou.com)
```

注：不知道什么原因，在测试时加载jar包时间过长。

# 2 配置日志

所有的sql语句是不可见的，我们希望知道数据库是如何执行的，所以应该添加日志。

开发的时候可以加上日志，上线的时候需要删除日志，因为它浪费时间。

```properties
# 配置默认日志
mybatis-plus.configuration.log-impl=org.apache.ibatis.logging.stdout.StdOutImpl
```

再次测试，控制台输出：

![image-20200610223820462](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831113204.png)

配置完成之后，后面的学习就需要注意这个自动生成的SQL了。

# 3 CRUD扩展

## 3.1 Insert插入

```java
    @Test
    public void InsertTest(){
        User user = new User();
        user.setName("xiaolun");
        user.setAge(3);
        user.setEmail("3493826518@qq.com");
        int result = userMapper.insert(user); //集成的insert值，输入参数为对象
        System.out.println("result------->"+result);  //受影响的行数
        System.out.println("user-------->"+user); //id会自动回填
    }
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831113205.png" alt="image-20200611080348185" style="zoom:80%;" />

### 3.1.1 主键生成策略

```java
//主键策略
public enum IdType {
    AUTO(0),//数据库自增 依赖数据库
    NONE(1),// 表示该类型未甚至主键类型 （如果没有主键策略）默认根据雪花算法生成
    INPUT(2),//用户输入ID（该类型可以通过自己注册填充插件进行填充）
　　//下面这三种类型,只有当插入对象id为空时 才会自动填充。
    ID_WORKER(3),//全局唯一（idWorker）数值类型
    UUID(4),//全局唯一（UUID）
    ID_WORKER_STR(5);//全局唯一（idWorker的字符串表示）
```

```java
@TableId(type = IdType.ID_WORKER) //默认是推特的雪花算法,全局唯一（idWorker）数值类型。放到实体类的属性上。
```

**雪花算法**

分布式系统唯一id:

```http
https://www.cnblogs.com/haoxinyue/p/5208136.html
```

snowflake是Twitter开源的分布式ID生成算法，结果是一个long型的ID。其核心思想是：使用41bit作为毫秒数，10bit作为机器的ID（5个bit是数据中心，5个bit的机器ID），12bit作为毫秒内的流水号（意味着每个节点在每毫秒可以产生 4096 个 ID），最后还有一个符号位，永远是0。

**主键自增**

```java
@TableId(type = IdType.AUTO) //数据库自增 依赖数据库
```

此时数据库字段一定要设置成自增。

**主键手动输入**

```java
   @TableId(type = IdType.INPUT)
    private Long id;
```

一旦手动输入之后，就需要自己配置Id了。

## 3.2 update更新

```java
@Test //更新测试
public void testUpdate(){
  User user = new User();
  user.setId(5L); //将要更新的id值传进去
  //通过条件拼接动态SQL
  user.setName("xiaopang");
  //传进去的参数是一个对象
  int result = userMapper.updateById(user);
  System.out.println("user-------->"+user);
}
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831113206.png" alt="image-20200611083318814" style="zoom:80%;" />

### 3.2.1 自动填充

创建时间（`gmt_create`）和修改时间(`gmt_modified`)这两个字段几乎所有的表都要配置上，但是这些操作一般都是自动化完成的，我们不希望手动操作。

**方式1：数据库级别**

1. 在表中新增字段create_time update_time。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831113207.png" alt="image-20200611090708479" style="zoom:80%;" />

设置默认值：CURRENT_TIMESTAMP，就可以实现创建时为当前时间，但是更新时也为当前时间，且更新时创建时间不变。

2. **将实体类同步**

```java
private Date createTime;
private Date updateTime;
```

**方式2：代码级别**

1. 删除数据库的默认值。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831113208.png" alt="image-20200611091114210" style="zoom:80%;" />

2. **实体类字段上需要增加注解**

```java
 //插入填充字段
    @TableField(fill = FieldFill.INSERT)
    private Date createTime;  //启用了驼峰命名，可以和数据库字段进行匹配。
    //插入和更新填充字段
    @TableField(fill = FieldFill.INSERT_UPDATE)
    private Date updateTime;
```

3. **编写处理器处理这些注解**

```java
@Slf4j
@Component //一定不要忘记将处理器加到IOC容器中
public class MyMetaObjectHandler implements MetaObjectHandler {

    //插入时的填充策略
    @Override
    public void insertFill(MetaObject metaObject) {
        log.info("start insert fill .......");  //日志功能
//        default MetaObjectHandler setFieldValByName(String fieldName, Object fieldVal, MetaObject metaObject)
        this.setFieldValByName("createTime",new Date(),metaObject);
        this.setFieldValByName("updateTime",new Date(),metaObject);
    }

    //更新时的填充策略
    @Override
    public void updateFill(MetaObject metaObject) {
        this.setFieldValByName("updateTime",new Date(),metaObject);
    }
}
```

+ 进行测试时，可以完成更新和插入时的时间改变的操作。
+ 遇到`Date`类型插入到`mysql`中出现时间不一致的问题，是因为时区的问题，将.`properties`文件中的`serverTimezone`其改为`CTT（Asia&Shanghai）`即可。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831113209.png" alt="image-20200611093822334" style="zoom:80%;" />

### 3.2.2 乐观锁

乐观锁总是假设最好的情况，每次去拿数据的时候都认为别人不会修改，所以不会上锁，但是在更新的时候会判断一下在此期间别人有没有去更新这个数据，可以使用版本号机制和CAS算法实现。

悲观锁总是假设最坏的情况，每次去拿数据的时候都认为别人会修改，所以每次在拿数据的时候都会上锁，这样别人想拿这个数据就会阻塞直到它拿到锁（**共享资源每次只给一个线程使用，其它线程阻塞，用完后再把资源转让给其它线程**）。

**乐观锁实现方式**：

- 取出记录时，获取当前`version`。
- 更新时，带上这个`version`。
- 执行更新时， `set version = newVersion where version = oldVersion`。
- 如果`version`不对，就更新失败。

```sql
 -- 举例
 -- 先查询获得版本号 version = 1
 -- A 线程  (修改2号用户数据)
 update user set name = "xiaolun" ,version = version + 1 where id = 2 and version  = 1
 
  -- B 线程抢先完成，这个时候version = 2，导致A修改失败。
 update user set name = "xiaolun" ,version = version + 1 where id = 2 and version  = 1
  -- 这样做可以实现线程的安全。
```

**测试乐观锁插件**

1. **给数据库增加`version`字段**

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831113210.png" alt="image-20200611110505504" style="zoom:80%;" />

默认值设置为1就可以了。

2. **在实体类中添加字段**

```java
@Version //乐观锁
private Integer version;
```

3. **注册组件**

```java
// 将启动类的扫描加到这里
@MapperScan("com.xiaolun.mapper")
@EnableTransactionManagement
@Configuration //配置类
public class MybatisConfig {

    @Bean //注册乐观锁插件
    public OptimisticLockerInterceptor optimisticLockerInterceptor() {
        return new OptimisticLockerInterceptor();
    }
}
```

![image-20200611111302679](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831113211.png)

4. **测试**

```java
  @Test //更新成功的操作
    public void testOptimisticLocker(){
        //1.查询用户信息
        User user = userMapper.selectById(1L);
        //2.修改用户信息
        user.setName("xiaopang");
        user.setAge(24);
        //3. 执行更新操作
        userMapper.updateById(user);
    }
```

![image-20200611112017324](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831113212.png)



```java
    @Test //更新失败的操作
    public void testOptimisticLocker2(){

        //线程1
        User user = userMapper.selectById(1L);
        user.setName("xiaopang111");

        //模拟另一个线程执行了插队操作
        User user2 = userMapper.selectById(1L);
        user2.setName("xiaopang222");
        userMapper.updateById(user2);

        //线程1后来更新，这里会更新失败。即没有乐观锁，会覆盖插队线程的值。
        userMapper.updateById(user);
    }
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831113213.png" alt="image-20200611112623416" style="zoom:80%;" />

## 3.3 select查询

```java
@Test
public void testSelect(){
  //批量查询
  List<User> userList = userMapper.selectBatchIds(Arrays.asList(1, 2, 3));
  for (User user: userList) {
    System.out.println("user------>" + user);
  }
}

@Test //条件查询
public void testSelect2(){
  //自定义要查询的条件
  Map<String, Object> map = new HashMap<>();
  map.put("name", "xiaolun");
  List<User> userList = userMapper.selectByMap(map);
  for (User user: userList) {
    System.out.println("user------>" + user);
  }
}
```

### 3.3.1 分页查询

1、导入分页插件

```java
@Bean //配置分页插件(在MybatisConfig配置类中)
public PaginationInterceptor paginationInterceptor() {
  return new PaginationInterceptor();
}
```

2、测试

```java
@Test //分页
public void testPage(){
  //参数1：当前页；参数2：当前页。即第一页每页只需要3个数据
  Page<User> page = new Page<>(1,3);
  userMapper.selectPage(page,null);
  for (User record : page.getRecords()) {
    System.out.println(record);
  }
}
```

## 3.4 delete删除

**逻辑删除**

在数据库中没有被移除，而是通过一个变量来让它失效（只是丢入回收站）。让正常的查询查询不到它。

应用场景：管理员可以查看被删除的记录，防止数据丢失。类似于回收站。

1. **数据表中增加一个`deleted`字段**

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831113214.png" alt="image-20200611115615780" style="zoom:80%;" />

2. **实体类中增加相应字段**

```java
 @TableLogic //逻辑删除
 private Integer deleted;
```

3. **配置**

```properties
# 配置逻辑删除,没有删除为0 ，删除了为1
mybatis-plus.global-config.db-config.logic-delete-value= 1
mybatis-plus.global-config.db-config.logic-not-delete-value= 0
```

```java
    @Bean  //逻辑删除组件(拦截器)(在MybatisConfig配置类中)
    public ISqlInjector sqlInjector() {
        return new LogicSqlInjector();
    }
```

4. **测试**

```java
    @Test //测试删除
    public void testDelete(){
       userMapper.deleteById(3L);
    }
```

+ 走的是更新操作，并不是删除操作，此时控制台输出

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831113215.png" alt="image-20200611121310990" style="zoom:80%;" />

+ 数据表显示

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831113216.png" alt="image-20200611121347908" style="zoom:80%;" />

# 4 性能分析插件

MP提供性能分析插件，用于输出每条 SQL 语句及其执行时间，如果超过指定时间，就停止运行。

1. **导入插件**

```java
 @Bean //性能分析插件
    @Profile({"dev","test"})// 设置 dev test 环境开启
    public PerformanceInterceptor performanceInterceptor() {
        PerformanceInterceptor performanceInterceptor = new PerformanceInterceptor();
        performanceInterceptor.setFormat(true); //代码格式化
        performanceInterceptor.setMaxTime(1000); //ms 设置sql执行的最大时间，如果超过就停止执行
        return performanceInterceptor;
    }
```

```properties
# 设置开发环境
spring.profiles.active=dev
```

2. **测试**

```java
    @Test
    void contextLoads() {
        /**
         * 查询所有用户
         * selectList中有一个参数Wrapper，称为条件构造器，为null时，查询全部的用户
         */
        List<User> userList = userMapper.selectList(null);
        for (User user:userList) {
            System.out.println(user);
        }
    }
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831113217.png" alt="image-20200611122459207" style="zoom: 50%;" />

# 5 条件构造器 Wrapper

 1、 测试1

```java
@Test
public void Test1(){
  //查询name、邮箱字段不为空，而且年龄大于等于12的用户
  QueryWrapper<User> wrapper = new QueryWrapper<>();
  wrapper
    .isNotNull("name")
    .isNotNull("email")
    .ge("age", "12");
  List<User> userList = userMapper.selectList(wrapper);
  userList.forEach(System.out::println);
}
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831113218.png" alt="image-20200611160103477" style="zoom:80%;" />

2、测试2

```java
@Test
public void Test2(){
  //查询名字为xiaopang3的用户
  QueryWrapper<User> wrapper = new QueryWrapper<>();
  wrapper
    .eq("name","xiaopang3");
  //查询一个数据，出现多个结果使用List或者Map
  User user = userMapper.selectOne(wrapper);
  System.out.println(user);
}
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831113221.png" alt="image-20200611162802493" style="zoom:80%;" />

3、测试3

```java
 @Test
    public void Test3(){
        //查询年龄在20~30岁之间的用户
        QueryWrapper<User> wrapper = new QueryWrapper<>();
        wrapper
                .between("age", 10, 20);
        Integer count = userMapper.selectCount(wrapper);
        System.out.println("count------>"+count);
    }
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831113220.png" alt="image-20200611161824820" style="zoom:80%;" />

4、测试4

```java
 @Test //模糊查询
    public void Test4(){
        //名字中不含3的，且email中以t开头，右连接
        QueryWrapper<User> wrapper = new QueryWrapper<>();
        wrapper
                .like("name", "3")
                .likeRight("email", "t");
        List<User> userList = userMapper.selectList(wrapper);
        for (User user:userList) {
            System.out.println(user);
        };
    }
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831113219.png" alt="image-20200611160533816" style="zoom:80%;" />

**测试5**

```java
 @Test
    public void Test5(){
        //id 在子查询中查出来
        QueryWrapper<User> wrapper = new QueryWrapper<>();
        wrapper
                .inSql("id", "select id from user where id < 3");
        List<User> userList = userMapper.selectList(wrapper);
        for (User user:userList) {
            System.out.println(user);
        };
    }
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831113222.png" alt="image-20200611163052811" style="zoom:80%;" />

**测试6**

```java
@Test
    public void Test6(){
        //使用 id 进行排序
        QueryWrapper<User> wrapper = new QueryWrapper<>();
        wrapper
                .orderByDesc("id");
        List<User> userList = userMapper.selectList(wrapper);
        for (User user:userList) {
            System.out.println(user);
        };
    }
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831113223.png" alt="image-20200611163233223" style="zoom:80%;" />

# 6 代码自动生成器

1. **在pom文件中导入依赖**

```html
 <!--03 mybatisplus 是自己开发的，并非官方的-->
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-boot-starter</artifactId>
            <version>3.0.5</version>
        </dependency>

        <!--04 代码生成器需要依赖-->
        <dependency>
            <groupId>org.apache.velocity</groupId>
            <artifactId>velocity-engine-core</artifactId>
            <version>2.2</version>
        </dependency>
```

2. **在测试的包中书写main方法**

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831113224.png" alt="image-20200611171457359" style="zoom:80%;" />

```java
//代码自动生成器
public class generatorCode  {
    public static void main(String[] args) {
        //需要构建一个代码生成器对象
        AutoGenerator generator = new AutoGenerator();
        /**
         *  配置策略
         */

        //1. 全局配置
        GlobalConfig gc = new GlobalConfig();
        //需要获取当前项目的根目录
        String path = System.getProperty("user.dir");
        //代码生成位置
        gc.setOutputDir( path + "/src/main/java");
        gc.setAuthor("xiaolun");
        //是否打开资源管理器，设置成不打开
        gc.setOpen(false);
        //是否覆盖原来生成的，设置成不覆盖
        gc.setFileOverride(false);
        //去IService 的 I前缀
        gc.setServiceName("%sService");
        gc.setIdType(IdType.ID_WORKER);
//        gc.setSwagger2(true); //是否设置swagger文档
        generator.setGlobalConfig(gc);

        //2.配置数据源
        DataSourceConfig dataSourceConfig = new DataSourceConfig();
        dataSourceConfig.setUrl("jdbc:mysql://localhost:3306/mybatisplus?serverTimezone=CTT & useUnicode=true & characterEncoding=UTF-8");
        dataSourceConfig.setDriverName("com.mysql.cj.jdbc.Driver");
        dataSourceConfig.setUsername("root");
        dataSourceConfig.setPassword("123");
        generator.setDataSource(dataSourceConfig);

        // 3. 配置包
        PackageConfig pc = new PackageConfig();
        pc.setModuleName("blog");
        pc.setParent("com.xiaolun");
        pc.setEntity("entity");
        pc.setMapper("mapper");
        pc.setService("service");
        pc.setController("controller");
        generator.setPackageInfo(pc);

        // 4. 策略配置
        StrategyConfig strategy = new StrategyConfig();
        strategy.setInclude("user"); //设置要映射的表名
        strategy.setNaming(NamingStrategy.underline_to_camel);
        strategy.setColumnNaming(NamingStrategy.underline_to_camel);
        strategy.setEntityLombokModel(true);  //自动lombok

        strategy.setLogicDeleteFieldName("deleted");
        //自动填充策略
        TableFill create_time = new TableFill("create_time", FieldFill.INSERT);
        TableFill update_time = new TableFill("update_time", FieldFill.INSERT_UPDATE);
        ArrayList<TableFill> tableFills = new ArrayList<>();
        tableFills.add(create_time);
        tableFills.add(update_time);
        strategy.setTableFillList(tableFills);

        //乐观锁
        strategy.setVersionFieldName("version");
        generator.setStrategy(strategy);

        //执行
        generator.execute();
    }
}
```

+ 代码输出

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831113225.png" alt="image-20200611171525650" style="zoom:80%;" />

















