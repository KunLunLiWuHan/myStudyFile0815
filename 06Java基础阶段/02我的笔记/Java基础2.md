## 1、Java比较器

1、介绍

在Java中经常会涉及到对象数组的排序问题，那么就涉及到对象之间的比较问题。

Java实现对象排序的方式有两种： 

自然排序：java.lang.Comparable 

定制排序：java.util.Comparator

一般来说，Java中通过接口实现两个对象的比较，比较常用就是Comparable接口和Comparator接口。首先类要实现接口，并且使用泛型规定要进行比较的对象所属的类，然后在该类类实现了接口后，还需要实现接口定义的比较方法，在这些方法中传入需要比较大小的另一个对象，通过**选定的成员变量**与之比较，如果大于则返回1，小于返回-1，相等返回0。

### 2、Comparable

1、自然排序

（1）实现方式

String：按照字符串中字符的Unicode值进行比较 。

Character：按照字符的Unicode值来进行比较 。

数值类型对应的包装类以及BigInteger、BigDecimal：按照它们对应的数值 大小进行比较。

Boolean：true 对应的包装类实例大于 false 对应的包装类实例。

Date、Time等：后面的日期时间比前面的日期时间大。

（2）代码

```java
public static void test1() {
  String[] arr = new String[]{"aa", "aab", "ddc", "dd"};
  Arrays.sort(arr); //对上面的数组进行排序
  // 排序后的数组输出 [aa, bb, cc, dd]
  System.out.println(Arrays.toString(arr));
}
```

2、自定义排序

（1）比较对象

```java
//实现Comparable接口
public class User implements Comparable<User> {
	private String name;
	private int price;

	public User() {
	}

	public User(String name, int price) {
		this.name = name;
		this.price = price;
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public int getPrice() {
		return price;
	}

	public void setPrice(int price) {
		this.price = price;
	}

	@Override
	public String toString() {
		return "User{" +
				"name='" + name + '\'' +
				", price=" + price +
				'}';
	}

	//指定用户比较大小的方式
	@Override
	public int compareTo(User o) {
		//方式1
		User user = o;
		if (this.price > user.price) {
			return 1;
		} else if (this.price < user.price) {
			return -1;
		} else {
			return 0;
		}
		//方式2
//			return Integer.compare(this.price, user.price);

	}
}
```

（2）测试

```java
public static void test2() {
		User[] usersArr = new User[4];
		usersArr[0] = new User("xiaomiMouse", 10);
		usersArr[1] = new User("dellMouse", 20);
		usersArr[2] = new User("lenovoMouse", 40);
		usersArr[3] = new User("huaweiMouse", 30);

		Arrays.sort(usersArr);
		System.out.println(Arrays.toString(usersArr));
	}
```

控制台输出：

```
[User{name='xiaomiMouse', price=10}, User{name='dellMouse', price=20}, User{name='huaweiMouse', price=30}, User{name='lenovoMouse', price=40}]
```

### 3、Comparator

1、介绍

当元素的类型没有实现java.lang.Comparable接口而又不方便修改代码，或者实现了java.lang.Comparable接口的排序规则不适合当前的操作，那 么可以考虑使用 Comparator 的对象来排序，强行对多个对象进行整体排序的比较。

2、测试代码

```java
public static void test2() {
   User[] usersArr = new User[5];
   usersArr[0] = new User("xiaomiMouse", 10);
   usersArr[1] = new User("dellMouse", 20);
   usersArr[2] = new User("lenovoMouse", 40);
   usersArr[3] = new User("huaweiMouse", 40);
   usersArr[4] = new User("huaweiMouse", 30);

   //指明用户比较大小的方式：按照用户名称从低到高排序，再按照价格从高到低排序
   Arrays.sort(usersArr, new Comparator<User>() {
      @Override
      public int compare(User o1, User o2) {
         User user1 = o1;
         User user2 = o2;
         if (user1.getName().equals(user2.getName())) {
            return -Integer.compare(user1.getPrice(), user2.getPrice());
         } else {
            return user1.getName().compareTo(user2.getName());
         }
      }
   });
   System.out.println(Arrays.toString(usersArr));
}
```

3、Comparable和Compatator使用比较

Comparable接口的方式一旦一定，就可以保证该接口实现类的对象在任何位置都可以比较大小。Compatator接口只是一个临时性的比较，什么时候需要什么时候创建，优先级比Comparable接口高。

## 2、String

1、String相关转换测试

```java
/**
 * @time: 2020-09-21 10:17
 * @author: likunlun
 * @description: String测试
 */
public class Test04 {
	public static void main(String[] args) {
		char[] chars = {'a', 'b'}; //字符数组
		for (char aChar : chars) {
			// 输出 a,b
			System.out.println(aChar);
		}
		System.out.println("--------------------------");
		/**
		 * 	 byte中可以存放一个ascii字符(ascii范围：0-127)、十进制数值(-128-127)、
		 *  十六进制数值(0x00 - 0x79，十进值范围：0-127)
		 */
		byte[] bytes = {'a', 'b'};
		for (byte aByte : bytes) {
			//输出 97，98
			System.out.println(aByte);
		}
		System.out.println("--------------------------");
//		charArray2String(chars);
//		byteArray2String(bytes);
		String str = "abc";
		String2byteArray(str);
	}

	//	字符串 -》 字节数组
	private static void String2byteArray(String str) {
		for (byte aByte : str.getBytes()) {
			//输出 97 98 99
			System.out.println(aByte);
		}
	}

	//	字节数组 -》字符串
	private static void byteArray2String(byte[] bytes) {
		String str = new String(bytes);
		//输出ab
		System.out.println(str);
	}

	//字符数组 -》 字符串
	private static void charArray2String(char[] chars) {
		String str = new String(chars);
		//输出ab
		System.out.println(str);
	}
}
```

2、StringBuffer测试

```java
public class Test05 {
	public static void main(String[] args) {
		//可变的字符穿
		StringBuffer buffer = new StringBuffer("aaa");
		 buffer.append("bbbb");
		 // aaabbbb
		System.out.println(buffer);
	}
}
```

## 3、枚举类

1、介绍

类的对象只有有限个，是确定的。因此，当需要定义一组常量时，强烈建议使用枚举类。

（1）枚举类的实现

JDK1.5之前需要自定义枚举类，之后使用新增的 enum 关键字用于定义枚举类。

若枚举只有一个对象, 则可以作为一种单例模式的实现方式。

（2）枚举类的属性

枚举类对象的属性不应允许被改动, 所以应该使用 private final 修饰。

枚举类的使用 private final 修饰的属性应该在构造器中为其赋值。

若枚举类显式的定义了带参数的构造器, 则在列出枚举值时也必须对应的传参数。

2、自定义枚举类

（1）介绍

私有化类的构造器，保证不能在类的外部创建其对象。

在类的内部创建枚举类的实例。声明为：public static final。

对象如果有实例变量，应该声明为private final，并在构造器中初始化。

（2）编写代码测试

```java
/**
 * @time: 2020-09-21 10:36
 * @author: likunlun
 * @description: String测试
 */
public class Test05 {
   public static void main(String[] args) {
      //自定义枚举类
      Season spring = Season.SPRING;
      /**
       * 输出结果如下：
       * Season{seasonName='春天', seasonDesc='春暖花开'}
       * 春天
       * 春暖花开
       */
      System.out.println(spring);
      System.out.println(spring.getSeasonName());
      System.out.println(spring.getSeasonDesc());
   }
}

//自定义枚举类
class Season {
   //1、声明Season对象的属性：private final
   private final String seasonName;
   private final String seasonDesc;

   //2、私有化类的构造器，并给对象属性赋值
   private Season(String seasonName, String seasonDesc) {
      this.seasonName = seasonName;
      this.seasonDesc = seasonDesc;
   }

   //3、提供当前枚举类的多个对象
   public static final Season SPRING = new Season("春天", "春暖花开");
   public static final Season SUMMER = new Season("夏天", "夏日炎炎");
   public static final Season AUTUMN = new Season("秋天", "秋高气爽");
   public static final Season WINTER = new Season("冬天", "冰天雪地");

   //4、其他诉求：获取枚举类对象的属性
   //只有get方法，没有set方法
   public String getSeasonName() {
      return seasonName;
   }

   public String getSeasonDesc() {
      return seasonDesc;
   }

   @Override
   public String toString() {
      return "Season{" +
            "seasonName='" + seasonName + '\'' +
            ", seasonDesc='" + seasonDesc + '\'' +
            '}';
   }
}
```

3、使用enum定义枚举类

（1）介绍

使用 enum 定义的枚举类默认继承了 java.lang.Enum类，因此不能再 继承其他类 

枚举类的构造器只能使用 private 权限修饰符，枚举类的所有实例必须在枚举类中显式列出(, 分隔 ; 结尾)。

系统会为列出的实例自动添加 public static final 修饰。

必须在枚举类的第一行声明枚举类对象，不然会报错。

Enum类的主要方法：

```
values()方法：返回枚举类型的对象数组。该方法可以很方便地遍历所有的 枚举值。 
valueOf(String str)：可以把一个字符串转为对应的枚举类对象。要求字符 串必须是枚举类对象的“名字”。如不是，会有运行时异常： IllegalArgumentException。 
toString()：返回当前枚举类对象常量的名。
```

（2）编写代码

枚举类

```java
//使用enum定义枚举类
public enum SeasonEnum {
   SPRING("春天", "春风又绿江南岸"),
   SUMMER("夏天", "映日荷花别样红"),
   AUTUMN("秋天", "秋水共长天一色"),
   WINTER("冬天", "窗含西岭千秋雪");
   private final String seasonName;
   private final String seasonDesc;

   private SeasonEnum(String seasonName, String seasonDesc) {
      this.seasonName = seasonName;
      this.seasonDesc = seasonDesc;
   }

   public String getSeasonName() {
      return seasonName;
   }

   public String getSeasonDesc() {
      return seasonDesc;
   }
}
```

测试

```java
public class Test05 {
   public static void main(String[] args) {
      // 使用enum定义枚举类
      /**
       * 输出结果如下：
       * Season{seasonName='春天', seasonDesc='春暖花开'}
       * 春天
       * 春暖花开
       */
      System.out.println(SeasonEnum.SPRING.toString());
      System.out.println(SeasonEnum.SPRING.getSeasonName());

      System.out.println("----------------------------------");
      /**
       * 遍历枚举值，输出结果如下：
       * SPRING
       * SUMMER
       * AUTUMN
       * WINTER
       */
      SeasonEnum[] values = SeasonEnum.values();
      for (SeasonEnum value : values) {
         System.out.println(value);
      }

      //把一个字符串(SUMMER)转为对应的枚举类对象
      System.out.println("----------------------------------");
      SeasonEnum summer = SeasonEnum.valueOf("SUMMER");
      //SUMMER
      System.out.println(summer);
   }
}
```