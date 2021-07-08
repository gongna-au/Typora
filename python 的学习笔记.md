# python 的学习笔记

## part1

### 1.**对于字符串，可执行的最简单的操作之一是修改其中的单词的大小写**

`name ="data love "`

`print (name.title())`

`Data Love`

`print (name.upper())`

`DATA LOVE`

`print (name.lower())`

`data love`

### 2. 剔除字符串的空白

`data =' python '`

`print(data.rstrip())`

' python'

`print(data.lstrip())`

'python '

`print(data.rstrip())`

'python'

### 3.列表的新元素的添加

`name =['gb','bh','gd'] `

`print (name)`

`name.append('dggus')`

`print (name)`

`name.insert(0,'gac')`

`print (name)`

insert并不是“替换”，而是右移动!

### 4.列表中元素的弹出，删除与移动

`del name [0]`

**del 的真实作用是根据索引删除列表中的元素,del 不是方法，不需要加（），del只是语句**

`name=['sd','hga','hgc','hhdka']`

`name.pop()`

"弹出"的是最后一个元素时，有点像栈，'"后来先走"，

**为什么是弹出，而不是删除，是因为pop是把元素从列表中移除，我还继续可以使用！**

`temp=name.pop()`

`print (temp)`

有返回值时，不去使用，效果与del语句差不多，去接收就可以储存在变量里面。

`pop（）不加参数时， 默认弹出最后一个参数`

`pop(0)加参数时，弹出相应索引的参数值，不去接收时，就是删除某个索引处的一个元素`

`remove()`

`name.remove ('sd')`

如果要确定移除元素的位置时，再把它打印出来就行。

`print (name)`

`name.remove('sd')`

`print (name)`

`temp=name.remove('sd')`

`print(temp)`

出现的问题是:如果列表中的元素有重复，但是remove( ) 只可以删除列表中第一次出现的元素，那么你就需要采用循环来判断是否删除了所有的这样的元素。

### 5.对列表进行排序

#### 1.永久性排序

`name = ['bmw', 'audi', 'toyota', 'subaru']
`

`name.sort(reverse=True)`

`print (name)`

sort是永久性的排序

#### 2.临时性排序

sorted（）

`name = ['bmw', 'audi', 'toyota', 'subaru']`

`print(sorted(name))`

#### 3.倒着打印

`name = ['bmw', 'audi', 'toyota', 'subaru']`

`print(reverse(name))`

`['subaru', 'toyota', 'audi', 'bmw']`

#### 4.确定列表长度

`name = ['bmw', 'audi', 'toyota', 'subaru']`

`length=len(name)`

`print(length)`

`4`

## part2

### 1.range ()的使用

`for value in range(1,5):
print(value)`

1

2

3

4

**不会打印出**5

**`list (range(1,5))`**使用list和range创建列表。

[1,2,3,4,]

**`list（range（1，11，2））`**



### **2.**切片

编写 Web 应用程序时,可使用切片来分页显示信息,并在每页显示数量合适的信息。





