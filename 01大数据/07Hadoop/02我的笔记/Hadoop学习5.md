## 1、快速排序

1、排序原理

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200907101005116.png" alt="image-20200907101005116" style="zoom:50%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200907101123862.png" alt="image-20200907101123862" style="zoom:50%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200907100804443.png" alt="image-20200907100804443" style="zoom: 50%;" />

2、代码实现

```java
package com.xiaolun;

import java.util.Arrays;

public class QuickSort {
   public static void main(String[] args) {
      int[] arr = {6,2,3,5,8,9,1};
      quickSort01(arr, 0, arr.length-1);
      System.out.println(Arrays.toString(arr));
   }

   public static void quickSort01(int[] arr,int left,int right){
      if (left > right){
         return;
      }else {
         //确定分界线位置的方法
         int index = getMarkIndex(arr,left,right);
         //左侧递归
         quickSort01(arr, left, index-1);
         //右侧递归
         quickSort01(arr, index+1, right);
      }
   }

   public static int getMarkIndex(int[] arr, int left, int right) {
      //确定分界线的位置
      int key = arr[left];

      //进行比较
      while (left < right){
         //从右向左
         while (arr[right]>=key  && left < right){
            right--;
         }
         //arr[right] < key 交换位置 arr[left]  key arr[right]
         arr[left] = arr[right];
         arr[right] = key;

         //从左向右
         while (arr[left]<=key  && left < right){
            left++;
         }
         //arr[right] > key 交换位置 arr[left]  key arr[right]

         arr[right] = arr[left];
         arr[left] = key;
      }
      return left;
   }

}
```

使用递归方法的理解：

```
/**
 * 快速排序的实现
 * 递归的三要素
 * （1）定义一个方法
 * 返回值 void
 * 参数：原始数组 起始下标 结束小表 小数据集的
 * （2）退出条件
 * left > right
 * return
 * （3）规律
 * 每一步都是在找最终的每一个界限 的位置
 * 定义一个方法 找最终位置
 * 左侧，递归
 * 右侧，递归
 */
```

## 2、环形缓存区

写入数据的时候，首位相连的写入，因此称为环形缓冲区。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200907163117083.png" alt="image-20200907163117083" style="zoom: 50%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200907163412084.png" alt="image-20200907163412084" style="zoom: 50%;" />

1、记录元数据的原因：便于在排序的时候，调整位置。

2、字节数据默认大小：缓冲区大小

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200907164021138.png" alt="image-20200907164021138" style="zoom: 67%;" />

3、溢出阈值

环形缓冲区达到移动的数据之后，向磁盘溢写。

这个环形缓冲区的空间大小占用比80%，即80M的时候开始写磁盘，预留20M的空间继续接收maptask的数据的写入。如果20M空间写满了，写入磁盘的80M空间还没有释放，写入数据的线程阻塞。其中，环形缓冲区中最后的内容，即使达不到溢写要求，最后也会flush刷新到磁盘中。

当80M的空间释放的时候，环形缓冲区会进行数据的移动，通过移动元数据和原始数据重新形成背靠背的模式，进而形成新的分界线。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200907164649482.png" alt="image-20200907164649482" style="zoom:67%;" />

## 3、mapreduce-shuffle

1、介绍

（1）流程

map -> shuffle -> reduce

（2）shuffle过程

依次是：分区，排序，分组，combiner

（3）先分区的目的

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200907165247428.png" alt="image-20200907165247428" style="zoom:67%;" />

分析如下：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200907165406391.png" alt="image-20200907165406391" style="zoom:50%;" />

2、逻辑代码讲解

（1）maptask运行的代码

![image-20200907165802812](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200907165802812.png)

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200907165935408.png" alt="image-20200907165935408" style="zoom:67%;" />

每一个maptask都会创建一个MyMapper的对象，调用一次run方法。

（2）reducetask代码讲解

每一个reducetask都会创建一个MyReducer，并且调用run方法。

3、流程图

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200907171414526.png" alt="image-20200907171414526" style="zoom: 50%;" />

![image-20200907171449078](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200907171449078.png)

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200907171516420.png" alt="image-20200907171516420" style="zoom:50%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20200907171539019.png" alt="image-20200907171539019" style="zoom: 33%;" />

## 4、reducejoin的实现

1、缺点

所有的join的工作的工作，都放到reduce端了。

（1）容易造成reduce端的数据倾斜。

（2）reducetask端的并行度不高。

（3）reducejoin的使用 list集合存在局限性。

## 5、倒序索引原理及案例分析

1、介绍

又叫反向索引，被用来存储在全文搜索下某个单词在一个文档或者一组文档中存储位置的映射。

一组文档：

```ini
1.txt
hello jack
hello baby
2.txt
tt zs jack
hello wukong
jack hello
```

正文索引的方式：

以文件建立索引，索引的是那些内容：

```ini
1.txt  hello-1,2 jack-1
2.txt  hello-2 jack-3
```

当我们要查找文件中，有哪些内容是比较方便的。但是按照内容查找，却是不太方便的，比如，查找hello出现在哪些文件中，因此我们使用下面的倒排索引的方式。

按照关键字进行索引（出现的文件，位置，次数）

```ini
hell0 - 1.txt,1,2 2.txt,2...
```

有两份数据如下：

```ini
1.txt
huangbo love xiaohei
hello hello baby
test test baby
```

创建两份数据的倒排索引：

```
huangbo  1.txt:1,1;
```

含义：关键字huangbo在1.txt的第1行（代码输出的是偏移量）出现了1次，在2.txt的第1行出现了1次。

2、分析

统计每一个单词 位置 在哪一个文档中出现的次数。

```ini
map端
	key:单词
	value:文件名+行偏移量+当前行出现的次数
	
reduce端
		接收的数据 相同单词的 所有位置的信息
		我们最后拼接位置信息就好了。
```

3、代码

```java
package com.xiaolun;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.FileSystem;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.LongWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.input.FileSplit;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;

import java.io.IOException;
import java.util.HashMap;
import java.util.Set;

public class Inverted_index {

	static class myMapper extends Mapper<LongWritable, Text, Text, Text> {
		Text mk = new Text();
		Text mv = new Text();
		String fileName = "";

		@Override
		protected void setup(Context context) throws IOException, InterruptedException {
			FileSplit split = (FileSplit) context.getInputSplit();
			fileName = split.getPath().getName(); //获取文件名
		}

		@Override
		protected void map(LongWritable key, Text value, Context context) throws IOException, InterruptedException {
			//获取每一行的内容
			String[] word = value.toString().split(" ");
			//先计算某一行某一个单词出现的次数
			HashMap<String, Integer> map = new HashMap<String, Integer>();
			for (String w : word) {
				if (!map.containsKey(w)) {
					map.put(w, 1);
				} else {
					map.put(w, map.get(w) + 1);
				}
			}
			//map集合中存放的就是每个单词出现的次数
			Set<String> keySet = map.keySet();
			for (String k : keySet) {
				mk.set(k);
				//huangbo  1.txt:1,1;2.txt:1,1
				mv.set(fileName + ":" + key.get() + "," + map.get(k));
				context.write(mk, mv);
			}
		}
	}

	static class myReduce extends Reducer<Text, Text, Text, Text> {
		Text rv = new Text();

		@Override
		protected void reduce(Text key, Iterable<Text> values, Context context) throws IOException, InterruptedException {
			StringBuffer buffer = new StringBuffer();
			for (Text v : values) {
				String index_content = v.toString();
				buffer.append(index_content).append(";");
			}
			rv.set(buffer.toString());
			context.write(key, rv);
		}
	}

	public static void main(String[] args) throws Exception {
		Configuration conf = new Configuration();
		//启动job
		Job job = Job.getInstance(conf);

		//设置jar主类
		job.setJarByClass(Inverted_index.class);

		//设置map reduce类
		job.setMapperClass(myMapper.class);
		job.setReducerClass(myReduce.class);

		//设置map reduce的输出
		/**
		 * 如果map输出的k v 和 reduce 输出的k v 一致，只需要设置一个就可以
		 */
		job.setOutputKeyClass(Text.class);
		job.setOutputValueClass(Text.class);

		//输入/出路径
		FileInputFormat.setInputPaths(job, new Path("D:\\tmp\\indexTest\\input"));
		Path out = new Path("D:\\tmp\\indexTest\\output");
		FileSystem fileSystem = FileSystem.get(conf);
		if (fileSystem.exists(out)) {
			fileSystem.delete(out, true);
		}
		FileOutputFormat.setOutputPath(job, out);

		//提交任务
		job.waitForCompletion(true);
	}
}
```

输出结果：

```ini
baby	1.txt:40,1;1.txt:22,1;
hello	1.txt:22,2;
huangbo	1.txt:0,1;
love	1.txt:0,1;
test	1.txt:40,2;
xiaohei	1.txt:0,1;
```









