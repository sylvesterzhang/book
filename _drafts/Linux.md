Linux

安装centos后，网络默认是关闭的。需要打开

```
cd /etc/sysconfig/network-scripts/
vi ifcfg-ens33
```

将ONBOOT修改为yes保存

重启系统



关闭防火墙

```
sudo systemctl stop firewalld 临时关闭

sudo systemctl disable firewalld ，然后reboot 永久关闭

sudo systemctl status  firewalld 查看防火墙状态。
```



基本命令

| 命令    | 含义                     |
| ------- | ------------------------ |
| cd ~    | 返回家目录               |
| cd -    | 返回上级目录             |
| pwd     | 获取当前所在的路径       |
| history | 查看历史执行的命令       |
| ! 数字  | 执行历史中的某条命令     |
| !!      | 执行上一条命令           |
| $?      | 判断上条命令是否执行成功 |
|         |                          |
|         |                          |



目录/dev/null是回收站



输出时间

```shell
echo -n;date +%F #2019-5-09
```





echo方法

1）-n表示输出后不换行

```shell
echo -n 'hello'
```

2）-e表示输出转译字符

```shell
echo -e "sdsd\nsdsdsd"
```

这里要加上引号，否则无法换行

| 转译字符 | 含义                       |
| -------- | -------------------------- |
| \a       | 发出警告声                 |
| \b       | 删除前一个字符             |
| \c       | 最后不加换行符             |
| \f       | 换行但光标仍停留再原理位置 |
| \r       | 光标移至行首               |
| \t       | 插入tab                    |
| \n       | 换行                       |



复制

```
cp -rf 目录1 目录2 
```

将目录1下所有文件递归复制到目录2下



删除

```
rm -rf 目录1 
```

将目录1下所有文件递归删除



$符号

1）文件内使用

创建文件a.sh，内容如下

```shell
#!/bin/bash
echo $0 #指当前文件
echo $1 #执行文件后，传入的第一个参数
echo $2 #执行文件后，传入的第二个参数
echo 'num: '$# #执行文件后，传入参数个数
```

执行命令`sh a.sh 1 2`后得到如下结果

```
a.sh
1
2
num: 2
```

2）命令窗口使用

$？ 获取上次执行的结果

$*  执行sh文件时，传入的所有参数组成的集合



read方法

```shell
read -t 7 -p "Enter your name in 7 seconds" NAME
echo $NAME
```

-t限定时间

-p表示提示内容

-n限制长度

-s不回显

系统函数

1）basename

去掉路径，直接截取文件名称

```shell
basename /home/a.txt #返回结果 a.txt
```

2）dirname

获取文件路径，去掉文件名称

```shell
dirname /home/a.txt #返回结果 /home
```



自定义函数

```shell
function sum()
{
	s=0
	s=$[$1+$2]
	echo $s
}
read -p "input you r parameter1:" P1
read -p "input you r parameter2:" P2

sum $P1 $P2
```



变量

1）基本语法

(1)定义变量

变量=值

(2)撤销变量

unset 变量

(3)声明静态变量（不可unset）

readonly 变量

2）变量定义规则

(1)变量名可由字母、数字、下划线组成

(2)等号两侧不能有空格

(3)变量默认类型是字符类型，无法直接进行数值运算

(4)变量值如果有空格，需使用引号括起来

3）常用系统变量

| 变量   | 说明           |
| ------ | -------------- |
| $HOME  | home目录       |
| $PWD   | 当前位置       |
| $SHELL | 当前命令解释器 |
| $USER  | 当前用户       |



if判断

```
if [ 判断条件 ]; then
	echo "做一些事情"
elif [ 判断条件 ]; then
	echo "再做一些事情"
fi
```

(1)两个整数间比较

| 比较符 | 作用       |
| ------ | ---------- |
| =      | 字符串比较 |
| -lt    | 小于       |
| -le    | 小于等于   |
| -eq    | 等于       |
| -gt    | 大于       |
| -ge    | 大于等于   |
| -ne    | 不等于     |

(2)按照文件权限进行判断

| 比较符 | 作用         |
| ------ | ------------ |
| -r     | 有读的权限   |
| -w     | 有写的权限   |
| -x     | 有执行的权限 |

(3)安装文件类型进行判断

| 比较符 | 作用                       |
| ------ | -------------------------- |
| -f     | 文件存在并且是一个常规文件 |
| -e     | 文件存在                   |
| -d     | 文件存在并是一个目录       |



case in 判断

```
case $变量 in
"值1")
	echo "做一些事"
;;
"值2")
	echo "做一些事"
;;
"值3")
	echo "做一些事"
;;
*)
	echo "做一些事"
;;
esac
```





for循环

1）累加

```shell
s=0
for ((i=0;i<100;i++))
do
	s=$[$s+$i]
done
echo $s #49050
```



2）遍历

```shell
PATH=/home/etc
for file in `ls $PATH`
do
echo $file
done
```



find



多行字符写入文本

1）使用cat

```shell
cat >>log.txt<<EOF # >>表示追加 >表示替换
are yo ok 
hello
thank you
EOF
```

2）使用echo

```shell
log=20201009
log="${log}\nare you ok"
log="${log}\nend"
echo $log>>log20201009.txt
```





运算符

1）加、减、乘、除、取余

```shell
# 运算符与数字之间必须有空格
expr 3 + 2
expr 3 - 2
expr 3 * 2
expr 3 / 2
expr 3 % 2
expr `expr 2 + 3 ` \* 4 #20

s=$[(2+3)*4]
echo $s #20
```

2）计算结果赋值给一个变量

```shell
let result=2+2
echo $result #4
```

