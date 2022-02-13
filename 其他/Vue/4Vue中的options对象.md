## el

* 类型：string或HTMLElement
* 作用：决定之后Vue实例会管理哪一个DOM

## data

* 类型：Object或Function（组件中data必须是一个函数）
* 作用：Vue实例对应的数据对象

## methods

* 类型：{[key:string]:Functon}
* 作用：定义属于Vue的一些方法，可以用在其他地方调用，也可以在指令中使用
* 方法和函数的区别（js中）：方法是实例中的函数；函数是js代码中单独定义的