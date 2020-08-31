# SpringCloud Alibaba Seata处理分布式事务

## 1、介绍

1、产生原因

在分布式前，单机单库没问题。分布式以后，从1：1 -> 1:N -> N: N。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112756.png" alt="image-20200825090047341" style="zoom:50%;" />



<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112757.png" alt="image-20200825090055739" style="zoom:50%;" />

总结：一次业务操作需要跨多个数据源或需要跨多个系统进行远程调用，就会产生分布式事务问题。

2、概述

​		Seata是一款开源的分布式事务解决方案，致力于在微服务架构下提供高性能和简单易用的分布式事务服务。

官网地址：

```http
http://seata.io/zh-cn/
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112758.png" alt="image-20200825090323910" style="zoom:67%;" />

3、作用

（1）一个典型的分布式事务过程

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112759.png" alt="image-20200825090417756" style="zoom:67%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112800.png" alt="image-20200825090424225" style="zoom:67%;" />

（2）术语-分布式事务处理过程的-ID+三组件模型

Transaction ID XID：全局唯一的事务ID。

Transaction Coordinator(TC) ：事务协调器，维护全局事务的运行状态，负责协调并驱动全局事务的提交或回滚。

Transaction  Manager(TM) ：控制全局事务的边界，负责开启一个全局事务，并最终发起全局提交或全局回滚的决议。

Resource Manager(RM) ：控制分支事务，负责分支注册，状态汇报，并接收事务协调器的指令，驱动分支（本地）事务的提交和回滚。

4、下载地址

```http
https://github.com/seata/seata/releases
```

5、操作

（1）Spring 本地@Transactional

（2）全局@GlobalTransactional，其中SEATA的分布式交易解决方案如下图：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112801.png" alt="image-20200825090849185" style="zoom:67%;" />

## 2、Seata-Server安装

1、seata-server-1.3.0.zip解压到指定目录并修改conf目录下的file.conf配置文件。

（1）先备份原始file.conf文件。

（2）添加service模块

```ini
service {
  #修改自定义事务存储(默认),十分重要
  #vgroup_mapping.my_test_tx_group = "default"
  vgroup_mapping.my_test_tx_group = "fsp_tx_group"
  default.grouplist = "127.0.0.1:8091"
  enableDegrade = false
  disable = false
  max.commit.retry.timeout = "-1"
  max.rollback.retry.timeout = "-1"
  disableGlobalTransaction = false
}
```

（3）修改事务日志store存储模式为db

```ini
store {
  ## store mode: file、db、redis
  mode = "db"
}
```

（4）修改store中数据库连接信息

```ini
## database store property
  db {
    ## the implement of javax.sql.DataSource, such as DruidDataSource(druid)/BasicDataSource(dbcp)/HikariDataSource(hikari) etc.
    datasource = "druid"
    ## mysql/oracle/postgresql/h2/oceanbase etc.
    dbType = "mysql"
    driverClassName = "com.mysql.cj.jdbc.Driver"
    url = "jdbc:mysql://127.0.0.1:3306/seata?serverTimezone=Asia/Shanghai & useUnicode=true & characterEncoding=UTF-8"
    user = "root"
    password = "123"
    minConn = 5
    maxConn = 30
    globalTable = "global_table"
    branchTable = "branch_table"
    lockTable = "lock_table"
    queryLimit = 100
    maxWait = 5000
  }
```

因为我的数据库为8.xx，所以驱动为：

```ini
driverClassName = "com.mysql.cj.jdbc.Driver"
```

2、在mysql数据库中建立seata数据库和建表

```sql
-- 第1步建立数据库seata
CREATE DATABASE IF NOT EXISTS seata;

-- 第2步在该库中建3个表
-- the table to store GlobalSession data
drop table if exists `global_table`;
create table `global_table` (
  `xid` varchar(128)  not null,
  `transaction_id` bigint,
  `status` tinyint not null,
  `application_id` varchar(32),
  `transaction_service_group` varchar(32),
  `transaction_name` varchar(128),
  `timeout` int,
  `begin_time` bigint,
  `application_data` varchar(2000),
  `gmt_create` datetime,
  `gmt_modified` datetime,
  primary key (`xid`),
  key `idx_gmt_modified_status` (`gmt_modified`, `status`),
  key `idx_transaction_id` (`transaction_id`)
);
 
-- the table to store BranchSession data
drop table if exists `branch_table`;
create table `branch_table` (
  `branch_id` bigint not null,
  `xid` varchar(128) not null,
  `transaction_id` bigint ,
  `resource_group_id` varchar(32),
  `resource_id` varchar(256) ,
  `lock_key` varchar(128) ,
  `branch_type` varchar(8) ,
  `status` tinyint,
  `client_id` varchar(64),
  `application_data` varchar(2000),
  `gmt_create` datetime,
  `gmt_modified` datetime,
  primary key (`branch_id`),
  key `idx_xid` (`xid`)
);
 
-- the table to store lock data
drop table if exists `lock_table`;
create table `lock_table` (
  `row_key` varchar(128) not null,
  `xid` varchar(96),
  `transaction_id` long ,
  `branch_id` long,
  `resource_id` varchar(256) ,
  `table_name` varchar(32) ,
  `pk` varchar(36) ,
  `gmt_create` datetime ,
  `gmt_modified` datetime,
  primary key(`row_key`)
);
```

数据库的结构：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112802.png" alt="image-20200825173154906" style="zoom:67%;" />

3、修改D:\03Enviroment\20seata\seata-server-1.3.0\seata\conf\registry.conf

配置文件

```ini
registry {
  # file 、nacos 、eureka、redis、zk、consul、etcd3、sofa
  type = "nacos"
  
   nacos {
    application = "seata-server"
    serverAddr = "127.0.0.1:8848"
    group = "SEATA_GROUP"
    namespace = ""
    cluster = "default"
    username = "nacos"
    password = "nacos"
  }
}
```

目的是：指明注册中心为nacos，及修改nacos连接信息。

4、启动服务

先启动本地nacos，端口号为8848，再启动bin/seata-server.bat（在接下来的测试中，我们要保证两个服务都处在正常运行状态），界面显示为：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112803.png" alt="image-20200825173701790" style="zoom:67%;" />

假如Seata没启动，IDEA中会有下面的报错：

```ini
no available server to connect
```

## 3、订单/库存/账户业务数据库准备

1、分布式业务说明

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112804.png" alt="image-20200825174001173" style="zoom:50%;" />

下订单-->扣库存-->减账户（余额）。

2、创建业务数据库

（1）存储订单的数据库-seata_order

```mysql
-- 第1步 创建数据库
CREATE DATABASE seata_order;

-- 第2步 创建表
CREATE TABLE t_order(
    `id` BIGINT(11) NOT NULL AUTO_INCREMENT PRIMARY KEY,
    `user_id` BIGINT(11) DEFAULT NULL COMMENT '用户id',
    `product_id` BIGINT(11) DEFAULT NULL COMMENT '产品id',
    `count` INT(11) DEFAULT NULL COMMENT '数量',
    `money` DECIMAL(11,0) DEFAULT NULL COMMENT '金额',
    `status` INT(1) DEFAULT NULL COMMENT '订单状态：0：创建中; 1：已完结'
) ENGINE=INNODB AUTO_INCREMENT=7 DEFAULT CHARSET=utf8;
 
SELECT * FROM t_order;
```

（2）存储库存的数据库-seata_storage



```mysql
-- 第1步 创建数据库
CREATE DATABASE seata_storage;

-- 第2步 创建表
CREATE TABLE t_storage(
    `id` BIGINT(11) NOT NULL AUTO_INCREMENT PRIMARY KEY,
    `product_id` BIGINT(11) DEFAULT NULL COMMENT '产品id',
    `total` INT(11) DEFAULT NULL COMMENT '总库存',
    `used` INT(11) DEFAULT NULL COMMENT '已用库存',
    `residue` INT(11) DEFAULT NULL COMMENT '剩余库存'
) ENGINE=INNODB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8;
 
INSERT INTO seata_storage.t_storage(`id`,`product_id`,`total`,`used`,`residue`)
VALUES('1','1','100','0','100');
 
SELECT * FROM t_storage;
```

（3）存储账户信息的数据库-seata_account

```mysql
-- 第1步 创建数据库
CREATE DATABASE seata_account;

-- 第2步 创建表
CREATE TABLE t_account(
    `id` BIGINT(11) NOT NULL AUTO_INCREMENT PRIMARY KEY COMMENT 'id',
    `user_id` BIGINT(11) DEFAULT NULL COMMENT '用户id',
    `total` DECIMAL(10,0) DEFAULT NULL COMMENT '总额度',
    `used` DECIMAL(10,0) DEFAULT NULL COMMENT '已用余额',
    `residue` DECIMAL(10,0) DEFAULT '0' COMMENT '剩余可用额度'
) ENGINE=INNODB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8;

-- 第3步 插入初始数据
INSERT INTO seata_account.t_account(`id`,`user_id`,`total`,`used`,`residue`) VALUES('1','1','1000','0','1000')
 
SELECT * FROM t_account;
```

（4）对上述三个库分别创建对应的回滚日志表

```mysql
drop table `undo_log`;
CREATE TABLE `undo_log` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT,
  `branch_id` bigint(20) NOT NULL,
  `xid` varchar(100) NOT NULL,
  `context` varchar(128) NOT NULL,
  `rollback_info` longblob NOT NULL,
  `log_status` int(11) NOT NULL,
  `log_created` datetime NOT NULL,
  `log_modified` datetime NOT NULL,
  `ext` varchar(100) DEFAULT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `ux_undo_log` (`xid`,`branch_id`)
) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
```

最终效果：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112805.png" alt="image-20200825174718365" style="zoom:67%;" />

## 4、新建订单seata-order-service2001

项目结构：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112806.png" alt="image-20200825174837602" style="zoom:50%;" />

代码书写步骤：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112807.png" alt="image-20200825174932816" style="zoom:50%;" />

1、添加pom

```xml
<dependencies>
    <!--nacos-->
    <dependency>
        <groupId>com.alibaba.cloud</groupId>
        <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
    </dependency>
    <!--seata-->
    <dependency>
        <groupId>com.alibaba.cloud</groupId>
        <artifactId>spring-cloud-starter-alibaba-seata</artifactId>
        <!--为了和我们的版本呢匹配，我们需要将自带的seata去掉-->
        <exclusions>
            <exclusion>
                <artifactId>seata-all</artifactId>
                <groupId>io.seata</groupId>
            </exclusion>
        </exclusions>
    </dependency>
    <dependency>
        <groupId>io.seata</groupId>
        <artifactId>seata-all</artifactId>
        <!--<version>0.9.0</version>-->
        <!--windows环境用的是1.3.0-->
        <version>1.3.0</version>
    </dependency>
    <!--feign-->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-openfeign</artifactId>
    </dependency>
    <!--web-actuator-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
    <!--mysql-druid-->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <!--            <version>5.1.37</version>-->
        <version>8.0.11</version>
    </dependency>
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>druid-spring-boot-starter</artifactId>
        <version>1.1.10</version>
    </dependency>
    <dependency>
        <groupId>org.mybatis.spring.boot</groupId>
        <artifactId>mybatis-spring-boot-starter</artifactId>
        <version>2.0.0</version>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <optional>true</optional>
    </dependency>
</dependencies>
```

2、添加yml

```yaml
server:
  port: 2001

spring:
  application:
    name: seata-order-service
  cloud:
    alibaba:
      seata:
        #自定义事务组名称需要与seata-server中的对应
        tx-service-group: fsp_tx_group
    nacos:
      discovery:
        server-addr: localhost:8848
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/seata_order?useSSL=false&serverTimezone=Asia/Shanghai
    username: root
    password: 123

feign:
  hystrix:
    enabled: false

logging:
  level:
    io:
      seata: info

mybatis:
  mapperLocations: classpath:mapper/*.xml
```

3、添加file.conf

```ini
service {
  #vgroup->rgroup
  vgroupMapping.fsp_tx_group = "default"
  #only support single node
  default.grouplist = "127.0.0.1:8091"
  #degrade current not support
  enableDegrade = false
  #disable
  disable = false
  disableGlobalTransaction = false
}

## transaction log store, only used in seata-server
store {
  ## store mode: file、db、redis
  mode = "db"

  ## file store property
  file {
    ## store location dir
    dir = "sessionStore"
    # branch session size , if exceeded first try compress lockkey, still exceeded throws exceptions
    maxBranchSessionSize = 16384
    # globe session size , if exceeded throws exceptions
    maxGlobalSessionSize = 512
    # file buffer size , if exceeded allocate new buffer
    fileWriteBufferCacheSize = 16384
    # when recover batch read size
    sessionReloadReadSize = 100
    # async, sync
    flushDiskMode = async
  }

  ## database store property
  db {
    ## the implement of javax.sql.DataSource, such as DruidDataSource(druid)/BasicDataSource(dbcp)/HikariDataSource(hikari) etc.
    datasource = "druid"
    ## mysql/oracle/postgresql/h2/oceanbase etc.
    dbType = "mysql"
    driverClassName = "com.mysql.cj.jdbc.Driver"
    url = "jdbc:mysql://127.0.0.1:3306/seata?serverTimezone=Asia/Shanghai & useUnicode=true & characterEncoding=UTF-8"
    user = "root"
    password = "123"
    minConn = 5
    maxConn = 30
    globalTable = "global_table"
    branchTable = "branch_table"
    lockTable = "lock_table"
    queryLimit = 100
    maxWait = 5000
  }

  ## redis store property
  redis {
    host = "127.0.0.1"
    port = "6379"
    password = ""
    database = "0"
    minConn = 1
    maxConn = 10
    queryLimit = 100
  }
}
```

​		上述文件是从seata-server-1.3.0中复制而来，但是对于service模块中的自定义分组有修改，修改如下：

```ini
#原先IDEA中的的vgroup_mapping会报错，需要写成vgroupMapping，不然连接不上seata-server
vgroupMapping.fsp_tx_group = "default"
```

4、添加registry.conf

```ini
registry {
  # file 、nacos 、eureka、redis、zk、consul、etcd3、sofa
  type = "nacos"

  nacos {
    application = "seata-server"
    serverAddr = "127.0.0.1:8848"
    group = "SEATA_GROUP"
    namespace = ""
    cluster = "default"
    username = "nacos"
    password = "nacos"
  }
  eureka {
    serviceUrl = "http://localhost:8761/eureka"
    application = "default"
    weight = "1"
  }
  redis {
    serverAddr = "localhost:6379"
    db = 0
    password = ""
    cluster = "default"
    timeout = 0
  }
  zk {
    cluster = "default"
    serverAddr = "127.0.0.1:2181"
    sessionTimeout = 6000
    connectTimeout = 2000
    username = ""
    password = ""
  }
  consul {
    cluster = "default"
    serverAddr = "127.0.0.1:8500"
  }
  etcd3 {
    cluster = "default"
    serverAddr = "http://localhost:2379"
  }
  sofa {
    serverAddr = "127.0.0.1:9603"
    application = "default"
    region = "DEFAULT_ZONE"
    datacenter = "DefaultDataCenter"
    cluster = "default"
    group = "SEATA_GROUP"
    addressWaitTime = "3000"
  }
  file {
    name = "file.conf"
  }
}

config {
  # file、nacos 、apollo、zk、consul、etcd3
  type = "file"

  nacos {
    serverAddr = "127.0.0.1:8848"
    namespace = ""
    group = "SEATA_GROUP"
    username = ""
    password = ""
  }
  consul {
    serverAddr = "127.0.0.1:8500"
  }
  apollo {
    appId = "seata-server"
    apolloMeta = "http://192.168.1.204:8801"
    namespace = "application"
  }
  zk {
    serverAddr = "127.0.0.1:2181"
    sessionTimeout = 6000
    connectTimeout = 2000
    username = ""
    password = ""
  }
  etcd3 {
    serverAddr = "http://localhost:2379"
  }
  file {
    name = "file.conf"
  }
}
```

该文件是从seata-server-1.3.0中复制过来的，无改动。

5、添加domain模块

（1）CommonResult

```java
package com.xiaolun.springcloud.domain;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;


@Data
@AllArgsConstructor
@NoArgsConstructor
public class CommonResult<T> {
   private Integer code;
   private String message;
   private T data;

   public CommonResult(Integer code, String message) {
      this(code, message, null);
   }
}
```

（2）Order

```java
package com.xiaolun.springcloud.domain;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import java.math.BigDecimal;

@Data
@AllArgsConstructor
@NoArgsConstructor
public class Order {
   private Long id;

   private Long userId;

   private Long productId;

   private Integer count; //库存

   private BigDecimal money;

   private Integer status; //订单状态（初始状态永远为0）：0：创建中；1：已完结
}
```

6、Dao接口及实现

（1）OrderDao

```java
package com.xiaolun.springcloud.dao;

import com.xiaolun.springcloud.domain.Order;
import org.apache.ibatis.annotations.Mapper;
import org.apache.ibatis.annotations.Param;

@Mapper //mybatis中推荐这个使用
public interface OrderDao {
   //新建订单
   void create(Order order);

   //修改订单状态，从零改为1
   void update(@Param("userId") Long userId, @Param("status") Integer status);
}
```

（2）resources文件夹下新建mapper文件夹后添加OrderMapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >

<mapper namespace="com.xiaolun.springcloud.dao.OrderDao">

    <resultMap id="BaseResultMap" type="com.xiaolun.springcloud.domain.Order">
        <id column="id" property="id" jdbcType="BIGINT"/>
        <!--数据库中有下划线，java中无下划线-->
        <result column="user_id" property="userId" jdbcType="BIGINT"/>
        <result column="product_id" property="productId" jdbcType="BIGINT"/>
        <result column="count" property="count" jdbcType="INTEGER"/>
        <result column="money" property="money" jdbcType="DECIMAL"/>
        <result column="status" property="status" jdbcType="INTEGER"/>
    </resultMap>

    <insert id="create">
        insert into t_order (id,user_id,product_id,count,money,status)
        values (null,#{userId},#{productId},#{count},#{money},0);
    </insert>

    <update id="update">
        update t_order set status = 1
        where user_id=#{userId} and status = #{status};
    </update>

</mapper>
```

7、Service接口及实现

（1）OrderService

```java
package com.xiaolun.springcloud.service;

import com.xiaolun.springcloud.domain.Order;

public interface OrderService {
   void create(Order order);
}
```

（2）OrderServiceImpl

```java
package com.xiaolun.springcloud.service.impl;

import com.xiaolun.springcloud.dao.OrderDao;
import com.xiaolun.springcloud.domain.Order;
import com.xiaolun.springcloud.service.AccountService;
import com.xiaolun.springcloud.service.OrderService;
import com.xiaolun.springcloud.service.StorageService;
import io.seata.spring.annotation.GlobalTransactional;
import lombok.extern.slf4j.Slf4j;
import org.springframework.stereotype.Service;

import javax.annotation.Resource;

@Service
@Slf4j
public class OrderServiceImpl implements OrderService {
   @Resource
   private OrderDao orderDao;
   @Resource
   private StorageService storageService;
   @Resource
   private AccountService accountService;

   /**
    * 创建订单->调用库存服务扣减库存->调用账户服务扣减账户余额->修改订单状态
    */
   @Override
   @GlobalTransactional(name = "fsp-create-order", rollbackFor = Exception.class)
   public void create(Order order) {
      log.info("----->开始新建订单");
      //新建订单
      orderDao.create(order);

      //扣减库存
      log.info("----->订单微服务开始调用库存，做扣减Count");
      storageService.decrease(order.getProductId(), order.getCount());
      log.info("----->订单微服务开始调用库存，做扣减end");

      //扣减账户
      log.info("----->订单微服务开始调用账户，做扣减Money");
      accountService.decrease(order.getUserId(), order.getMoney());
      log.info("----->订单微服务开始调用账户，做扣减end");

      //修改订单状态，从零到1代表已经完成
      log.info("----->修改订单状态开始");
      orderDao.update(order.getUserId(), 0);
      log.info("----->修改订单状态结束");
      log.info("----->下订单结束了");
   }
}
```

（3）StorageService

```java
package com.xiaolun.springcloud.service;

import com.xiaolun.springcloud.domain.CommonResult;
import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestParam;

@FeignClient(value = "seata-storage-service")
public interface StorageService { //订单微服务2001去调用库存微服务2002
   @PostMapping(value = "/storage/decrease") //库存做减法（减库存之后数据要进行变更）
   CommonResult decrease(@RequestParam("productId") Long productId, @RequestParam("count") Integer count);
}
```

（4）AccountService

```java
package com.xiaolun.springcloud.service;

import com.xiaolun.springcloud.domain.CommonResult;
import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestParam;

import java.math.BigDecimal;

@FeignClient(value = "seata-account-service")
public interface AccountService {
	@PostMapping(value = "/account/decrease") //账号做减法（按照用户id去修改）
	CommonResult decrease(@RequestParam("userId") Long userId, @RequestParam("money") BigDecimal money);
}
```

8、业务类-controller

```java
package com.xiaolun.springcloud.controller;

import com.xiaolun.springcloud.domain.CommonResult;
import com.xiaolun.springcloud.domain.Order;
import com.xiaolun.springcloud.service.OrderService;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

import javax.annotation.Resource;

@RestController
public class OrderController {
   @Resource
   private OrderService orderService;

   @GetMapping("/order/create")
   public CommonResult create(Order order) {
      orderService.create(order);
      return new CommonResult(200, "订单创建成功");
   }
}
```

9、config配置

（1）MyBatisConfig

```java
package com.xiaolun.springcloud.config;

import org.mybatis.spring.annotation.MapperScan;
import org.springframework.context.annotation.Configuration;

@Configuration
@MapperScan({"com.xiaolun.springcloud.dao"})
public class MyBatisConfig {

}
```

（2）DataSourceProxyConfig

```java
package com.xiaolun.springcloud.config;

import com.alibaba.druid.pool.DruidDataSource;
import io.seata.rm.datasource.DataSourceProxy;
import org.apache.ibatis.session.SqlSessionFactory;
import org.mybatis.spring.SqlSessionFactoryBean;
import org.mybatis.spring.transaction.SpringManagedTransactionFactory;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.core.io.support.PathMatchingResourcePatternResolver;

import javax.sql.DataSource;

//使用Seata对数据源进行代理
@Configuration
public class DataSourceProxyConfig {

   @Value("${mybatis.mapperLocations}")
   private String mapperLocations;

   @Bean
   @ConfigurationProperties(prefix = "spring.datasource")
   public DataSource druidDataSource() {
      return new DruidDataSource();
   }

   @Bean
   public DataSourceProxy dataSourceProxy(DataSource dataSource) {
      return new DataSourceProxy(dataSource);
   }

   @Bean
   public SqlSessionFactory sqlSessionFactoryBean(DataSourceProxy dataSourceProxy) throws Exception {
      SqlSessionFactoryBean sqlSessionFactoryBean = new SqlSessionFactoryBean();
      sqlSessionFactoryBean.setDataSource(dataSourceProxy);
      sqlSessionFactoryBean.setMapperLocations(new PathMatchingResourcePatternResolver().getResources(mapperLocations));
      sqlSessionFactoryBean.setTransactionFactory(new SpringManagedTransactionFactory());
      return sqlSessionFactoryBean.getObject();
   }
}
```

10、主启动类

```java
package com.xiaolun.springcloud;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.openfeign.EnableFeignClients;

@EnableDiscoveryClient
@EnableFeignClients  //使用Seata对数据源进行代理
@SpringBootApplication(exclude = DataSourceAutoConfiguration.class)//取消数据源自动创建的配置
public class SeataOrderMainApp2001 {

	public static void main(String[] args) {
		SpringApplication.run(SeataOrderMainApp2001.class, args);
	}
}
```

11、测试

启动该主启动类，项目可以正常启动时，配置成功。

## 5、新建库存seata-order-service2002

项目结构：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112808.png" alt="image-20200825180815083" style="zoom:50%;" />

1、添加pom

与seata-order-service2001同。

2、添加yml

```yaml
server:
  port: 2002

spring:
  application:
    name: seata-storage-service
  cloud:
    alibaba:
      seata:
        tx-service-group: fsp_tx_group
    nacos:
      discovery:
        server-addr: localhost:8848
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/seata_storage?serverTimezone=Asia/Shanghai & useUnicode=true & characterEncoding=UTF-8
    username: root
    password: 123

logging:
  level:
    io:
      seata: info

mybatis:
  mapperLocations: classpath:mapper/*.xml
```

3、file.conf和registry.conf和seata-order-service2001同。

4、domain

（1）CommonResult与seata-order-service2001同。

（2）Storage

```java
package com.xiaolun.springcloud.domain;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@NoArgsConstructor
@AllArgsConstructor
public class Storage {

   private Long id;

   // 产品id
   private Long productId;

   //总库存
   private Integer total;

   //已用库存
   private Integer used;

   //剩余库存
   private Integer residue;
}
```

5、Dao接口及实现

（1）StorageDao

```java
package com.xiaolun.springcloud.dao;

import org.apache.ibatis.annotations.Mapper;
import org.apache.ibatis.annotations.Param;

@Mapper
public interface StorageDao {
   //扣减库存信息
   void decrease(@Param("productId") Long productId, @Param("count") Integer count);
}
```

（2）resources文件夹下新建mapper文件夹后添加StorageMapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >


<mapper namespace="com.xiaolun.springcloud.dao.StorageDao">

    <resultMap id="BaseResultMap" type="com.xiaolun.springcloud.domain.Storage">
        <id column="id" property="id" jdbcType="BIGINT"/>
        <result column="product_id" property="productId" jdbcType="BIGINT"/>
        <result column="total" property="total" jdbcType="INTEGER"/>
        <result column="used" property="used" jdbcType="INTEGER"/>
        <result column="residue" property="residue" jdbcType="INTEGER"/>
    </resultMap>

    <update id="decrease">
        UPDATE
            t_storage
        SET
            used = used + #{count},residue = residue - #{count}
        WHERE
            product_id = #{productId}
    </update>

</mapper>
```

6、Service接口及实现

（1）StorageService

```java
package com.xiaolun.springcloud.service;

public interface StorageService {
   // 扣减库存
   void decrease(Long productId, Integer count);
}
```

（2）StorageServiceImpl

```java
package com.xiaolun.springcloud.service.impl;

import com.xiaolun.springcloud.dao.StorageDao;
import com.xiaolun.springcloud.service.StorageService;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Service;

import javax.annotation.Resource;

@Service
public class StorageServiceImpl implements StorageService {

   private static final Logger LOGGER = LoggerFactory.getLogger(StorageServiceImpl.class);

   @Resource
   private StorageDao storageDao;

   // 扣减库存
   @Override
   public void decrease(Long productId, Integer count) {
      LOGGER.info("------->storage-service中扣减库存开始");
      storageDao.decrease(productId, count);
      LOGGER.info("------->storage-service中扣减库存结束");
   }
}
```

7、业务类-controller

```java
package com.xiaolun.springcloud.controller;

import com.xiaolun.springcloud.domain.CommonResult;
import com.xiaolun.springcloud.service.StorageService;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import javax.annotation.Resource;

@RestController
public class StorageController {

   @Resource
   private StorageService storageService;

   //扣减库存
   @RequestMapping("/storage/decrease")
   public CommonResult decrease(Long productId, Integer count) {
      storageService.decrease(productId, count);
      return new CommonResult(200, "扣减库存成功！");
   }
}
```

8、config配置

（1）MyBatisConfig

```java
package com.xiaolun.springcloud.config;

import org.mybatis.spring.annotation.MapperScan;
import org.springframework.context.annotation.Configuration;

@Configuration
@MapperScan({"com.xiaolun.springcloud.dao"})
public class MyBatisConfig {
}
```

（2）DataSourceProxyConfig与seata-order-service2001同。

9、主启动类

```java
package com.xiaolun.springcloud;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.openfeign.EnableFeignClients;

@SpringBootApplication(exclude = DataSourceAutoConfiguration.class)
@EnableDiscoveryClient
@EnableFeignClients
public class SeataStorageServiceApplication2002 {
   public static void main(String[] args) {
      SpringApplication.run(SeataStorageServiceApplication2002.class, args);
   }
}
```

10、测试

启动该主启动类，项目可以正常启动时，配置成功。

## 6、新建账户seata-order-service2003

项目结构：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112809.png" alt="image-20200825181738978" style="zoom:50%;" />

1、pom文件与seata-order-service2001同。

2、添加yml

```yml
server:
  port: 2003

spring:
  application:
    name: seata-account-service
  cloud:
    alibaba:
      seata:
        tx-service-group: fsp_tx_group
    nacos:
      discovery:
        server-addr: localhost:8848
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/seata_account?serverTimezone=Asia/Shanghai & useUnicode=true & characterEncoding=UTF-8
    username: root
    password: 123

feign:
  hystrix:
    enabled: false

logging:
  level:
    io:
      seata: info

mybatis:
  mapperLocations: classpath:mapper/*.xml
```

3、file.conf和registry.conf和seata-order-service2001同。

4、domain

（1）CommonResult与seata-order-service2001同。

（2）Account

```java
package com.xiaolun.springcloud.domain;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

import java.math.BigDecimal;

@Data
@AllArgsConstructor
@NoArgsConstructor
public class Account {

   private Long id;

   /**
    * 用户id
    */
   private Long userId;

   /**
    * 总额度
    */
   private BigDecimal total;

   /**
    * 已用额度
    */
   private BigDecimal used;

   /**
    * 剩余额度
    */
   private BigDecimal residue;
}
```

5、Dao接口及实现

（1）AccountDao

```java
package com.xiaolun.springcloud.dao;

import org.apache.ibatis.annotations.Mapper;
import org.apache.ibatis.annotations.Param;

import java.math.BigDecimal;

@Mapper
public interface AccountDao {

   /**
    * 扣减账户余额
    */
   void decrease(@Param("userId") Long userId, @Param("money") BigDecimal money);
}
```

（2）resources文件夹下新建mapper文件夹后添加AccountMapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >

<mapper namespace="com.xiaolun.springcloud.dao.AccountDao">

    <resultMap id="BaseResultMap" type="com.xiaolun.springcloud.domain.Account">
        <id column="id" property="id" jdbcType="BIGINT"/>
        <result column="user_id" property="userId" jdbcType="BIGINT"/>
        <result column="total" property="total" jdbcType="DECIMAL"/>
        <result column="used" property="used" jdbcType="DECIMAL"/>
        <result column="residue" property="residue" jdbcType="DECIMAL"/>
    </resultMap>

    <update id="decrease">
        UPDATE t_account
        SET
          residue = residue - #{money},used = used + #{money}
        WHERE
          user_id = #{userId};
    </update>

</mapper>
```

6、Service接口及实现

（1）AccountService

```java
package com.xiaolun.springcloud.service;

import org.springframework.web.bind.annotation.RequestParam;

import java.math.BigDecimal;

public interface AccountService {

   /**
    * 扣减账户余额
    */
   void decrease(@RequestParam("userId") Long userId, @RequestParam("money") BigDecimal money);
}
```

（2）AccountServiceImpl

```java
package com.xiaolun.springcloud.service.impl;

import com.xiaolun.springcloud.dao.AccountDao;
import com.xiaolun.springcloud.service.AccountService;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Service;

import javax.annotation.Resource;
import java.math.BigDecimal;
import java.util.concurrent.TimeUnit;

/**
 * 账户业务实现类
 */
@Service
public class AccountServiceImpl implements AccountService {

   private static final Logger LOGGER = LoggerFactory.getLogger(AccountServiceImpl.class);
   @Resource
   AccountDao accountDao;

   /**
    * 扣减账户余额
    */
   @Override
   public void decrease(Long userId, BigDecimal money) {

      LOGGER.info("------->account-service中扣减账户余额开始");
      //模拟超时时间，全局事务回滚
//    try {
//       TimeUnit.SECONDS.sleep(20);
//    } catch (InterruptedException e) {
//       e.printStackTrace();
//    }
      accountDao.decrease(userId, money);
      LOGGER.info("------->account-service中扣减账户余额结束");
   }
}
```

​		在该类中有一个模拟超时时间的方法，在测试全局事务的时候需要开启，默认是关闭的。

7、业务类-controller

```java
package com.xiaolun.springcloud.controller;

import com.xiaolun.springcloud.domain.CommonResult;
import com.xiaolun.springcloud.service.AccountService;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

import javax.annotation.Resource;
import java.math.BigDecimal;

@RestController
public class AccountController {

   @Resource
   AccountService accountService;

   /**
    * 扣减账户余额
    */
   @RequestMapping("/account/decrease")
   public CommonResult decrease(@RequestParam("userId") Long userId, @RequestParam("money") BigDecimal money) {
      accountService.decrease(userId, money);
      return new CommonResult(200, "扣减账户余额成功！");
   }
}
```

8、config配置中，MyBatisConfig和DataSourceProxyConfig与seata-order-service2001同。

9、主启动类

```java
package com.xiaolun.springcloud;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.openfeign.EnableFeignClients;

@SpringBootApplication(exclude = DataSourceAutoConfiguration.class)
@EnableDiscoveryClient
@EnableFeignClients
public class SeataAccountMainApp2003 {
   public static void main(String[] args) {
      SpringApplication.run(SeataAccountMainApp2003.class, args);
   }
}
```

10、测试

启动该主启动类，项目可以正常启动时，配置成功。

## 7、测试

​		依次启动nacos，seata-server，seata-order-service2001/2002/2003，在nacos中会看到下面的注册情况。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112810.png" alt="image-20200825184210118" style="zoom:67%;" />

### 测试1-正常情况

1、数据库初始情况

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112811.png" alt="image-20200825182502986" style="zoom:50%;" />

2、正常下单

访问下面的网址：

```http
//表示1号用于买10个1号产品，一共花了100块钱。
http://localhost:2001/order/create?userId=1&productId=1&count=10&money=100
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112812.png" alt="image-20200825183859800" style="zoom:50%;" />

此时数据库情况：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112813.png" alt="image-20200825182654590" style="zoom:50%;" />

### 测试2-超时异常，没加@GlobalTransactional

1、在AccountServiceImpl中添加下面的超时方法

```java
try {
	TimeUnit.SECONDS.sleep(20);
} catch (InterruptedException e) {
	e.printStackTrace();
}
```

2、故障情况

​		当库存和账户余额扣减后，订单状态并没有设置为已经完成，没有从零改为1，而且由于feign的重试机制，账户余额还有可能被多次扣减。

### 测试3-超时异常，添加@GlobalTransactional

1、此时同样在AccountServiceImpl中添加上面 的超时方法

2、添加下面的注解

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112814.png" alt="image-20200825183112807" style="zoom:50%;" />

3、故障情况

下单后数据库数据并没有任何改变。

## 8、Seata之原理简介

### 1、介绍

​		2019年1月份蚂蚁金服和阿里巴巴共同开源的分布式事务解决方案。

​		Simple Extensible Autonomous Transaction Architecture,简单可扩展自治事务框架。

​		2020起初，参加工作后用1.0以后的版本。

### 2、再看TC/TM/RM三大组件

分布式事务的执行流程：

+ TM开启分布式事务(TM向TC注册全局事务记录)
+ 换业务场景，编排数据库，服务等事务内资源（RM向TC汇报资源准备状态）
+ TM结束分布式事务，事务一阶段结束（TM通知TC提交/回滚分布式事务）
+ TC汇总事务信息，决定分布式事务是提交还是回滚
+ TC通知所有RM提交/回滚资源，事务二阶段结束

### 3、AT模式如何做到对业务的无侵入

1、介绍

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112815.png" alt="image-20200825183458621" style="zoom:50%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112816.png" alt="image-20200825183507216" style="zoom:50%;" />

2、流程

（1）一阶段加载

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112817.png" alt="image-20200825183608852" style="zoom:67%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112818.png" alt="image-20200825183615453" style="zoom:50%;" />

（2）二阶段提交

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112819.png" alt="image-20200825183635986" style="zoom:50%;" />

（3）二阶段回滚

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112820.png" alt="image-20200825183658804" style="zoom:50%;" />



<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112821.png" alt="image-20200825183705297" style="zoom:67%;" />

（4）补充

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112822.png" alt="image-20200825183804062" style="zoom:67%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831112823.png" alt="image-20200825183815575" style="zoom:67%;" />