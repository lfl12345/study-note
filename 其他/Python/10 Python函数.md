# Python函数

- 函数代码块以 **def** 关键词开头，后接函数标识符名称和圆括号**()**。

- 函数内容以冒号起始，并且缩进。

- **return [表达式]** 结束函数，选择性地返回一个值给调用方。不带表达式的return相当于返回 None。

  ```python
  def functionname( parameters ):
     "函数_文档字符串"
     function_suite
     return [expression]
  ```

## 1、可更改和不可更改对象

#### 在 python 中，strings, tuples, 和 numbers 是不可更改的对象，而 list,dict 等则是可以修改的对象。

* **不可变类型：**变量赋值 **a=5** 后再赋值 **a=10**，这里实际是新生成一个 int 值对象 10，再让 a 指向它，而 5 被丢弃，不是改变a的值，相当于新生成了a。（a的地址变化了）
* **可变类型：**变量赋值 **la=[1,2,3,4]** 后再赋值 **la[2]=5** 则是将 list la 的第三个元素值更改，本身la没有动，只是其内部的一部分值被修改了。（la的地址没有变化）

#### Python函数的参数传递

- **不可变类型：**类似 c++ 的值传递，如 整数、字符串、元组。如fun（a），传递的只是a的值，没有影响a对象本身。比如在 fun（a）内部修改 a 的值，只是修改另一个复制的对象，不会影响 a 本身。
- **可变类型：**类似 c++ 的引用传递，如 列表，字典。如 fun（la），则是将 la 真正的传过去，修改后fun外部的la也会受影响

## 2、必备参数

## 3、关键字参数

* 关键字参数和函数调用关系紧密，函数调用使用关键字参数来确定传入的参数值。
* 使用关键字参数允许函数调用时参数的顺序与声明时不一致，因为 Python 解释器能够用参数名匹配参数值。

```py
#可写函数说明
def printinfo( name, age ):
   "打印任何传入的字符串"
   print "Name: ", name
   print "Age ", age
   return
 
#调用printinfo函数
printinfo( age=50, name="miki" )
```

## 4、默认参数

* 调用函数时，默认参数的值如果没有传入，则被认为是默认值。

```py
def printinfo( name, age = 35 ):
   "打印任何传入的字符串"
   print "Name: ", name
   print "Age ", age
   return
 
#调用printinfo函数
printinfo( age=50, name="miki" )
printinfo( name="miki" )
```

## 5、不定长参数

* 加了星号（*）的变量名会存放所有未命名的变量参数。

```py
def printinfo( arg1, *vartuple ):
   "打印任何传入的参数"
   print "输出: "
   print arg1
   for var in vartuple:
      print var
   return
 
# 调用printinfo 函数
printinfo( 10 )
printinfo( 70, 60, 50 )
```

## 6、匿名函数

* 用关键字lambda来声明



