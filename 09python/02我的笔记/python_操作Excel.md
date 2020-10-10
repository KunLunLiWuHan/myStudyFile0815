# 1、创建Excel文件

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

# 2、读取Excel文件

1、介绍

（1）header ：指定作为列名的行，默认0，即取第一行，数据为列名行以下的数据；若数据不含列名，则设定 header = None；



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

# 3、写数据

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

# 4、自动填充

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





