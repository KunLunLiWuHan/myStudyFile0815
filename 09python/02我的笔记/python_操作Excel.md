### 1、创建Excel文件

1、代码

```python
import pandas as pd

# 创建一个两列的数据框，
df = pd.DataFrame({'ID': [1, 2, 3], 'Name': ['Tom', 'Jack', 'Nick']})
# 为excel表格添加上指定索引,如果不设置，索引默认为1，2，3
df = df.set_index('ID')
df.to_excel('D:/output.xlsx')
print("Done!")
```

此时，会在指定目录下创建一个文件output.xlsx，文件内容为：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201009213205140.png" alt="image-20201009213205140" style="zoom:80%;" />

### 2、读取Excel文件

1、介绍

（1）header ：指定作为列名的行，默认为0，即取第一行，数据为列名行以下的数据；若数据不含列名，则设定 header = None；

2、代码

```python
# 文件的读取
import pandas as pd

"""
1、当表格不含表头时，使用header=None不显示头信息
2、可以使用下面的语句为表格添加头信息
"""
# people = pd.read_excel("D:\\People.xlsx", header=None)
# people.columns = ('ID', 'Type', 'Title', 'FirstName', 'MiddleName', 'LastName')

people = pd.read_excel("D:\\People.xlsx")
# 列表的（行，列）的尺寸，输出：(19972, 6)
print(people.shape)
# 列表的列信息，输出
# Index(['ID', 'Type', 'Title', 'FirstName', 'MiddleName', 'LastName'], dtype='object')
print(people.columns)
"""
由于excel表格太长，我们展示只显示头信息（默认只看前5行）：
   ID      Type Title FirstName MiddleName    LastName
0   1  Employee   NaN       Ken          J     Sánchez
1   2  Employee   NaN     Terri        Lee       Duffy
2   3  Employee   NaN   Roberto        NaN  Tamburello
3   4  Employee   NaN       Rob        NaN     Walters
4   5  Employee   Ms.      Gail          A    Erickson
"""
# print(people.head())
# 查看前3行
print(people.head(3))
# 查看后3行
# print(people.tail(3))
```

### 3、写数据

1、代码

```python
import pandas as pd

l1 = [100, 200, 300]
l2 = ['x', 'y', 'z']

s1 = pd.Series(l1, index=l2)
# 输出：Index(['x', 'y', 'z'], dtype='object')
print(s1.index)
# 输出：[100 200 300]
print(s1.values)

print("---------------------------------------")

s1 = pd.Series((1, 2, 3), index=(1, 2, 3), name='A')
s2 = pd.Series((10, 20, 30), index=(1, 2, 3), name='B')
s3 = pd.Series((100, 200, 300), index=(1, 2, 3), name='c')
df = pd.DataFrame({s1.name: s1, s2.name: s2, s3.name: s3})

"""
1、index索引是为了对齐,
如果定义的index在原字典中已经存在，那么该索引会一直对应
原字典的值，如果index对应不到原字典的值，则会返回NaN。
2、name是表头
输出：
   A   B    c
1  1  10  100
2  2  20  200
3  3  30  300
"""
print(df)
```

### 4、自动填充

1、介绍

（1）index_col

+  默认值（index_col = None）：重新设置一列成为index值。
+ False：重新设置一列成为index值。
+ 0：第一列为index值。

（2）skiprows：省略指定行数的数据。

2、代码

```python
import datetime
import pandas as pd

"""
1、月份自动填充
    d：传进来的其实时间
    md：增加的步长
"""
def add_month(d, md):
    yd = md // 12  # 传进来的月份包含多少年
    m = d.month + md % 12  # 剩余下的月份=当前月份 + 取余月份
    """
    如果传进来的月份是11月，增减月份为3，那么上式m=14，我们还需要对其进一步处理。
    """
    if m != 12:
        yd += m // 12
        m = m % 12
    return datetime.date(d.year + yd, m, d.day)


"""
1、dtype={'ID': str}：将ID这一行的数据变成str类型的
"""
books = pd.read_excel("D:\\Books.xlsx", skiprows=3, usecols="C:F", index_col=None,
                      dtype={'ID': str, 'InStore': "str", 'Date': "str"})
# print(books.head())
# 输出：RangeIndex(start=0, stop=20, step=1)
print(books.index)
start = datetime.date(2018, 1, 1)
# 填充ID,InStore列
for i in books.index:
    books['ID'].at[i] = i + 1
    books['InStore'].at[i] = 'YES' if i % 2 == 0 else 'NO'
    # 天，月，年增加
    books['Date'].at[i] = start + datetime.timedelta(days=i)
    # books['Date'].at[i] = add_month(start, i)
    # books['Date'].at[i] = datetime.date(start.year + i, start.month, start.day)

print(books)
```

### 5、函数填充，计算列

1、代码

```python
import pandas as pd

books = pd.read_excel("D:\\Books_price.xlsx", index_col='ID')
# 指定数据范围计算
# for i in range(5, 16):

# for i in books.index:
#     books["Price"].at[i] = books["ListPrice"].at[i] * books["Discount"].at[i]
#
# print(books)

"""
1、使用这种方式将ListPrice列 + 2
2、比较简单，但是难以理解。
"""
# books["ListPrice"] += 2
# print(books)


# 我们也可以使用下面的方式来进行ListPrice + 2
def add_2(x):
    return x + 2


books["ListPrice"] = books["ListPrice"].apply(add_2)
print(books)
```

### 6、排序

1、excel操作

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201020173724976.png" alt="image-20201020173724976" style="zoom: 50%;" />

2、代码

```python
import pandas as pd

products = pd.read_excel('D:/List.xlsx', index_col='ID')
# Worthy进行升序排列，Price进行降序排列
products.sort_values(by=['Worthy', 'Price'], ascending=[True, False], inplace=True)
print(products)
```

### 7、数据筛选，过滤

1、Excel操作

![image-20201020174712230](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201020174712230.png)

2、代码

```python
import pandas as pd


def validate_age(a):
    return 18 <= a <= 30


def level_b(s):
    return 60 <= s < 90


students = pd.read_excel('D:/Students.xlsx', index_col='ID')
students = students.loc[students['Age'].apply(validate_age)].loc[students.Score.apply(level_b)]
print(students)
```

### 8、绘制柱状图

1、Excel中操作

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201021211020498.png" alt="image-20201021211020498" style="zoom:67%;" />

2、代码

```python
import pandas as pd
import matplotlib.pyplot as plt

student = pd.read_excel("D:/Students.xlsx")
student.sort_values(by='Number', inplace=True, ascending=False)
# 使用pandas来画图（该库只能做一些中规中矩的图）
# student.plot.bar(x="Field", y="Number", color="orange", title="International Student by Filed")
plt.bar(student.Field, student.Number, color="orange")
# 对横坐标数据旋转90度
plt.xticks(student.Field, rotation='90')
# 调整图参数，使之填充整个图像区域（紧凑型布局）
plt.tight_layout()
plt.show()
```

3、分组柱图

在Excel表格中的操作同单组柱图一样。

（1）代码

```python
import pandas as pd
import matplotlib.pyplot as plt

students = pd.read_excel('D:/Students.xlsx')
students.sort_values(by='2017', inplace=True, ascending=False)
print(students)
# 绘制一个分组的的柱图
students.plot.bar('Field', ['2016', '2017'], color=['orange', 'Red'])
plt.title('International Students by Field', fontsize=16)
plt.xlabel('Field', fontweight='bold')
plt.ylabel('Number', fontweight='bold')
plt.tight_layout()
ax = plt.gca()
# ha='right'表示Field中（x轴）的标签按照右顶点进行旋转。默认按照字体的中间进行旋转
ax.set_xticklabels(students['Field'], rotation=40, ha='right')
# 将子图左和底部各留出20%的空余
plt.gcf().subplots_adjust(left=0.2, bottom=0.42)
plt.show()
```

结果显示：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201021212229415.png" alt="image-20201021212229415" style="zoom:67%;" />

4、叠加水平柱状图

（1）Excel操作

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201021213011057.png" alt="image-20201021213011057" style="zoom:67%;" />

（2）代码

```python
import pandas as pd
import matplotlib.pyplot as plt

users = pd.read_excel('D:/Users.xlsx')
# 为Users表设置一个属性，用来降序排列
users['Total'] = users['Oct'] + users['Nov'] + users['Dec']
users.sort_values(by='Total', inplace=True, ascending=False)
print(users)

#  正常方向上（垂直方向上）。stacked=True 用来叠加数据
# users.plot.bar(x='Name', y=['Oct', 'Nov', 'Dec'], stacked=True)
# 水平方向上图形显示
users.plot.barh(x='Name', y=['Oct', 'Nov', 'Dec'], stacked=True)
plt.tight_layout()
plt.show()
```

### 9、绘制饼状图

1、代码

```python
import pandas as pd
import matplotlib.pyplot as plt

students = pd.read_excel('D:/Students.xlsx', index_col='From')
print(students)

# counterclock（逆时针）=False
#  startangle=-270表示从-270为起始点
students['2017'].plot.pie(fontsize=6, counterclock=False)
plt.title('Source of International Students', fontsize=16, fontweight='bold')
plt.ylabel('2017', fontsize=12, fontweight='bold')
plt.show()
```

结果展示：

![image-20201022102759368](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201022102759368.png)

### 10、折线（叠加）趋势图

1、Excel操作

（1）选中数据

![image-20201022103608319](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201022103608319.png)

（2）画出折线图

![image-20201022103757119](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201022103757119.png)

2、代码

```python
import pandas as pd
import matplotlib.pyplot as plt

weeks = pd.read_excel('D:/Orders.xlsx', index_col='Week')

weeks.plot(y=['Accessories', 'Bikes', 'Clothing', 'Components'])
# weeks.plot.area(y=['Accessories', 'Bikes', 'Clothing', 'Components'])
plt.title('Sales Trends', fontsize=16, fontweight='bold')
plt.xticks(weeks.index, fontsize=8)
plt.show()
```

### 11、绘制散点图，直方图,密度图

1、Excel操作

（1）散点图

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201022104824861.png" alt="image-20201022104824861" style="zoom:80%;" />

（2）直方图

![image-20201022104910621](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201022104910621.png)

2、散点图代码

```python
import pandas as pd
import matplotlib.pyplot as plt

# 用于在控制台打印数据（将Excel的信息都显示出来）
pd.options.display.max_columns = 999
homes = pd.read_excel('D:/home_data.xlsx')
print(homes.head())
# 散点图
homes.plot.scatter(x='sqft_living', y='price')

plt.show()
```

3、直方图

```python
import pandas as pd
import matplotlib.pyplot as plt

# 用于在控制台打印数据（将Excel的信息都显示出来）
pd.options.display.max_columns = 999
homes = pd.read_excel('D:/home_data.xlsx')
print(homes.head())

# 直方图
homes.sqft_living.plot.hist(bins=100)
# 500表示x轴的步长
plt.xticks(range(0, max(homes.sqft_living), 500), fontsize=8, rotation=90)
plt.show()
```

4、密度图

```python
import pandas as pd
import matplotlib.pyplot as plt

# 用于在控制台打印数据（将Excel的信息都显示出来）
pd.options.display.max_columns = 999
homes = pd.read_excel('D:/home_data.xlsx')
print(homes.head())
# 两列之间数据的相关性
print(homes.corr())
# 密度图（kernel density）
homes.sqft_living.plot.kde()
plt.show()
```

数据显示：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201022110031960.png" alt="image-20201022110031960" style="zoom:67%;" />

### 12、多表联合

1、表格信息介绍

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201022110836341.png" alt="image-20201022110836341" style="zoom:67%;" />

2、代码

```python
import pandas as pd

students = pd.read_excel('D:/Student_score.xlsx', sheet_name='Students', index_col='ID')
scores = pd.read_excel('D:/Student_score.xlsx', sheet_name='Scores', index_col='ID')
# 将右表Scores以左表的Students的ID为基准进行连接。Scores中没有的数据补零后显示。
table = students.join(scores, how='left').fillna(0)
# 将Score中的数据变成整数
table.Score = table.Score.astype(int)
print(table)
```

### 13、数据校验

1、Excel操作

![image-20201022111823580](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201022111823580.png)

2、代码

```python
import pandas as pd


def score_valication(row):
    try:
        assert 0 <= row.Score <= 100
    except:
        print(f'#{row.ID}\tstudent {row.Name} has an invalid score {row.Score}')


students = pd.read_excel('D:/Students.xlsx')
# axis=1表示跨列操作
students.apply(score_valication, axis=1)
```

### 14、列数据分割

1、Excel操作

（1）选择需要进行分裂的数据

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201022112416395.png" alt="image-20201022112416395" style="zoom:50%;" />

（2）指定分隔符

![image-20201022112516286](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201022112516286.png)

（3）选择输出文本类型

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201022112558957.png" alt="image-20201022112558957" style="zoom:50%;" />

（4）结果显示

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201022112648796.png" alt="image-20201022112648796" style="zoom:50%;" />

2、代码

```python
import pandas as pd

employees = pd.read_excel('D:/Employees.xlsx', index_col='ID')
df = employees['Full Name'].str.split(expand=True)
employees['First Name'] = df[0]
employees['Last Name'] = df[1]
print(employees)
```

### 15、求和和统计平均操作

1、Excel表格信息

![image-20201022113354273](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201022113354273.png)

2、代码

```python
import pandas as pd

students = pd.read_excel('D:/Students.xlsx', index_col='ID')

# 跨行操作，进行一行的的求和和统计平均操作
row_sum = students[['Test_1', 'Test_2', 'Test_3']].sum(axis=1)
row_mean = students[['Test_1', 'Test_2', 'Test_3']].mean(axis=1)

students['Total'] = row_sum
students['Average'] = row_mean

col_mean = students[['Test_1', 'Test_2', 'Test_3', 'Total', 'Average']].mean()
# 在Name中追加一列Summary
col_mean['Name'] = 'Summary'
students = students.append(col_mean, ignore_index=True)
print(students)
```

### 16、删除重复数据

1、Excel操作

（1）执行操作

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201022113912307.png" alt="image-20201022113912307" style="zoom:80%;" />

（2）结果显示

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201022114001670.png" alt="image-20201022114001670" style="zoom:80%;" />

2、代码

```python
import pandas as pd

students = pd.read_excel('D:/Students_Duplicates.xlsx')
# keep='first'（last）保持前面的数据
students.drop_duplicates(subset='Name', inplace=True, keep='first')
# subset:限定函数作用范围
# dupe = students.duplicated(subset='Name')
# 表示将dataFrame中的数据扫描一遍，看看那些行是重复的。重复为True。
# dupe = dupe[dupe == True]  # dupe = dupe[dupe]
# 将重复数据的索引打印出来
# print(students.iloc[dupe.index])
print(students)
```

### 17、旋转数据表

1、Excel操作

![image-20201022115007917](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201022115007917.png)

2、代码

```python
import pandas as pd

pd.options.display.max_columns = 999
# 需要添加上index_col='Month'，不然旋转后在原来的数据表上会多出一行
videos = pd.read_excel('D:/Videos.xlsx', index_col='Month')
# 下面两行代码等价
table = videos.transpose()
# table = videos.T
print(table)
```

### 18、读取csv，txt文件

1、代码

```python
import pandas as pd

students1 = pd.read_csv('D:/Students.csv', index_col='ID')
students2 = pd.read_csv('D:/Students.tsv', sep='\t', index_col='ID')
students3 = pd.read_csv('D:/Students.txt', sep='|', index_col='ID')
```

### 19、创建透视表

1、Excel操作

（1）原表的表格信息

![image-20201022123113157](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201022123113157.png)

（2）全选数据区域，创建透视表

![image-20201022123209877](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201022123209877.png)

（3）查看

![image-20201022123312898](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201022123312898.png)

2、代码

```python
import pandas as pd
import numpy as np
from datetime import date

orders = pd.read_excel('D:/Orders.xlsx', dtype={'Date': date})
orders['Year'] = pd.DatetimeIndex(orders.Date).year
pt = orders.pivot_table(index='Category', columns='Year', values='Total', aggfunc=np.sum)
print(pt)
```

### 20、线性回归，数据预测

1、Excel操作

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201022124347566.png" alt="image-20201022124347566" style="zoom:80%;" />

2、代码

```python
import pandas as pd
import matplotlib.pyplot as plt
from scipy.stats import linregress

sales = pd.read_excel('D:/Sales.xlsx', dtype={'Month': str, 'Revenue': float})

# 使用内置函数
slope, intercept, r_value, p_value, std_err = linregress(sales.index, sales.Revenue)
exp = sales.index * slope + intercept

plt.scatter(sales.index, sales.Revenue)
plt.plot(sales.index, exp, color='red')
plt.xticks(sales.index, sales.Month, rotation=90)
plt.show()
```

### 21、条件格式

1、Excel操作

（1）将表格中小于60分的数据标红

选中匹配的数据格式：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201022125655661.png" alt="image-20201022125655661" style="zoom:67%;" />

结果展示：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201022125523581.png" alt="image-20201022125523581" style="zoom:67%;" />

字体颜色：

![image-20201022125550291](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201022125550291.png)

填充颜色：

![image-20201022125610081](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201022125610081.png)

（2）找出每列的第一名

结果显示：

![image-20201022130006975](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201022130006975.png)

操作：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201022130122213.png" alt="image-20201022130122213" style="zoom:67%;" />

（2）使用颜色深浅来表示数值的大小

```python
import pandas as pd

# == == == == == ==将不及格的数据标红== == == == == ==
def low_score_red(s):
    color = 'red' if s < 60 else 'green'
    return f'color:{color}'


students = pd.read_excel('D:/Students.xlsx')
# apply 用在dataframe上，用于对row或者column进行计算；
# applymap 用于dataframe上，是元素级别的操作
students.style.applymap(low_score_red, subset=['Test_1', 'Test_2', 'Test_3'])

# == == == == == ==找出一列中的最大值 == == == == == ==

import pandas as pd


def highest_score_green2(col):
    return ['background-color:lime' if v == col.max() else 'background-color:red' for v in col]


students = pd.read_excel('D:/Students.xlsx')
students.style.apply(highest_score_green2, subset=['Test_1', 'Test_2', 'Test_3'])

# == == == == =颜色深浅= == == == == == == ==

import pandas as pd
import seaborn as sns

color_map = sns.light_palette('green', as_cmap=True)

students = pd.read_excel('D:/Students.xlsx')
students.style.background_gradient(cmap=color_map, subset=['Test_1', 'Test_2', 'Test_3'])

# == == == == ==数据条 == == == == == == ==

import pandas as pd

students = pd.read_excel('D:/Students.xlsx')
students.style.bar(color='orange', subset=['Test_1', 'Test_2', 'Test_3'])
```

### 22、行操作

1、代码

```python
import pandas as pd

page_001 = pd.read_excel('D:/Students.xlsx', sheet_name='Page_001')
page_002 = pd.read_excel('D:/Students.xlsx', sheet_name='Page_002')

# 追加已有
students = page_001.append(page_002).reset_index(drop=True)

# 追加新建
stu = pd.Series({'ID': 41, 'Name': 'Abel', 'Score': 90})
students = students.append(stu, ignore_index=True)

# 删除（可切片）
students = students.drop(index=[39, 40])

# 插入
stu = pd.Series({'ID': 100, 'Name': 'Bailey', 'Score': 100})
part1 = students[:21]  # .iloc[] is the same
part2 = students[21:]
students = part1.append(stu, ignore_index=True).append(part2).reset_index(drop=True)

# 更改
stu = pd.Series({'ID': 101, 'Name': 'Danni', 'Score': 101})
students.iloc[39] = stu

# 设置空值
for i in range(5, 15):
    students['Name'].at[i] = ''

# 去掉空值
missing = students.loc[students['Name'] == '']
students.drop(missing.index, inplace=True)

print(students)
```

### 23、列操作

1、代码

```python
import pandas as pd
import numpy as np

students = pd.read_excel('D:/Students.xlsx')

# 追加列
students['Age'] = 25

# 删除列
students.drop(columns=['Score', 'Age'], inplace=True)

# 插入列
students.insert(1, column='Foo', value=np.repeat('foo', len(students)))

# 改列名
students.rename(columns={'Foo': 'FOO', 'Name': 'NAME'}, inplace=True)

# 设置空值
students['ID'] = students['ID'].astype(float)
for i in range(5, 15):
    students['ID'].at[i] = np.nan

# 去掉空值
students.dropna(inplace=True)

print(students)
```

### 24、复杂计算式

1、代码

```python
import pandas as pd
import numpy as np


# 结构化的方式来计算
def get_circumcircle_area(l, h):
    r = np.sqrt(l ** 2 + h ** 2) / 2
    return r ** 2 * np.pi


def wrapper(row):
    return get_circumcircle_area(row['Length'], row['Height'])


rects = pd.read_excel('D:/Rectangles.xlsx', index_col='ID')
rects['Circumcircle Area'] = rects.apply(wrapper, axis=1)
print(rects)
```