# Python变量类型

## 数字

* Python中变量赋值不需要类型声明

* Python允许同时为多个变量赋值

  ```
  a = b = c = 1
  ```

* 为多个变量指定多个值

  ```
  a, b, c = 1, 2, "john"
  ```

* 五类标准**数据类型**
  + Number（数字）
    + int
    + long
    + float
    + complex(复数)
    + bool
      * true（非零）非零即为真
      * false（零）
  + String（字符串）
  + List（列表）
  + Tuple（元组）
  + Dictionary（字典）
  
* 当指定一个值时，Python会分配一个Number对象，改变值，会重新分配一个对象；可以使用del语句删除一些对象的引用

* Python中所有非数字类型都支持一下特点（String，List，Tuple，Dictionary）

  1. 都是一个 **序列** `sequence`，也可以理解为 **容器**
  2. **取值** `[]`
  3. **遍历** `for in`
  4. **计算长度**、**最大/最小值**、**比较**、**删除**
  5. **链接** `+` 和 **重复** `*`
  6. **切片**

## 字符串

* Python字符串两种取值顺序
  + 从左到右索引默认0开始的，最大范围是字符串长度少1
  + 从右到左索引默认-1开始的，最大范围是字符串开头

<img src="C:\Users\三夏三十\AppData\Roaming\Typora\typora-user-images\image-20210701085148146.png" alt="image-20210701085148146"  />

* 当使用以冒号分隔的字符串，python 返回一个新的对象，结果包含了以这对偏移标识的连续的内容，左边的开始是包含了下边界。

  ![img](https://www.runoob.com/wp-content/uploads/2013/11/o99aU.png)

## 列表

* List（列表） 是 Python 中使用最频繁的数据类型。
* 列表可以完成大多数集合类的数据结构实现。它支持字符，数字，字符串甚至可以包含列表（即嵌套）。
* 列表用 **[ ]** 标识，是 python 最通用的复合数据类型。
* 列表中值的切割也可以用到变量 **[头下标:尾下标]** ，就可以截取相应的列表，从左到右索引默认 0 开始，从右到左索引默认 -1 开始，下标可以为空表示取到头或尾。

<img src="https://www.runoob.com/wp-content/uploads/2014/08/list_slicing1_new1.png" alt="img" style="zoom:50%;" />

## 元组

* 元组是另一个数据类型，类似于 List（列表）。

* 元组用 **()** 标识。内部元素用逗号隔开。但是元组不能二次赋值，相当于只读列表。

  ```
  tuple = ( 'runoob', 786 , 2.23, 'john', 70.2 )
  tinytuple = (123, 'john')
   
  print tuple               # 输出完整元组
  print tuple[0]            # 输出元组的第一个元素
  print tuple[1:3]          # 输出第二个至第四个（不包含）的元素 
  print tuple[2:]           # 输出从第三个开始至列表末尾的所有元素
  print tinytuple * 2       # 输出元组两次
  print tuple + tinytuple   # 打印组合的元组
  ```

  

## 字典

* 字典(dictionary)是除列表以外python之中最灵活的内置数据结构类型。列表是有序的对象集合，字典是无序的对象集合。
* 两者之间的区别在于：字典当中的元素是通过键来存取的，而不是通过偏移存取。
* 字典用"{ }"标识。字典由索引(key)和它对应的值value组成。

```
dict = {}
dict['one'] = "This is one"
dict[2] = "This is two"
 
tinydict = {'name': 'runoob','code':6734, 'dept': 'sales'}
 
 
print dict['one']          # 输出键为'one' 的值
print dict[2]              # 输出键为 2 的值
print tinydict             # 输出完整的字典
print tinydict.keys()      # 输出所有键
print tinydict.values()    # 输出所有值
```

