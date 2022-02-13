# Python模块

* Python 模块(Module)，是一个 Python 文件，以 .py 结尾，包含了 Python 对象定义和Python语句。
* 模块能定义函数，类和变量，模块里也能包含可执行的代码。

## 1、import语句

```python
import module1[, module2[,... moduleN]]
```

* 一个模块只会被导入一次，不管你执行了多少次import。这样可以防止导入模块被一遍又一遍地执行。

## 2、from。。。import语句

* 一个模块只会被导入一次，不管你执行了多少次import。这样可以防止导入模块被一遍又一遍地执行。

```python
from modname import name1[, name2[, ... nameN]]
```

* 这个声明不会把整个 fib 模块导入到当前的命名空间中，它只会将 fib 里的 fibonacci 单个引入到执行这个声明的模块的全局符号表。

## 3、from。。。import*语句

* 把一个模块的所有内容全都导入到当前的命名空间也是可行的

```python
from modname import *
```

## 4、搜索路径

- 1、当前目录
- 2、如果不在当前目录，Python 则搜索在 shell 变量 PYTHONPATH 下的每个目录。
- 3、如果都找不到，Python会察看默认路径。UNIX下，默认路径一般为/usr/local/lib/python/。