[TOC]
# 课程介绍

市面上不乏python入门教程，但深入讲解python语言和应用的课程甚少，本课程定位深入理解python核心语法，结合实际应用场景，带领初学python或是有其它编程语言背景的程序员能快速掌握这门强大的语言，使其能在开发中发挥强大的生产力。

## 传智播客c++学院出品

		http://c.itcast.cn/


# 深入理解python编程

最大的优势在于它的字符串模式匹配能力，其提供一个十分强大的正则表达式匹配引擎。核心实现依赖perl，但语法比perl易懂的多。
高级语言,面向对象,可拓展,可移植,语法清晰,易维护,高效的原型。

我为什么推崇Python?

1. 干某一件事情，C需要100行，JAVA需要50行，Python只需要10行，当你忙于编写代码或是设计框架时，Python程序员已经早早的下班开启了把妹之旅。

2. 面向对象开发，方便团队协作，语言间的万能胶水，当需要高性能的处理时可以自然粘合c/c++模块。

3. 信奉python的哲学

在python解释器中输入，"import this"

		漂亮比丑陋要好。
		直接比含蓄要好。
		简单比繁复要好。
		繁复比复杂要好。
		平铺比嵌套要好。
		稀疏比密集要好。
		可读性很重要。
		特例不能破坏规则。
		尽管实用优于纯正。
		错误永远不能安静的通过。
		除非明确的让它安静。
		拒绝在模糊的地方猜测。
		应当有一种，并且最好只有一种，明显的方法去做一件事。
		尽管开始时那种方法并不明显，除非你是荷兰人。
		现在要比永远不更好。
		尽管永远不常常比当前要好。
		如果一个实现很难解释，那么它就是一个不好的想法。
		如果一个实现容易解释，那么它可能是一个好的想法。
		名称空间是一个很伟大的想法，让我们做的更多。


python家族:

+ C语言实现，CPython，扩展可用C/C++
+ Java实现，Jython，扩展可用Java
+ .Net实现，IronPython，扩展可用C#


python能干什么？

+ 科学计算
+ 图形化开发
+ 系统脚本
+ web服务器
+ 网络爬虫
+ 服务器集群自动化运维

## 一. 高效的开发环境与基础

### python开发环境

		Mac/Linux发行版目前默认安装python
		ipython
		python官方IDE，在python发行版自带
		Eclipse+pydev
		PyScripter
		subline text3

#### Windows下Sublime Text3和python语言环境

sublimetext官方下载地址，请根据自己的操作系统平台选择对应版本

		http://www.sublimetext.com/3

python下载地址

		https://www.python.org/downloads/	

#### Linux下Sublime Text3和python语言环境

Linux系统选择广受大家欢迎的Ubuntu14.04，如果没有此环境可以选择使用传智C++学院配置好的虚拟机镜像

		下载链接：http://pan.baidu.com/s/1c0yTN4c        密码：b77w
		用户名：itcast
		密码：itcast

sublimetext官方下载地址，请根据自己的操作系统平台选择对应版本

		http://www.sublimetext.com/3

ubuntu14.04默认是安装了python2.7的

#### Ubuntu 下 ipython

1. 安装

		sudo apt-get install ipython

2. 启动

		itcast@itcast:~/workspace/chuanzhi/openlessons$ ipython

3. 体验

		print  "hello itcastcpp"          #此为python2的写法

4. 退出

		exit

5. 案例

		import requests
		res=requests.get("http://c.itcast.cn")
		savefile = open("itcast.html", "w")
		savefile.write(res.content)
		savefile.close()

6. 技巧

%history ：记录敲过的命令，方便从命令转为脚本文件

tab：补齐命令或路径

### Sublime使用技巧

1.安装package管理工具

`ctrl+\``调出命令输入窗口

```python
import urllib.request,os; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); open(os.path.join(ipp, pf), 'wb').write(urllib.request.urlopen( 'http://sublime.wbond.net/' + pf.replace(' ','%20')).read())
```

设置vim模式，Sublime Text 内置 Vim 模式支持，你只需到用户设置文件将 "ignored_packages": ["vintage"] 中的 vintage 删除即可。

2.调出installpackage界面

		ctrl + shift + p
		install package

3.常用工具包

		AdvancedNewFile
		Djaneiro
		Emmet
		Git
		Side Bar
		HTML/CSS/JS Prettify
		Python PEP8 Autoformat
		SublimeCodeIntel
		ColorPicker
		OmniMarkupPreviewer

4.常用包使用说明

AdvancedNewFile

可以创建文件，也可以连目录和文件都创建
    win+alt+n

Djaneiro

django一些语法快速补齐功能，参考如下

		https://packagecontrol.io/packages/Djaneiro

Emmet

快速缩写html,tab补齐

		    html:5    补齐html
		    p.foo     补齐class
		    p#foo     补齐id
		    >         子元素符号，表示嵌套的元素
		    +         同级标签符号
		    ^         可以使该符号前的标签提升一行

		更多参考：http://www.iteye.com/news/27580

Git

集成git

		    ctrl+shift+p
		    输入git 

Side Bar

折叠目录树

		    ctrl+k   ctrl+b

HTML/CSS/JS Prettify

格式化代码,鼠标右键，从里面选

Python PEP8 Autoformat

格式化python代码

		    ctrl+shift+r

SublimeCodeIntel

自动匹配补全代码

		    ctrl+f3    调到变量定义的地方

ColorPicker

屏幕拾色器

		    ctrl+shift+c

OmniMarkupPreviewer

更多插件，设置OmniMarkupPreviewer的package setting中的default。修改里面的extensions

		    "extensions": ["extra", "codehilite", "toc", "strikeout", "smarty", "subscript", "superscript"]

安装语法高亮支持插件

		    sudo pip install pygments

ConvertToUTF8

直接在菜单栏中可以转，专为中文设计

Terminal

Sublime版的在当前文件夹内打开

		    ctrl+shift+t

Side​Bar​Enhancements

		右键一下子多处很多选择

自带技巧

+ 修改同一个变量,光标放在变量后，两次    `ctrl+d` 
+ 多变量修改，按住ctrl，鼠标点击修改位置
+ 查找 `ctrl+f`
+ 插入注释 `ctrl+shift+/`
+ 注释当前行  `ctrl+/`
+ 分屏
		    Alt+Shift+1（非小键盘）窗口分屏，恢复默认1屏
		    Alt+Shift+2 左右分屏-2列
		    Alt+Shift+3 左右分屏-3列
		    Alt+Shift+4 左右分屏-4列
		    Alt+Shift+5 等分4屏
		    Alt+Shift+8 垂直分屏-2屏
		    Alt+Shift+9 垂直分屏-3屏

+ 标签切换 `alt+数字`
+ `Ctrl+Shift+P` 打开命令面板
+ 关闭当前标签文件`ctrl+f4`
+ f11全屏

脚本一键安装

```bash
cd ~/home/xwp/.config/sublime-text-3/Packages

echo Install...
echo ==================================================

echo === Package Control ===
rm -rf "Package Control"
git clone https://github.com/JustQyx/Sublime-Text-Package-Control.git "Package Control"

echo === Block Cursor Everwhere ===
rm -rf "Block Cursor Everwhere"
git clone https://github.com/ingshtrom/BlockCursorEverywhere.git "Block Cursor Everwhere"
...
```

### 基础数据类型

+ 整型：通常被称为是整型或整数，是正或负整数，不带小数点。
+ 长整型：无限大小的整数，整数最后是一个大写(或小写)的L。
+ 浮点型：浮点型由整数部分与小数部分组成，浮点型也可以使用科学计数法表示（2.5e2 = 2.5 x 102 = 250）
+ 复数：复数的虚部以字母J 或 j结尾 。如：2+3j
+ 布尔类型：True , False
+ 字符串：单引号，双引号，三个单引号扩起来

获取变量的数据类型  type(var_name)

		主提示符		>>>  	在等待下一条语句
		次提示符		...	在等待当前语句的其他部分

### 变量

#### 变量本质

1. python中的变量不需要先定义，再使用，可以直接使用,还有重新使用用以存储不同类型的值。
2. 变量命名遵循C命名风格。
3. 大小写敏感。
4. 变量引用计数。
5. del语句可以直接释放资源，变量名删除，引用计数减1。
6. 变量内存自动管理回收,垃圾收集。
7. 指定编码在文件开头加入 # -*- coding: UTF-8 -*- 或者 #coding=utf-8。

		    >>> a = 12               #无需定义，直接使用，python解释器根据右值决定左侧类型
		    >>> print a
		    12
		    >>> id(a)                #变量a在内存中的编号
		    136776784
		    >>> type(a)              #a的类型为int类型
		    <class 'int'>
		    >>> b = 12.34
		    >>> print b
		    12.34
		    >>> id(b)                #变量b在内存中所占内存编号
		    3071447616
		    >>> type(b)
		    <class 'float'>          #b的类型为float
		    >>> a = "itcast"         #变量a从新指向一个字符串
		    >>> print a
		    itcast
		    >>> id(a)                #变量a在内存中的编号为保存“itcast”地方，原来a所指向的内存编号里内容并没有立即释放
		    3071127936
		    >>> type(a)              #变量a现在指向一个字符串
		    <class 'str'>
		    >>> c = b              
		    >>> print c
		    12.34
		    >>> id(c)                #变量c保存的内存中的编号和b一致  
		    3071447616
		    >>> type(c)
		    <class 'float'>

		    >>> b = 12               #解释器在内存中发现有保存12的这个单元，于是变量b指向了此单元，减少了存储空间的反复申请与释放
		    >>> id(b)
		    136776784
		    >>> type(b)
		    <class 'int'>
		    >>> print b
		    12

		    >>> print a
		    itcast
		    >>> del(a)
		    >>> print a
		    Traceback (most recent call last):
		      File "<stdin>", line 1, in <module>
		    NameError: name 'a' is not defined

### 简单函数
	
函数定义格式

```python
def add(x, y):
    z = x + y
    return z

res = add(3, 5)
print res
8
```

1. def定义函数的关键字
2. x和y为形参，不需要类型修饰
3. 函数定义行需跟':'
4. 函数体整体缩进
5. 函数可以拥有返回值，若无返回值，返回None，相当于C中的NULL

### 局部变量和全局变量

代码1. 局部变量作用域覆盖全局变量

```python
def p_num():
    num=5
    print num

num=10
p_num()
print num
#结果:  5 10
```

代码2. 函数内有局部变量定义，解释器不使用全局变量，局部变量的定义晚于被引用，报错

```python
def p_num():
    print num
    num=5
    print num

num=10
p_num()
print num
# 结果出错
```

代码3. 函数内部可以直接访问全局变量

```python
def p_num():
    print num

num=10
p_num()
print num
# 结果: 10  10
```

代码4. 函数内修改全局变量,使用global关键字

```python
def p_num():
    global num
    print num
    num = 20
    print num

num=10
p_num()
print num
```


### 特殊变量

	    _xxx    from module import *无法导入
	    __xxx__   系统定义的变量
	    __xxx   类的本地变量
	    


### 表达式
	
#### 算术表达式

	
		    +a       结果符号不变                     
		    -a       对结果符号取负                    
		    a +  b   a加b
		    a -  b   a减b
		    a ** b   a的b次幂    
		    a *  b   a乘以b                          
		    a /  b   a除以b，真正除，浮点数保留小数 
		    a // b   a除以b，向下取整            
		    a %  b   a对b取余数 
		    
    
#### 逻辑表达式

		    not a        a的逻辑非           bool
		    a and b      a和b逻辑与          bool
		    a or b       a和b逻辑或          bool
		    a is b       a和b是同一个对象    bool
		    a is not b   a和b不是同一个对象  bool

#### 关系表达式

运算结果为布尔类型

		==	等于
		!=	不等于 
		<>	不等于(废弃)
		>	大于 
		<	小于
		>=	大于等于
		<=	小于等于

### 位运算

	    ~a     按位取反
	    a << n     a左移n位
	    a >> n     a右移n位
	    a &  b     a和b按位与
	    a |  b     a和b按位或
	    a ^  b     a和b按位异或




### 语法格式

缩进表示关系，函数，分支，循环语句后面带':'

### 分支语句


```python
#if-else

    if a > b:
        print("aaa")
    else:
        print("bbb")

#if-elif-else

    if a > b:
        print("a>b")
    elif a == b:
        print("a==b")
    else:
        print("a<b")
```

### 循环语句

python里的控制语句和循环语句和C中的非常相似，毕竟python是用C实现的。
注意语句后面的':'不要丢掉，这一点C/C++程序员刚上手python的时候是最容易犯的错误。

		    while 判断条件：
		        执行语句

```python
var = 1
while var == 1 :  # 该条件永远为true，循环将无限执行下去
	num = raw_input("Enter a number  :")
	print "You entered: %d", num

	print "Good bye!"
```

Python for循环可以遍历任何序列的项目，如一个列表或者一个字符串。

		    for iterating_var in sequence:
		        执行语句

```python
for letter in 'Python':     # First Example
	print 'Current Letter :', letter

fruits = ['banana', 'apple',  'mango']
for fruit in fruits:        # Second Example
	print'Current fruit :', fruit
```

在 python 中，for … else 表示这样的意思，for 中的语句和普通的没有区别，else 中的语句会在循环正常执行完（即 for 不是通过 break 跳出而中断的）的情况下执行，while … else 也是一样。

```python
    count = 0
    while count < 5:
       print(count, " is  less than 5")
       count = count + 1
    else:
       print(count, " is not less than 5")
```

以上实例输出结果为：

		    0 is less than 5
		    1 is less than 5
		    2 is less than 5
		    3 is less than 5
		    4 is less than 5
		    5 is not less than 5


#### break

Python break语句，就像在C语言中，打破了最小封闭for或while循环。

break语句用来终止循环语句，即循环条件没有False条件或者序列还没被完全递归完，也会停止执行循环语句。

break语句用在while和for循环中。

如果您使用嵌套循环，break语句将停止执行最深层的循环，并开始执行下一行代码。

#### continue

Python continue 语句跳出本次循环，而break跳出整个循环。

continue 语句用来告诉Python跳过当前循环的剩余语句，然后继续进行下一轮循环。

continue语句用在while和for循环中。

### list列表

序列都可以进行的操作包括索引，切片，加，乘，检查成员。
序列中的每个元素都分配一个数字 - 它的位置，或索引，第一个索引是0，第二个索引是1，依此类推。
	
列表和元组二者均能保存任意类型的python对象,索引访问元素从开始
列表元素用[]包括,元素个数,值都可以改变
元组元素用()包括  通过切片 [] [:] 得到子集,此操作同于字符串相关操作
	切片使用的基本样式[下限:上限:步长]

#### 访问列表中的值
		
		>>>aList = [1,2,3,4]
		>>>aList
		[1,2,3,4]
		>>>aList[0]
		1
		>>>aList[2:]
		[3,4]
		>>>aList[:3]
		[1,2,3]

#### 更新列表中的值

		>>>aList[1] = 5
		>>>aList
		[1,5,3,4]

#### 删除列表中的值

		>>> del aList[1]
		>>>aList
		[1,3,4]

#### Python列表脚本操作符

列表对 + 和 * 的操作符与字符串相似。+ 号用于组合列表，* 号用于重复列表。
	
		Python表达式				结果					描述
		len([1, 2, 3])			3					长度
		[1, 2, 3] + [4, 5, 6]	[1, 2, 3, 4, 5, 6]	组合
		['Hi!'] * 4				['Hi!', 'Hi!', 'Hi!', 'Hi!']	重复
		3 in [1, 2, 3]			True				元素是否存在于列表中
		for x in [1, 2, 3]: print x,	1 2 3		迭代

#### Python列表截取

Python的列表截取与字符串操作类型，如下所示：
	
		L = ['spam', 'Spam', 'SPAM!']
		操作：
		
		Python 表达式	结果	描述
		L[2]	'SPAM!'	读取列表中第三个元素
		L[-2]	'Spam'	读取列表中倒数第二个元素
		L[1:]	['Spam', 'SPAM!']	从第二个元素开始截取列表

#### Python列表函数&方法

		Python包含以下函数:
		
		序号	函数
		1	cmp(list1, list2)	比较两个列表的元素
		2	len(list)			列表元素个数
		3	max(list)			返回列表元素最大值
		4	min(list)			返回列表元素最小值
		5	list(seq)			将元组转换为列表
		Python包含以下方法:
		
		序号	方法
		1	list.append(obj)	在列表末尾添加新的对象
		2	list.count(obj)		统计某个元素在列表中出现的次数
		3	list.extend(seq)	在列表末尾一次性追加另一个序列中的多个值（用新列表扩展原来的列表）
		4	list.index(obj)		从列表中找出某个值第一个匹配项的索引位置
		5	list.insert(index, obj)		将对象插入列表
		6	list.pop(obj=list[-1])		移除列表中的一个元素（默认最后一个元素），并且返回该元素的值
		7	list.remove(obj)			移除列表中某个值的第一个匹配项
		8	list.reverse()				反向列表中元素
		9	list.sort([func])			对原列表进行排序

### 元组Tuple

Python的元组与列表类似，不同之处在于元组的元素不能修改。也可进行分片 和 连接操作.
元组使用小括号，列表使用方括号。

		>>>aTuple = ('et',77,99.9)
		>>>aTuple
		('et',77,99.9)

#### 访问元组

		>>>aTuple[2]
		99

#### 修改元组

		>>>aTuple[1] = 5  #真的不能修改呀
		报错啦
		>>>tup2 = (1, 2, 3, 4, 5, 6, 7 )
		>>>print "tup2[1:5]: ", tup2[1:5]
		>>>tup2[1:5]:  (2, 3, 4, 5)
		
		>>>tup3 = tup2 + aTuple;
		>>>print tup3
		(1, 2, 3, 4, 5, 6, 7,'et',77,99.9)

#### 删除元素

		元组中的元素值是不允许删除的，但我们可以使用del语句来删除整个元组

####	元组运算符

		与字符串一样，元组之间可以使用 + 号和 * 号进行运算。这就意味着他们可以组合和复制，运算后会生成一个新的元组。
		
		Python 表达式	结果	描述
		len((1, 2, 3))	3	计算元素个数
		(1, 2, 3) + (4, 5, 6)	(1, 2, 3, 4, 5, 6)	连接
		['Hi!'] * 4	['Hi!', 'Hi!', 'Hi!', 'Hi!']	复制
		3 in (1, 2, 3)	True	元素是否存在
		for x in (1, 2, 3): print x,	1 2 3	迭代 
####	元组索引，截取
		因为元组也是一个序列，所以我们可以访问元组中的指定位置的元素，也可以截取索引中的一段元素.

		L = ('spam', 'Spam', 'SPAM!')
		Python 表达式	结果	描述
		L[2]	'SPAM!'	读取第三个元素
		L[-2]	'Spam'	反向读取；读取倒数第二个元素
		L[1:]	('Spam', 'SPAM!')	截取元素
#### 无关闭分隔符
		任意无符号的对象，以逗号隔开，默认为元组，如下实例：
		
		print 'abc', -4.24e93, 18+6.6j, 'xyz';
		x, y = 1, 2;
		print "Value of x , y : ", x,y;
		以上实例允许结果：
		
		abc -4.24e+93 (18+6.6j) xyz
		Value of x , y : 1 2
####	元组内置函数
	Python元组包含了以下内置函数
		序号	方法及描述
		1	cmp(tuple1, tuple2)		比较两个元组元素。
		2	len(tuple)				计算元组元素个数。
		3	max(tuple)				返回元组中元素最大值。
		4	min(tuple)				返回元组中元素最小值。
		5	tuple(seq)				将列表转换为元组。
#### 多维元祖访问的示例
	>>> tuple1 = [(2,3),(4,5)]
	>>> tuple1[0]
	(2, 3)
	>>> tuple1[0][0]
	2
	>>> tuple1[0][2]
	Traceback (most recent call last):
	  File "<stdin>", line 1, in <module>
	IndexError: tuple index out of range
	>>> tuple1[0][1]
	3
	>>> tuple1[2][2]
	Traceback (most recent call last):
	  File "<stdin>", line 1, in <module>
	IndexError: list index out of range
	>>> tuple2 = tuple1+[(3)]
	>>> tuple2
	[(2, 3), (4, 5), 3]
	>>> tuple2[2]
	3
	>>> tuple2[2][0]
	Traceback (most recent call last):
	  File "<stdin>", line 1, in <module>
	TypeError: 'int' object is not subscriptable

### 字典Dictionary

字典是python中的映射数据类型,,由键值(key-value)构成,类似于关联数组或哈希表.
key一般以数字和字符串居多,值则可以是任意类型的python对象，如其他容器模型。
字典元素用大括号	{} 包括.比如:

	>>>dict = {'Alice': '2341', 'Beth': '9102', 'Cecil': '3258'}
	>>>aDict = {'host':'noname'}	

每个键与值用冒号隔开（:），每对用逗号分割，整体放在花括号中（{}）。
键key必须独一无二，但值则不必。值value可以取任何数据类型，但必须是不可变的

### 访问字典里的值

	>>>aDict['host']
	'noname'
	>>> for key in aDict
	... print key,aDict[key]
	...
	host noname
	port 80
	
	>>>aDict.keys()
	['host','port']
	>>>aDict.values()
	
### 修改字典

向字典添加新内容的方法是增加新的键/值对，修改或删除已有键/值对.

	>>>aDict['port'] = 80    #如果有port key,值将会被更新 否则会被新建一个port key
	>>>aDict
		{'host':'noname','port':80}

### 删除字典元素

能删单一的元素也能清空字典，清空只需一项操作.

显示删除一个字典用del命令

	dict = {'Name': 'Zara', 'Age': 7, 'Class': 'First'};
	del dict['Name']; # 删除键是'Name'的条目
	dict.clear();     # 清空词典所有条目
	del dict ;        # 删除词典

### 字典键(key)的特性

字典值可以没有限制地取任何python对象，既可以是标准的对象，也可以是用户定义的，但键不行。

两个重要的点需要记住：

1)不允许同一个键出现两次。创建时如果同一个键被赋值两次，后一个值会被记住

        >>>dict = {'Name': 'Zara', 'Age': 7, 'Name': 'Manni'};
        >>>print "dict['Name']: ", dict['Name'];
        >>>dict['Name']:  Manni
	
2)键必须不可变，所以可以用数，字符串或元组充当，所以用列表就不行

        >>>dict = {['Name']: 'Zara', 'Age': 7};
        >>>print "dict['Name']: ", dict['Name'];
            raceback (most recent call last):
            File "test.py", line 3, in <module>
        >>>dict = {['Name']: 'Zara', 'Age': 7};
            TypeError: list objects are unhashable

### 字典内置函数&方法

Python字典包含了以下内置函数：
	
	序号	函数及描述
	1	cmp(dict1, dict2)		比较两个字典元素。
	2	len(dict)				计算字典元素个数，即键的总数。
	3	str(dict)				输出字典可打印的字符串表示。
	4	type(variable)			返回输入的变量类型，如果变量是字典就返回字典类型。
	Python字典包含了以下内置函数：
	
	序号	函数及描述	
	1	radiansdict.clear()		删除字典内所有元素
	2	radiansdict.copy()		返回一个字典的浅复制
	3	radiansdict.fromkeys()	创建一个新字典，以序列seq中元素做字典的键，val为字典所有键对应的初始值
	4	radiansdict.get(key, default=None)		返回指定键的值，如果值不在字典中返回default值
	5	radiansdict.has_key(key)	如果键在字典dict里返回true，否则返回false
	6	radiansdict.items()			以列表返回可遍历的(键, 值) 元组数组
	7	radiansdict.keys()			以列表返回一个字典所有的键
	8	radiansdict.setdefault(key, default=None)	
								和get()类似, 但如果键不已经存在于字典中，将会添加键并将值设为default
	9	radiansdict.update(dict2)	把字典dict2的键/值对更新到dict里
	10	radiansdict.values()		以列表返回字典中的所有值
	print dict['one'] # 输出键为'one' 的值
	print dict[2] # 输出键为 2 的值
	print tinydict # 输出完整的字典
	print tinydict.keys() # 输出所有键
	print tinydict.values() # 输出所有值
### tuple元祖
### dict字典
### 简单函数

#### Python数字类型转换

		int(x [,base ])         	将x转换为一个整数  
		long(x [,base ])       	将x转换为一个长整数  
		float(x )               	将x转换到一个浮点数  
		complex(real [,imag ])  	创建一个复数  
		str(x )                 	将对象 x 转换为字符串  
		repr(x )                	将对象 x 转换为表达式字符串  
		eval(str )              	用来计算在字符串中的有效Python表达式,并返回一个对象  
		tuple(s )               	将序列 s 转换为一个元组  
		list(s )                		将序列 s 转换为一个列表  
		chr(x )                 	将一个整数转换为一个字符  
		unichr(x )              	将一个整数转换为Unicode字符  
		ord(x )                 	将一个字符转换为它的整数值  
		hex(x )                 	将一个整数转换为一个十六进制字符串  
		oct(x )                 	将一个整数转换为一个八进制字符串  

#### Python数学函数

		函数		返回值 ( 描述 )
		abs(x)		返回数字的绝对值，如abs(-10) 返回 10
		ceil(x)		返回数字的上入整数，如math.ceil(4.1) 返回 5
		cmp(x, y)	如果 x < y 返回 -1, 如果 x == y 返回 0, 如果 x > y 返回 1
		exp(x)		返回e的x次幂(ex),如math.exp(1) 返回2.718281828459045
		fabs(x)		返回数字的绝对值，如math.fabs(-10) 返回10.0
		floor(x)		返回数字的下舍整数，如math.floor(4.9)返回 4
		log(x)		如math.log(math.e)返回1.0,math.log(100,10)返回2.0
		log10(x)		返回以10为基数的x的对数，如math.log10(100)返回 2.0
		max(x1, x2,...)	返回给定参数的最大值，参数可以为序列。
		min(x1, x2,...)	返回给定参数的最小值，参数可以为序列。
		modf(x)		返回x的整数部分与小数部分，两部分的数值符号与x相同，整数部分以浮点型表示。
		pow(x, y)	x**y 运算后的值。
		round(x [,n])	返回浮点数x的四舍五入值，如给出n值，则代表舍入到小数点后的位数。
		sqrt(x)		返回数字x的平方根，数字可以为负数，返回类型为实数，如math.sqrt(4)返回 2+0j

### Python随机数函数

随机数可以用于数学，游戏，安全等领域中，还经常被嵌入到算法中，用以提高算法效率，并提高程序的安全性。

		函数	描述
		choice(seq)	 从序列的元素中随机挑选一个元素，比如random.choice(range(10))，从0到9中随机挑选一个整数。
		randrange ([start,] stop [,step])	从指定范围内，按指定基数递增的集合中获取一个随机数，基数缺省值为1
		random()			随机生成下一个实数，它在[0,1)范围内。
		seed([x])			改变随机数生成器的种子seed。如果你不了解其原理，你不必特别去设定seed，Python会帮你选择seed。
		shuffle(lst)			将序列的所有元素随机排序
		uniform(x, y)			随机生成下一个实数，它在[x,y]范围内。

## 二. 字符串处理与特殊函数

### 字符串各种函数
### 函数传参
### 闭包
### 装饰器

## 三. 面向对象

### 封装
### 继承
### 类变量命名规则
### 包的制作与发布

## 四. 文件操作

### 文件创建与读写
### Linux和Windows平台下的差异性

## 五. 应用案例剖析

### 15个经典Python案例，展现python优美神奇的一面

## 六. 模块化借力C/C++

### 借力C/C++，提高程序性能，实现代码复用
### Python操作Redis

## 七. Linux系统网络编程实战

### requests网络库使用
### socket原生网络库使用

## 八. web框架Django开发

### 移动互联网+项目开发
### Django开发模型
### 微信公众号app开发
