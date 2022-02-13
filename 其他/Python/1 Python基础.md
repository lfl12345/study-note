# 1 Python基础



## 编译型语言和解释型语言的区别



规定：

* 有的编程语言要求必须提前将所有源代码一次性转换成二进制指令，也就是生成一个可执行程序（Windows 下的 .exe），比如C语言、[C++](http://c.biancheng.net/cplus/)、Golang、Pascal（Delphi）、汇编等，这种编程语言称为***编译型语言***，使用的转换工具称为编译器。
* 有的编程语言可以一边执行一边转换，需要哪些源代码就转换哪些源代码，不会生成可执行程序，比如 [Python](http://c.biancheng.net/python/)、[JavaScript](http://c.biancheng.net/js/)、[PHP](http://c.biancheng.net/php/)、Shell、[MATLAB](http://c.biancheng.net/matlab/) 等，这种编程语言称为***解释型语言***，使用的转换工具称为解释器。
* [Java](http://c.biancheng.net/java/) 和 [C#](http://c.biancheng.net/csharp/)是一种比较奇葩的存在，它们是**半编译半解释型**的语言，源代码需要先转换成一种中间文件（字节码文件），然后再将中间文件拿到虚拟机中执行。Java 引领了这种风潮，它的初衷是在跨平台的同时兼顾执行效率；C# 是后来的跟随者，但是 C# 一直止步于 Windows 平台，在其它平台鲜有作为。

