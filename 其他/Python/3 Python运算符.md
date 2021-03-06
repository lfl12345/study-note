# Python运算符

## Python算术运算符

* 加、减、乘、除（不是取整了）、取模都是常规操作

* 幂运算（**）

  ```
  a**b 为10的20次方， 输出结果 100000000000000000000
  ```

* 取整除（// 返回商的整数部分，向下取整）

  ```
  >>> 9//2
  4
  ```

## Python比较运算符

* 都是常规操作

## Python赋值运算符

* 都是常规操作

## Python位运算符

* 位运算符是把数字当成二进制来进行计算的

  | 运算符 | 含义                        |
  | ------ | --------------------------- |
  | &      | 按位与运算符                |
  | \|     | 按位或                      |
  | ^      | 按位异或                    |
  | ~      | 按位取反                    |
  | <<     | 左移运算，高位丢弃，低位补0 |
  | >>     | 右移运算，高位补0           |

## Python逻辑运算符

* 把&、||、！换为 and、or、not

## Python成员运算符

* String、List、Tuple、Dictionary

| 运算符 | 描述                                                    | 实例                                              |
| :----- | :------------------------------------------------------ | :------------------------------------------------ |
| in     | 如果在指定的序列中找到值返回 True，否则返回 False。     | x 在 y 序列中 , 如果 x 在 y 序列中返回 True。     |
| not in | 如果在指定的序列中没有找到值返回 True，否则返回 False。 | x 不在 y 序列中 , 如果 x 不在 y 序列中返回 True。 |

## Python身份运算符

* 身份运算符用于比较两个对象的存储单元

| 运算符 | 描述                                        | 实例                                                         |
| :----- | :------------------------------------------ | :----------------------------------------------------------- |
| is     | is 是判断两个标识符是不是引用自一个对象     | **x is y**, 类似 **id(x) == id(y)** , 如果引用的是同一个对象则返回 True，否则返回 False |
| is not | is not 是判断两个标识符是不是引用自不同对象 | **x is not y** ， 类似 **id(a) != id(b)**。如果引用的不是同一个对象则返回结果 True，否则返回 False。 |

## Python运算符优先级

* 都是常规优先级

| 运算符                   | 描述                                                   |
| :----------------------- | ------------------------------------------------------ |
| **                       | 指数 (最高优先级)                                      |
| ~ + -                    | 按位翻转, 一元加号和减号 (最后两个的方法名为 +@ 和 -@) |
| * / % //                 | 乘，除，取模和取整除                                   |
| + -                      | 加法减法                                               |
| >> <<                    | 右移，左移运算符                                       |
| &                        | 位 'AND'                                               |
| ^ \|                     | 位运算符                                               |
| <= < > >=                | 比较运算符                                             |
| <> == !=                 | 等于运算符                                             |
| = %= /= //= -= += *= **= | 赋值运算符                                             |
| is is not                | 身份运算符                                             |
| in not in                | 成员运算符                                             |
| not and or               | 逻辑运算符                                             |