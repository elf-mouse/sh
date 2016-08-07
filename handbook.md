# Shell速查手册

```sh
# 查看系统shell
cat /etc/shells
```

## 运行shell程序有3种方法

1. `chmod +x`使文件具有可执行权限, 直接运行
2. 直接调用解释器, 将脚本文件作为参数传入 (比如`bash hi.sh`)
3. 使用`source`(也可用 `.` 代替)执行文件

> 使用source执行shell脚本时, 不会创建子进程, 而是在父进程中直接执行!

## 变量

1. Shell中的变量只有字符串这一种类型
2. Shell中变量名与变量值没有长度限制
3. Shell的变量也允许比较操作和整数操作, 只要变量中的字符串为数字

### 定义变量

> 需要注意: `=` 两边不能加空格, 当赋值语句包含空格时请加引号(单引号/双引号均可)比如:

```sh
variable_name="Hello World"
```

Shell中的变量可以分为两种类型:

1. 局部变量 (定义变量时在前面加`local`修饰符)
2. 全局变量 (定义变量时不加任何修饰符)

```sh
#! /bin/sh
num=111 #全局变量

func1()
{
  local num=222 #局部变量
  echo $num
}

echo "before---$num"
func1
echo "after---$num"
```

输出:

```sh
before---111
222
after---111
```

### 使用变量

使用一个定义过的变量, 只要在变量名前面加`$`即可, 如:

```sh
name=Betty
echo $name
echo ${name} #{} 为了帮助解释器识别变量边界, 非必须
```

在使用变量时还有一个地方需要注意, 请看下面的例子:

```sh
#! /bin/sh
str='abc'
echo "1 print $str"
echo '2 print $str'
```

输出:

```sh
1 print abc
2 print $str
```

__被双引号括起来的变量会发生变量替换, 单引号不会__

## 注释

Shell中注释使用`#`, 而且它不支持多行注释.

## 常用的字符串操作

### 字符串拼接

```sh
name="shell"
sayHi="hello, "$name" !"
sayHi2="hello, ${name} !"
echo $sayHi $sayHi2
```

__注意:__ 上面说的单双引号引起的变量替换问题

### 获得字符串长度

```sh
string="abcd"
echo ${#string} #输出：4
```

### 截取字符串

```sh
str="hello shell"
echo ${str:2}  #输出: llo shell
echo ${string:1:3} #输出：ell
```

更多关于字符串的操作可以看[这个](http://tldp.org/LDP/abs/html/string-manipulation.html)

## if/else流程控制

基本语法结构:

```sh
if condition
then
  do something
elif condition
then
  do something
elif condition
then
  do something
else
  do something
fi
```

其中, `elif`语句和`else`语句非必须的.看个例子:

```sh
#! /bin/sh
a=1
if [ $1=$a ]
then
  echo "you input 1"
elif [ $1=2 ]
then
  echo "you input 2"
else
  #do nothing
  echo " you input $1"
fi
```

注意, 如果某个条件下的执行体为空, 则你就不能写这个条件 即下面这样会报错:

```sh
if condition
then
  #do nothing
elif condition
then
  # do nothing
#or
else
  #do nothing
fi
```

另外, `[ ]` 两边一定要加空格, 下面这样都会报错:

```sh
if [$a=$b]
#or
if [ $a=$b]
#or
if [$a=$b ]
``

__只有这样`if [ $a=$b ]`才是对的__

__注意:__ 实际上这里的`[]`是test命令的一种形式, [是系统的一个内置命令,存在路径是/bin/[,它是调用test命令的标识, 右中括号是关闭条件判断的标识, 因此下面的两个测试语句是等效的:

```sh
if test "2>3"
then
  ...
fi
```

```sh
if [ "2>3" ]
then
  ...
fi
```

除`[]`之外, shell语言中还有几种其它括号, 比如: 单小括号/双小括号/双中括号/… , 不同的括号有不同的用法, 更多关于shell中, 括号的用法可以看看[这个](http://blog.csdn.net/tttyd/article/details/11742241)

## switch流程控制

当条件较多时, 可以选择使用`switch`语句, shell中的`switch`语句的写法和其它语言还是有些不同的, 基本结构如下:

```sh
case expression in
  pattern1)
    do something... ;;
  pattern2)
    do something... ;;
  pattern2)
    do something... ;;
  ...
esac
```

看个例子:

```sh
#! /bin/sh
input=$1
case $input in
        1 | 0)
        str="一or零";;
        2)
        str="二";;
        3)
        str="三";;
        *)
        str=$input;;
esac
echo "---$str"
```

NOTE:

1. `;;`相当于其它语言中的`break`
2. 每个pattern之后记得加)
3. 最后记得加`esac` (即反的`case`)

## for循环

基本结构:

```sh
for name [in list]
do
  ...
done
```

其中,`[]`括起来的 `in list`, 为可选部分, 如果省略`in list`则默认为`in "$@"`, 即你执行此命令时传入的参数列表.

看个例子:

```sh
# 遍历当前目录下的所有txt文件, 并依次打开.
for file in *.txt
do
  open $file
done
```

## while循环

基本结构:

```sh
while condition
do
  do something...
done
```

看个例子:

```sh
#! /bin/sh
i=0
while ((i<5));
do
  ((i++))
  echo "i=$i"
done
```

输出:

```sh
i=1
i=2
i=3
i=4
i=5
```

NOTE: 你可能需要去了解一下`(())`的用法

## until循环

基本结构

```sh
until condition
do
  do something...
done
```

看个例子:

```sh
#! /bin/sh
i=5
until ((i==0))
do
  ((i--))
  echo "i=$i"
done
```

输出:

```sh
i=4
i=3
i=2
i=1
i=0
```

## 跳出循环

shell中也支持`break`跳出循环, `continue`跳出本次循环.用法与C, Java中相同

## 函数

要定义一个函数, 可以使用下面两种形式:

```sh
function funcname()
{
  do something
}
```

或者

```sh
funcname ()
{
  do something
}
```

看个例子

```sh
#! /bin/sh
# ad.sh 计算sum
add()
{
  let "sum=$1+$2"
  return $sum
}

add $1 $2
echo "sum=$?"
```

输入

```sh
ad 1 2
```

输出

```sh
sum=3
```

其中, `$?`在shell中保存的是上一条命令的返回值

NOTE:

1. 函数必须先定义后使用
2. 如果在函数中使用`exit`会退出脚本, 如果想退回到原本函数调用的地方, 则可使用`return`

## 向脚本传递参数

先shell脚本传递参数, 非常简单, 只需要在你执行命令的后面跟上即可, 看个例子:

```sh
#! /bin/sh
# test.sh
echo "$# parameters";
echo "$@";
echo "$0"
echo "$1"
```

输入:

```sh
test.sh 11 22
```

输出:

```sh
2 parameters
11 22
test.sh
11
```

__解释:__ `$@`代表所有参数的内容, `$#`代表所有参数的个数, `$0`代表脚本的名称, `$1`代表第一个参数的值.
