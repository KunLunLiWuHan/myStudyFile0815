## 1、numpy操作

### 1、numpy的属性

```python
import numpy as np

# 一维矩阵 2 * 3
matrix_test = [[1, 2, 3],
               [4, 5, 6]]
# 将矩阵转化为数组
array = np.array(matrix_test)
# 二维数组
# [[1 2 3]
#  [4 5 6]]
print(array)
# 数组的长度：输出 2
print(len(array))
# 数组的维度：2
print("number of dim:", array.ndim)
# 数组的形状,输出：shape: (2, 3)
print("shape:", array.shape)
# 输出：size: 6
print("size:", array.size)
```

### 2、numpy的拷贝

```python
import numpy as np

t1 = np.arange(4)
t2 = t1  # 浅拷贝，t1改变会影响t2
t1[0] = 10
# [10  1  2  3]
print(t2)
t3 = t1.copy()  # 深拷贝
t1[1] = 20
# [10  1  2  3]
print(t3)
```

### 3、numpy创建Array

```python
import numpy as np

# 创建一个一维Array数组，使用list表的形式，并指令类型
test = np.array([1, 2, 3], dtype=np.int)
# 输出：[1 2 3]
print(test)
# 输出：int32
print(test.dtype)

# 定义一个二维的矩阵
test2 = np.array([[1, 2, 3],
                  [4, 5, 6]])
print(test2)

# 定义一个2*3的零矩阵
# [[0. 0. 0.]
#  [0. 0. 0.]]
test3 = np.zeros((2, 3))
print(test3)

# 定义全1矩阵
# [[1 1 1]
#  [1 1 1]]
test4 = np.ones((2, 3), dtype=np.int16)
print(test4)

# 空矩阵
# [[0. 0. 0.]
#  [0. 0. 0.]]
test5 = np.empty((2, 3))
print(test5)

# 生成一个3*4的矩阵
# [[ 0  1  2  3]
#  [ 4  5  6  7]
#  [ 8  9 10 11]]
test6 = np.arange(12).reshape((3, 4))
print(test6)

print("--------------矩阵操作-----------------")
n1 = np.mat([1, 2, 3])
print(n1)  # 输出：[[1 2 3]]
print(type(n1))  # <class 'numpy.matrix'>
```

### 4、numpy的基础运算

```python
import numpy as np

test1 = np.array([2, 3, 4])
test2 = np.arange(3)
# 输出：[0 1 2]
print(test2)
sub = test1 - test2
# 输出：[2 2 2]
print(sub)

test3 = np.sin(test1)
print(test3)

test4 = np.array([[1, 1],
                  [0, 1]])
test5 = np.array([[0, 1],
                  [2, 3]])

# 对应项相乘
# [[0 1]
#  [0 3]]
test6 = test4 * test5

# 矩阵真正的乘法
# [[2 4]
#  [2 3]]
test_doc = np.dot(test4, test5)
print(test6)
print(test_doc)

# 创建一个2*3矩阵的随机数
# [[0.40444673 0.60621217 0.7454439 ]
#  [0.06838504 0.19788044 0.97726666]]
t1 = np.random.random((2, 3))
print(t1)

# 在每一列中求最大值
print("----------------")
print(np.max(t1, axis=0))
# 在每一行中求最大值
print("----------------")
print(np.max(t1, axis=1))

print("-------------------第二阶段-------------------")
m1 = np.arange(1, 5).reshape((2, 2))
# 返回当前矩阵最大值的索引
print(np.argmax(m1))

# 输出：
# [[1 2]
#  [2 2]]
# 切割，[1，2]之间的数为原始值，大于2的数为2，小于1的值为1
print(np.clip(m1, 1, 2))
```

### 5、numpy的索引

```python
import numpy as np

t1 = np.arange(3, 12)
# [3 4 5 6 7 8 9]
print(t1)
# 输出：5
print(t1[2])

t2 = np.arange(3, 12).reshape((3, 3))
print("t2 type ", type(t2))  # <class 'numpy.ndarray'>
print(t2)
# 输出第3行：[ 9 10 11]
print(t2[2])
# 索引第2行第2列，下面输出等价
print(t2[1][1])
print(t2[1, 1])

# 输出第2行的所有元素：[6 7 8]
print(t2[1, :])

# 变成一行，输出：[ 3  4  5  6  7  8  9 10 11]
print(t2.flatten())
```

### 6、numpy合并array

```python
import numpy as np

t1 = np.array([1, 1, 1])
print(type(t1))  # <class 'numpy.ndarray'>
print(t1.shape)  # 输出：(3,)!!!!!!!。说明t1是一个序列，不是矩阵

# 方法1：转换成矩阵操作
t1_2_mat = np.mat(t1)
print(type(t1_2_mat))  # <class 'numpy.matrix'>
print("t1_2_mat.shape:", t1_2_mat.shape)  # (1, 3)

# 方法2：再添加一个[],变换一个格式
m1 = np.array([[1, 2, 3]])
print(m1)
print(m1.shape)  # (1, 3)

t2 = np.array([2, 2, 2])
t3 = np.vstack((t1, t2))  # 上下合并
t4 = np.hstack((t1, t2))  # 左右合并
# 输出：
# [[1 1 1]
#  [2 2 2]]
print("t3：", t3)

print("--------------------------------")
n1 = t1[:, np.newaxis]  # 插入新维度,将一维的数据转变成一个矩阵
# 输出：
# [[1]
#  [1]
#  [1]]
print(n1)
print(n1.shape)  # (3, 1)
print(type(n1))  # <class 'numpy.ndarray'>

n2 = t1[np.newaxis, :]  # 插入新维度
print(n2)  # [[1 1 1]]
print(n2.shape)  # (1, 3)
```

### 7、numpy中array的分割

```python
import numpy as np

t1 = np.arange(12).reshape((3, 4))
# 对于列对等分割(分成2块).输出
# [array([[0, 1],
#        [4, 5],
#        [8, 9]]),
# array([[ 2,  3],
#        [ 6,  7],
#        [10, 11]])]
print(np.split(t1, 2, axis=1))
# 不等分割，输出
# [array([[0, 1],
#        [4, 5],
#        [8, 9]]), array([[ 2],
#        [ 6],
#        [10]]), array([[ 3],
#        [ 7],
#        [11]])]
print(np.array_split(t1, 3, axis=1))

print("-----------简化的分割函数-----------")
m1 = np.vsplit(t1, 3)  # row方向
# [array([[0, 1, 2, 3]]), array([[4, 5, 6, 7]]), array([[ 8,  9, 10, 11]])]
print(m1)
print(type(m1))  # 输出：<class 'list'>
m2 = np.hsplit(t1, 2)  # col方向
print(m2)
# [array([[0, 1],
#        [4, 5],
#        [8, 9]]), array([[ 2,  3],
#        [ 6,  7],
#        [10, 11]])]
print(type(m2))  # 输出：<class 'list'>
```

## 2、pandas操作

### 1、pandas介绍

```python
import pandas as pd
import numpy as np

s1 = pd.Series([2, 4, 6])
# 输出：左边有序号，右边是数据
# 0    2
# 1    4
# 2    6
print("s1", s1)

dates = pd.date_range("20201016", periods=2)
# DatetimeIndex(['2020-10-16', '2020-10-17'], dtype='datetime64[ns]', freq='D')
print(dates)

# DataFrame相当于一个二维矩阵
df = pd.DataFrame(np.random.randn(2, 2), index=dates, columns=['a', 'b'])
# 输出：以dates作为左侧行的索引，以字母a,b作为列的索引
#                    a        b
# 2020-10-16  0.442655 -0.22998
# 2020-10-17 -1.157189 -0.27214
print(df)

df2 = pd.DataFrame(
    {"A": 1, "B": pd.Timestamp("20201016"), "c": "foo", "d": pd.Series([3] * 4, index=list(range(4, 8)))})
# 1、字典中d的index指定索引值，不然会报错
# 2、c中的foo和B自动填充
#    A          B    c  d
# 4  1 2020-10-16  foo  3
# 5  1 2020-10-16  foo  3
# 6  1 2020-10-16  foo  3
# 7  1 2020-10-16  foo  3
print(df2)
# Int64Index([4, 5, 6, 7], dtype='int64')
print(df2.index)
# Index(['A', 'B', 'c', 'd'], dtype='object'
print(df2.columns)
# 将矩阵中的值打印出来
print(df2.values)

# 对df2的描述，方差，平均值什么的。
#        A    d
# count  4.0  4.0
# mean   1.0  3.0
# std    0.0  0.0
# min    1.0  3.0
# 25%    1.0  3.0
# 50%    1.0  3.0
# 75%    1.0  3.0
# max    1.0  3.0
print(df2.describe())

print("--------------排序后输出-----------------")
sort1 = df2.sort_index(axis=0, ascending=False)
# 根据索引值对行进行降序输出
#    A          B    c  d
# 7  1 2020-10-16  foo  3
# 6  1 2020-10-16  foo  3
# 5  1 2020-10-16  foo  3
# 4  1 2020-10-16  foo  3

# 指定对A列中的数据进行排序
# sort1 = df2.sort_values(by="A", ascending=False)
print(sort1)
```

### 2、pandas中选择数据

```python
dates = pd.date_range("20201016", periods=3)
df = pd.DataFrame(np.arange(12).reshape(3, 4), index=dates, columns=['a', 'b', 'c','d'])
# 输出
#             a  b   c   d
# 2020-10-16  0  1   2   3
# 2020-10-17  4  5   6   7
# 2020-10-18  8  9  10  11
print(df)
# 选取第一行的数据
print(df[0:1])
```

### 3、设置值

```python
import pandas as pd
import numpy as np

dates = pd.date_range("20130101", periods=3)
df = pd.DataFrame(np.arange(12).reshape(3, 4), index=dates, columns=['A', 'B', 'C', 'D'])

# 对坐标（2，2）的数据进行赋值
# df.iloc[2, 2] = 1111  # 推荐使用这一个
# df.loc['20130101', 'B'] = 2222

# 将A列中大于0对应这一行的数据置为0
# df[df.A > 0] = 0

# 将A列中大于0的数据赋值为0
# df.A[df.A > 0] = 0

# 添加E列，并赋值为NaN
df["E"] = np.nan
# 添加F列，
df["F"] = pd.Series([1, 2, 3], index=pd.date_range("20130101", periods=3))
print(df)
```

### 4、处理丢失数据

```python
import pandas as pd
import numpy as np

dates = pd.date_range("20130101", periods=3)
df = pd.DataFrame(np.arange(12).reshape(3, 4), index=dates, columns=['A', 'B', 'C', 'D'])
df.iloc[0, 0] = np.nan
df.iloc[0, 1] = np.nan
# print(df)
# 过滤数据中的缺失数据。
# how='any'表示如果在一行中有Nan的数据，就把这一行丢掉
# how='all'表示这一行全部等于Nan才会被过滤。
# print(df.dropna(axis=0, how='all'))
# 将nan数据替换为0
print(df.fillna(value=0))
```

### 5、合并数据

1、使用concat

```python
import pandas as pd
import numpy as np

df1 = pd.DataFrame(np.ones((3, 4)) * 0, columns=['a', 'b', 'c', 'd'])
df2 = pd.DataFrame(np.ones((3, 4)) * 1, columns=['a', 'b', 'c', 'd'])

# 对行进行合并，忽略索引。
# 默认join="inner"(outer表示外连接，将不相同的进行补全)
# res = pd.concat([df1, df2], axis=0, join="inner",ignore_index=True)

print("-----------------追加数据----------------")
s1 = pd.Series([1, 2, 3, 4], index=['a', 'b', 'c', 'd'])
res2 = df1.append(s1, ignore_index=True)
print(res2)
```

2、使用merge

```python
import pandas as pd
import numpy as np

left = pd.DataFrame({'key': ['k0', 'k1'], 'A': ['A0', 'A1']})
right = pd.DataFrame({'key': ['k0', 'k1'], 'B': ['B0', 'B1']})
# 按照key进行合并
# how=['left','right','outer','inner']
# indicator提示merge以怎么样的形式进行合并
res = pd.merge(left, right, on='key', how='right', indicator=True)
print(res)
```

## 3、Matplotlib操作

### 1、绘制直线和折线

1、代码

```python
import matplotlib.pyplot as plt

# 准备绘制的两个点(1,2) (4,8)
# 调用绘制plot方法
plt.plot([1, 4], [4, 8])
# 显示绘制的图
plt.show()

print("------------绘制y=x^2的线段----------------")
# 准备绘制的点
x = [1, 2, 3, 4, 5]
y = [1, 4, 9, 16, 25]
# 调用绘制方法
plt.plot(x, y)
# 显示图
plt.show()
```

![image-20201020165415242](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201020165415242.png)

### 2、设置样式

1、代码

```python
import matplotlib.pyplot as plt
import numpy as np

# 解决图像中负号不显示的问题
plt.rcParams['axes.unicode_minus'] = False

# 准备x y
x = [1, 2, 3, 4, 5]
y = [1, 4, 9, 16, 25]

# 绘制折线
# linewidth属性设置线条的宽度
plt.plot(x, y, linewidth=5)
# 添加x y轴名称
plt.xlabel('x')
plt.ylabel('y=x^2')
plt.rcParams['font.sans-serif'] = ['SimHei']  # 用来正常显示中文标签
# 给图标添加标题
plt.title('多个点绘制折线图')
plt.show()

print("----------------绘制一元二次方程曲线-----------------")
# 准备x  y
x = range(-100, 100)  # 200个点
y = [i ** 2 for i in x]
# 绘制一元二次方程曲线
plt.plot(x, y)
# 保存图片
plt.savefig('result')  # 默认的格式png
# plt.savefig('result.jpg')
plt.show()

print("----------------绘制正弦曲线-----------------")
# 生成0-10之间 100个等差数
x = np.linspace(0, 10, 100)
sin_y = np.sin(x)
# 进行绘制正弦曲线
plt.plot(x, sin_y)

cos_y = np.cos(x)
# 绘制余弦曲线
plt.plot(x, cos_y)
# 保存成图片，图片中蓝色为第一个曲线，黄色为第二个
plt.savefig('sin_cos.jpg')
plt.show()
```

结果展示：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201020165633433.png" alt="image-20201020165633433" style="zoom:67%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201020165649037.png" alt="image-20201020165649037" style="zoom:67%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201020165709041.png" alt="image-20201020165709041" style="zoom:67%;" />

### 3、subplot的使用

1、代码

```python
from matplotlib import pyplot as plt
import numpy as np

# 创建0-10中100个等差数
x = np.linspace(0, 10, 100)
sin_y = np.sin(x)
# 绘制正弦曲线
# 对画布进行分区 将画布分为2行2列  画到区1
plt.subplot(2, 2, 1)
# 修改x、y轴的坐标
plt.xlim(-5, 20)
plt.ylim(-2, 2)
plt.plot(x, sin_y)

# 画到区4
plt.subplot(2, 2, 4)
plt.plot(x, np.cos(x))
plt.show()
```

结果展示：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201020165812321.png" alt="image-20201020165812321" style="zoom:67%;" />

### 4、绘制散点图

1、介绍

使用plot绘制和使用scatter绘制出来的图形是没有区别的，
但是使用plot绘制图形的速度由于scatter，所以如果画一堆点，而且点的形式没有差别，那么我们使用plot，如果点的形式有差别(指点的大小和颜色不同)则必须使用scatter。

2、代码-简单散点图

```python
import matplotlib.pyplot as plt
import numpy as np

# 生成0-10之间100个等差数
x = np.linspace(0, 10, 100)
sin_y = np.sin(x)
# 使用plot绘制正弦的曲线的散点图
# plt.plot(x, sin_y, 'o')
# 绘制散点图
plt.scatter(x,sin_y)
plt.show()
```

结果展示：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201020165926317.png" alt="image-20201020165926317" style="zoom:67%;" />

3、代码-

```python
import matplotlib.pyplot as plt
import numpy as np

# 画10种大小， 10种颜色的散点图
np.random.seed(0)  # 执行多次每次获取的随机数都是一样的
x = np.random.rand(10)
print("x", x)
y = np.random.rand(10)
print("y", y)
# 10种的颜色
colors = np.random.rand(10)
# 生成10种大小,*100作用是将点的大小放大放大。其个数和x或者y的个数相同
size = np.random.rand(10) * 100
# colors表示点的颜色, size表示点的大小,alpha表示透明度(用于重叠问题)
plt.scatter(x, y, c=colors, s=size, alpha=0.7)
plt.show()
```

结果展示：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201020170035656.png" alt="image-20201020170035656" style="zoom:67%;" />

### 5、绘制不同颜色直线并添加图例

1、代码

```python
import matplotlib.pyplot as plt
import numpy as np

x = np.linspace(0, 10, 100)
# 使用legend()图例,给plot方法添加参数label
plt.plot(x, x + 0, '--g', label='--g')
plt.plot(x, x + 1, '-.r', label='-.r')
plt.plot(x, x + 2, ':b', label=':b')
plt.plot(x, x + 3, '.k', label='.k')
plt.plot(x, x + 4, ',c', label=',c')
plt.plot(x, x + 5, '*y', label='*y')
# 左上角upper left fancybox边框  framealpha透明度  shadow阴影  borderpad边框宽度
plt.legend(loc='upper right', fancybox=True, framealpha=0.5, shadow=True,
           borderpad=1)  # 默认的位置在左上角upper left  可以通过loc进行修改
plt.show()
```

结果展示：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201020170226064.png" alt="image-20201020170226064" style="zoom:80%;" />

### 6、绘制柱状图

1、代码-简单介绍

```python
import matplotlib.pyplot as plt
import numpy as np

# 创建x ，y  表示x年份  y表示年份对应的销量
x = [1980, 1985, 1990, 1995]
x_label = ['1980年', '1985年', '1990年', '1995年']
y = [1000, 3000, 4000, 5000]
# 调用bar函数绘制柱状图
plt.bar(x, y, width=3)  # width修改柱的宽度
# 修改中文乱码
plt.rcParams['font.sans-serif'] = ['SimHei']  # 用来正常显示中文标签
# 修改x坐标的值
plt.xticks(x, x_label)
# 给x坐标y坐标添加名称
plt.xlabel('年份')
plt.ylabel('销量')
# 添加标题title
plt.title('根据年份销量对比图')
plt.show()
```

结果展示：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201020170315369.png" alt="image-20201020170315369" style="zoom:67%;" />

2、代码-bar和barh函数的使用

```python
import matplotlib.pyplot as plt
import numpy as np

# 生成x y
np.random.seed(0)
x = np.arange(5)
# 在[-5,5]之间生成5个数据，输出为：[ 0 -5 -2 -2  2]
y = np.random.randint(-5, 5, 5)
# 将画布分为1行2列  在第一个区域画bar
plt.subplot(1, 2, 1)
# 添加颜色
plt.bar(x, y, color='blue')
# 在0位置水平方向添加蓝色的线条
plt.axhline(0, color='blue', linewidth=2)

# 在第二个区域画barh
# barh  将y和x进行对换，竖着方向为x轴
plt.subplot(1, 2, 2)
plt.barh(x, y, color='red')
# 在0位置垂直方向添加红色线条
plt.axvline(0, color='red', linewidth=2)

plt.show()
```

结果展示：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201020170416082.png" alt="image-20201020170416082" style="zoom:67%;" />

3、代码-设置不同颜色的柱

```python
import matplotlib.pyplot as plt
import numpy as np

# 生成x y
np.random.seed(0)
x = np.arange(5)
y = np.random.randint(-5, 5, 5)
# 添加颜色
v_bar = plt.bar(x, y, color='blue')

# 对y值大于0设置为蓝色  小于0的柱设置为绿色
for bar, height in zip(v_bar, y):
    if height < 0:
        bar.set(color='green')

plt.show()
```

结果展示：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201020170527280.png" alt="image-20201020170527280" style="zoom:67%;" />

4、柱状图的使用

```python
import matplotlib.pyplot as plt
import numpy as np

# 准备数据
# 三部电影的名称
real_names = ['千与千寻', '玩具总动员4', '黑衣人：全球追缉']
# 3天内票房书
real_num1 = [7548, 4013, 1673]
real_num2 = [5453, 1840, 1080]
real_num3 = [4348, 2345, 1890]
x = np.arange(len(real_names))
# 绘制柱状图
width = 0.3
plt.bar(x, real_num1, alpha=0.5, width=width, label=real_names[0])
plt.bar([i + width for i in x], real_num2, alpha=0.5, width=width, label=real_names[1])
plt.bar([i + 2 * width for i in x], real_num3, alpha=0.5, width=width, label=real_names[2])
plt.rcParams['font.sans-serif'] = ['SimHei']  # 用来正常显示中文标签
# 设置x坐标的值  第1天   第2天   第3天
# xticks(ticks, [labels], **kwargs)
# ticks：数组类型，用于设置X轴刻度间隔
# [labels]：数组类型，用于设置每个间隔的显示标签
x_label = ['第{}天'.format(i + 1) for i in x]
# print(x_label)
plt.xticks([i + width for i in x], x_label)
# 添加ylabel
plt.ylabel('票房书')
# 添加图例
plt.legend()
# 添加标题
plt.title('3天3部电影票房数')
plt.show()
```

结果展示：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201020170611185.png" alt="image-20201020170611185" style="zoom:67%;" />

### 7、绘制饼状图

1、代码

```python
import matplotlib.pyplot as plt
import numpy as np

# 准备男、女的人数及比例
man = 71351
woman = 68187
man_perc = man / (woman + man)
woman_perc = woman / (woman + man)
# 添加名称
labels = ['男', '女']
# 添加颜色
colors = ['blue', 'red']
# 绘制饼状图  pie
plt.rcParams['font.sans-serif'] = ['SimHei']  # 用来正常显示中文标签
# labels 名称 colors：颜色，explode=分裂  autopct显示百分比（保留一位小数）
paches, texts, autotexts = plt.pie([man_perc, woman_perc], labels=labels, colors=colors, explode=(0, 0.05),
                                   autopct='%0.1f%%')

# 设置饼状图中的字体颜色
for text in autotexts:
    text.set_color('white')

# 设置字体大小
for text in texts + autotexts:
    text.set_fontsize(20)
plt.show()
```

结果展示：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201020170710380.png" alt="image-20201020170710380" style="zoom:67%;" />

### 8、绘制直方图

1、代码

```python
import matplotlib.pyplot as plt
import numpy as np

# 生成10个标志的正态分布随机数
x = np.random.randn(10)
# plt.hist(x)
# 修改柱的宽度  使用bins
# bins参数指定bin(箱子)的个数,也就是总共有几条条状图，下面有100个bins
plt.hist(x, bins=2)  # (10/2)正态分布中的每个5个柱（数）装在一起
plt.show()

print("-------------------------")
# 使用np.random.normal()指定期望和均值的正太分布
# loc（均值），scale（标准差）， size
x = np.random.normal(0, 0.8, 1000)
y = np.random.normal(-2, 1, 1000)
z = np.random.normal(3, 2, 1000)
# 创建字典类型的数据，并将数据传到直方图plt.hist中
kwargs = dict(bins=100, alpha=0.5)  # alpha是透明度
plt.hist(x, **kwargs)
plt.hist(y, **kwargs)
plt.hist(z, **kwargs)
plt.show()
```

结果显示：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201020170802392.png" alt="image-20201020170802392" style="zoom:67%;" />

### 9、绘制等高图

1、代码

```python
import matplotlib.pyplot as plt
import numpy as np

# 创建x y（-10,10）之间有100个等差点
x = np.linspace(-10, 10, 100)
print("x", x)
y = np.linspace(-10, 10, 100)
print("y", y)

# 计算x y相交的点 X Y
X, Y = np.meshgrid(x, y)
# 计算Z
Z = np.sqrt(X ** 2 + Y ** 2)
print(type(Z))
print("Z", Z)
# 绘制等高线图，Z表示网格数据对应网格的高度值
# contour() 是绘制轮廓线，contourf()会填充轮廓
# plt.contour(X, Y, Z)
plt.contourf(X, Y, Z)
plt.show()
```

结果展示：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201020170853124.png" alt="image-20201020170853124" style="zoom:67%;" />

### 10、绘制3D图案

1、代码

```python
import matplotlib.pyplot as plt
# 导入3d包
from mpl_toolkits.mplot3d import Axes3D

# 创建X  Y  Z
X = [1, 1, 2, 2]
Y = [3, 4, 4, 3]
Z = [1, 100, 1, 1]
figure = plt.figure()
# 创建Axes3D对象
ax = Axes3D(figure)
ax.plot_trisurf(X, Y, Z)
plt.show()
```

结果展示：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201020170945050.png" alt="image-20201020170945050" style="zoom:67%;" />