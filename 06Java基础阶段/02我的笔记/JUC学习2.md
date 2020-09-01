# 1 流式计算

## 1、四大函数式接口

1、接口类型

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085044.png" alt="image-20200811213746258" style="zoom:80%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085045.png" alt="image-20200811213755511" style="zoom:80%;" />

2、实例

```java
import java.util.function.Consumer;
import java.util.function.Function;
import java.util.function.Predicate;
import java.util.function.Supplier;

public class StreamDemo02 {
   public static void main(String[] args) {
      functionTest();
      predicateTest();
      consumerTest();
      supplierTest();
   }

   //1、函数式接口<String, Integer><输入类型，输出类型>
//    Function <String, Integer> function = new Function<String, Integer>() {
//       @Override
//       public Integer apply(String s) {
//          return 1024;
//       }
//    };
   private static void functionTest() {
      Function<String, Integer> function = (s) -> {
         return 1024;
      };
      System.out.println(function.apply("abc")); //1024
   }

   //2、断定型接口 输入参数类型 String,返回boolean类型
//    Predicate<String> predicate = new Predicate<String>() {
//       @Override
//       public boolean test(String s) {
//          return false;
//       }
//    };
   private static void predicateTest() {
      Predicate<String> predicate = (s) -> {
         return s.isEmpty();
      };
      System.out.println(predicate.test("abc"));  //false
   }

   //3、消费型接口 输入类型 输出为void
   //    Consumer<String> consumer = new Consumer<String>() {
//       @Override
//       public void accept(String s) {
//
//       }
//    };

   private static void consumerTest() {
      Consumer<String> consumer = (s) -> {
         System.out.println("进入消费数据接口中");
      };
      consumer.accept("param");
   }

   //4、供给型接口(和消费者相反) 无输出参数，又赶回参数
//    Supplier<String> supplier = new Supplier<String>() {
//       @Override
//       public String get() {
//          return null;
//       }
//    };
   private static void supplierTest() {
      Supplier<String> supplier = () -> {
         return "hello world!";
      };
      System.out.println(supplier.get());
   }
}
```

## 2、Stream流

1、介绍

（1）流(Stream) 到底是什么呢？是数据渠道，用于操作数据源（集合、数组等）所生成的元素序列。
		“集合讲的是数据，流讲的是计算！”。

2、特点

（1）Stream 自己不会存储元素。

（2）Stream 不会改变源对象。相反，他们会返回一个持有结果的新Stream。

（3）Stream 操作是延迟执行的。这意味着他们会等到需要结果的时候才执行。

3、阶段

（1）创建一个Stream：一个数据源（数组、集合）。

（2）中间操作：一个中间操作，处理数据源数据。

（3）终止操作：一个终止操作，执行中间操作链，产生结果。

4、实现代码

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
class User {
   private Integer id;
   private String userName;
   private int age;
}

/**
 * 1、题目：请按照给出数据，找出同时满足
 * 偶数ID且年龄大于24且用户名转为大写且用户名字母倒排序
 * 最后只输出一个用户名字
 * 2、源头=>中间流水线=>结果
 */
public class StreamDemo01 {
   public static void main(String[] args) {
      User u1 = new User(11, "a", 23);
      User u2 = new User(12, "b", 24);
      User u3 = new User(13, "c", 22);
      User u4 = new User(14, "d", 28);
      User u5 = new User(16, "e", 26);

      List<User> list = Arrays.asList(u1, u2, u3, u4, u5);

      list.stream().filter(p -> {
         return p.getId() % 2 == 0;
      }).filter(p -> {
         return p.getAge() > 24;
      }).map(f -> {
         return f.getUserName().toUpperCase();
      }).sorted((o1, o2) -> {
         return o2.compareTo(o1);
      }).limit(1).forEach(System.out::println);
   }
}
```

# 2 分支合并框架

## 1、原理

Fork：把一个复杂任务进行分拆，大事化小。
Join：把分拆任务的结果进行合并。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085046.png" alt="image-20200812174341306" style="zoom:80%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085047.png" alt="image-20200812174347684" style="zoom:80%;" />

## 2、相关类

1、ForkJoinPool

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085048.png" alt="image-20200812174432507" style="zoom:80%;" />

分支合并池    类比=>   线程池。

2、ForkJoinTask

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085049.png" alt="image-20200812174455462" style="zoom:80%;" />

ForkJoinTask    类比=>   FutureTask。

3、RecursiveTask

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085050.png" alt="image-20200812174519297" style="zoom:80%;" />

递归任务：继承后可以实现递归(自己调自己)调用的任务。

```java
class Fibonacci extends RecursiveTask<Integer> {
   final int n;
   Fibonacci(int n) { this.n = n; }
   Integer compute() {
     if (n <= 1)
       return n;
     Fibonacci f1 = new Fibonacci(n - 1);
     f1.fork();
     Fibonacci f2 = new Fibonacci(n - 2);
     return f2.compute() + f1.join();
   }
 }
```

## 3、实现代码

```java
class MyTask extends RecursiveTask<Integer> {
   //10以内进行拆分（计算临界）
   private static final Integer ADJUST_VALUE = 10;
   private int begin; //开始 0
   private int end; //结束 100
   private int result;

   public MyTask(int begin, int end) {
      this.begin = begin;
      this.end = end;
   }

   @Override
   protected Integer compute() {
      if ((end - begin) <= ADJUST_VALUE) {
         for (int i = begin; i <= end; i++) {
            result = result + i;
         }
      } else {
         int middle = (begin + end) / 2;
         MyTask task01 = new MyTask(begin, middle);
         MyTask task02 = new MyTask(middle + 1, end);
         task01.fork();  //回来调用compute方法
         task02.fork();
         result = task01.join() + task02.join();
      }
      return result;
   }
}

/**
 * 分支合并例子
 * ForkJoinPool
 * ForkJoinTask
 * RecursiveTask
 */
public class ForkJoinDemo {
   public static void main(String[] args) throws ExecutionException, InterruptedException {
      MyTask myTask = new MyTask(0, 100); //任务
      ForkJoinPool forkJoinPool = new ForkJoinPool();
      ForkJoinTask<Integer> forkJoinTask = forkJoinPool.submit(myTask);
      System.out.println(forkJoinTask.get());
      forkJoinPool.shutdown();
   }
}
```

# 3 异步调用

## 1、原理

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085051.png" alt="image-20200812180030241" style="zoom:80%;" />

## 2、实现代码

```java
/**
 * 1、做完子线程的任务后，调用子线程（异步调用）
 */
public class CompletableFutureDemo {

   public static void main(String[] args) throws Exception {

      //异步调用：没有返回值，只是启动一个线程去干活。
//        CompletableFuture<Void> completableFuture1 = CompletableFuture.runAsync(()->{
//            System.out.println(Thread.currentThread().getName()+"\t completableFuture1");
//        });
//        completableFuture1.get();

      //异步回调 supply:供给型函数
      CompletableFuture<Integer> completableFuture2 = CompletableFuture.supplyAsync(() -> {
         System.out.println(Thread.currentThread().getName() + "\t completableFuture2");
//       int i = 10 / 0;
         return 1024;
      });
      //whenComplete 有两个参数，没有返回值（类似消费者，获取线程中的函数）
      System.out.println(completableFuture2.whenComplete((t, u) -> {
         //正常完成，走该分支
         //-------t------1024
         System.out.println("-------t------" + t);
         //       -------u------null
         System.out.println("-------u------" + u); //异常信息
      }).exceptionally(f -> {
         //异常完成，走该分支
         System.out.println("-----exception---------" + f.getMessage());
         return 444;
         //返回正确结果：1024
         //返回异常结果：444
      }).get());
   }
}
```

