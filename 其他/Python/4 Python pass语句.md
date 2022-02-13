# Python pass语句

* Python pass 是空语句，是为了保持程序结构的完整性。

* **pass** 不做任何事情，一般用做占位语句

  ```
  for letter in 'Python':
     if letter == 'h':
        pass
        print '这是 pass 块'
     print '当前字母 :', letter
   
  print "Good bye!"
  ```

* Python2.x中，当函数没有函数体时，必须使用pass语句占位，要不会报错

  ```
  def function():
      # 空函数在Python2.x版本中pass是必须的
      pass
  ```

* Python3.x中，pass语句是可选的

  ```
  def function():
      # 在Python3.x的时候pass可以写或不写
      pass
  ```

  