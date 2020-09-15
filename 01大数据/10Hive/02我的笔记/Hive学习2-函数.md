## 1、函数

### 1、系统内置函数

```sql
-- 查看系统自带的函数
hive> show functions;

-- 详细显示自带的函数的用法
hive> desc function extended split;
```

### 2、自定义函数

1、介绍

（1）Hive 自带了一些函数，比如：max/min 等，但是数量有限，自己可以通过自定义 UDF来方便的扩展。

（2）当 Hive 提供的内置函数无法满足你的业务处理需要时，此时就可以考虑使用用户自定义函数（UDF：user-defined function）。

（3）根据用户自定义函数类别分为以下三种：

```
1、UDF（User-Defined-Function）
一进一出
2、UDAF（User-Defined Aggregation Function）
聚集函数，多进一出
类似于：count/max/min
3、UDTF（User-Defined Table-Generating Functions）
一进多出
如 lateral view explore()
```

（4）官方参考文档地址

```http
https://cwiki.apache.org/confluence/display/Hive/HivePlugins
```

（5）编程步骤

```
1、继承 org.apache.hadoop.hive.ql.UDF
2、需要实现 evaluate 函数；evaluate 函数支持重载；
3、在 hive 的命令行窗口创建函数
  a）添加 jar
  add jar linux_jar_path
  b）创建 function
  create [temporary] function [dbname.]function_name AS
  class_name;
4、在 hive 的命令行窗口删除函数
Drop [temporary] function [if exists]
[dbname.]function_name;
```

其中，UDF 必须要有返回类型，可以返回 null，但是返回类型不能为 void。

2、自定义UDF函数

创建Maven项目，项目名：hive-study。结构如下：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200915163424821.png" alt="image-20200915163424821" style="zoom:67%;" />

（1）导入依赖

```xml
    <dependencies>
        <dependency>
            <groupId>org.apache.hive</groupId>
            <artifactId>hive-exec</artifactId>
            <version>1.2.1</version>
        </dependency>
    </dependencies>
```

（2）创建测试类

```java
package com.xiaolun.hive;

import org.apache.hadoop.hive.ql.exec.UDF;

public class MyUDF extends UDF {
	public int evaluate(int data){
		return  data + 5;
	}
}
```

（3）将IDEA中打成的package上传到服务器/myhive/hive/lib

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200915163620397.png" alt="image-20200915163620397" style="zoom:67%;" />

（4）将jar包添加到hive的classpath中，以便识别

```shell
hive > add jar /myhive/hive/lib/hiveMyUdf.jar
```

（5）创建临时函数与开发好的 java class 关联

```sql
hive> create temporary function hiveMyUdf as "com.xiaolun.hive.MyUDF";
```

（6）在hql中使用自定义的函数

```sql
 select hiveMyUdf(id) from stu;
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200915163907791.png" alt="image-20200915163907791" style="zoom:67%;" />

可以看到stu表中的id+5之后的显示如上图所示。

3、自定义UDTF函数

项目结构：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200915170015906.png" alt="image-20200915170015906" style="zoom:67%;" />

（1）导入和UDF相同的pom依赖。

（2）创建测试类

```java
package com.xiaolun.hive;
import org.apache.hadoop.hive.ql.exec.UDFArgumentException;
import org.apache.hadoop.hive.ql.metadata.HiveException;
import org.apache.hadoop.hive.ql.udf.generic.GenericUDTF;
import org.apache.hadoop.hive.serde2.objectinspector.ObjectInspector;
import org.apache.hadoop.hive.serde2.objectinspector.ObjectInspectorFactory;
import org.apache.hadoop.hive.serde2.objectinspector.StructObjectInspector;
import org.apache.hadoop.hive.serde2.objectinspector.primitive.PrimitiveObjectInspectorFactory;

import java.util.ArrayList;
import java.util.List;

public class MyUdtf extends GenericUDTF {

   private ArrayList<String> outList = new ArrayList<String>();

   @Override
   public StructObjectInspector initialize(StructObjectInspector argOIs) throws UDFArgumentException {
      //1.定义输出数据的列名
      List<String> fieldNames = new ArrayList<String>();
      fieldNames.add("lineToWord");

      //2.定义输出数据的类型，以String类型输出
      List<ObjectInspector> fieldOIs = new ArrayList<ObjectInspector>();
      fieldOIs.add(PrimitiveObjectInspectorFactory.javaStringObjectInspector);
      return ObjectInspectorFactory.getStandardStructObjectInspector(fieldNames, fieldOIs);
   }

   @Override
   public void process(Object[] args) throws HiveException {
      //1.获取原始数据
      String arg = args[0].toString();
      //2.获取数据传入的第二个参数，此处为分隔符
      String splitKey = args[1].toString();
      //3.将原始数据按照传入的分隔符进行切分
      String[] fields = arg.split(splitKey);
      //4.遍历切分后的结果，并写出
      for (String field : fields) {
         //集合为复用的，首先清空集合
         outList.clear();
         //将每一个单词添加至集合
         outList.add(field);
         //将集合内容写出
         forward(outList);
      }
   }

   @Override
   public void close() throws HiveException {
   }
}
```

（3）将IDEA中打成的package上传到服务器/myhive/hive/lib

（4）将jar包添加到hive的classpath中

```sql
add jar /myhive/hive/lib/myUdtf.jar;
```

（5）创建临时函数与开发好的 java class 关联

```sql
create temporary function hiveUdtf as "com.xiaolun.hive.MyUdtf";
```

（6）在hql中使用自定义的函数

```sql
select hiveUdtf('hello,world,sky',',');
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200915170206030.png" alt="image-20200915170206030" style="zoom:80%;" />