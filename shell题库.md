## shell题库

### 输出文件行数

```shell
cat huang.txt|wc -l
#注意 wc -l huang.txt 输出会携带文件名
```

### 输出数字 0 到 500 中 7 的倍数

```shell
#!/bin/bash
for((i=0;i<=500;i++));do
if [ $(($i % 7)) == 0 ]
   then
        echo $i
fi
done
```

### 输出一个文本文件中空行的行号

```shell
#!/bin/bash
i=1
while read line;
do
    if [ -z $line ];
    then
        echo $i
    fi
    i=$[i+1] ##((i++))
done < nowcoder.txt

awk '/^$/{print NR}' nowcoder.txt

awk '{if($0 == "") {print NR}}' ./nowcoder.txt

sed -n  '/^$/='  "nowcoder.txt"
-n 对匹配的行做处理
=打印匹配到的内容的行号
p打印匹配到的内容
```

### 去掉一个文本文件中的空行

```shell
sed -n '/[^$]/p'
sed -n 静默模式
p：打印变动的流（行）
正则部分： [^$] ^代表以后面跟着的字符为开头，$代表以前面的字符为结尾；
^$联合使用，中间不加任何字符数字，代表匹配空行；
[ ] 在shell正则中表示取反

	
sed '/^\s*$/ d'
```

### 统计文本文件中字母数小于8的单词

```shell
#!/bin/bash
for str in `cat nowcoder.txt`
do
   if [ `echo $str|wc -L` -lt 8 ]
    then
        echo "$str"
   fi
done


awk '{for(i=1;i<=NF;i++)if(length($i)<8)print $i;}' nowcoder.txt
```

### 文件滤出字符

```shell
#'Poe'字符串与33794712
I am Poe,my qq is 33794712

awk -F '[ ,]+' '{print $3" "$7}' test.txt

[ :]+这个是正则表达式，+表示一个或多个，这里就表示一个或多个空格或冒号
```

### 统计/etc/passwd的账户人数

```shell
awk 'BEGIN {count=0;print "[start] user count is ",count} {count=count+1;print $0} END{print "[end] user count is ",count}' /etc/passwd
```

### 统计某个文件夹下的文件占用的字节数

```shell
ll |awk 'BEGIN{size=0;} {size=size+$5;} END{print "[end]size is ",size/1024/1024,"M"}'
```

### 查看服务器连接状态并汇总 

```shell
netstat -an|awk '/^tcp/{++s[$NF]}END{for(a in s)print a,s[a]}'
```

### 统计 web 日志访问流量

```shell
#要求输出访问次数，请求页面或图片，每个请求的总大小，总访问流量的大小汇总
awk '{a[$7]+=$10;++b[$7];total+=$10}END{for(x in a)print b[x],x,a[x]|"sort -rn -k1";print
"total size is :"total}' /app/log/access_log

a[$7]+=$10 表示以第 7 列为下标的数组（ $10 列为$7 列的大小），把他们大小累加得到
$7 每次访问的大小，后面的 for 循环有个取巧的地方， a 和 b 数组的下标相同，所以一
条 for 语句足矣
```

### 计算所有进程占用内存大小的和

```shell
root         2  0.0  0.0      0     0 ?        S    9月25   0:00 [kthreadd]
root         4  0.0  0.0      0     0 ?        I<   9月25   0:00 [kworker/0:0H]
web       1638  1.8  1.8 6311352 612400 ?      Sl   10月16  21:52 test

#!/bin/bash
awk 'BEGIN{total=0}{total+=$6}END{print total}' 
```

### 统计一个文本文件中每个单词出现的个数（以词频升序排列）

```shell
welcome nowcoder
welcome to nowcoder
nowcoder

输出格式：
to 1 
welcome 2 
nowcoder 3 

#!/bin/bash
awk '{
for(i=1;i<=NF;i++){
	count[$i]++;
}
}END{
for(item in count){
	print item" "count[item]
}
}' nowcoder.txt

cat nowcoder.txt  | tr -s ' ' '\n' |sort |uniq -c|awk '{print $2" "$1}'
tr -s 正则将空格替换为换行符
uniq -c 统计连续文本次数
```

### 检查文件第二列是否有重复，且有几个重复，并提取出重复的行的第二列信息

```shell
20201001 python 99
20201002 go 80
20201002 c++ 88
20201003 php 77
20201001 go 88
20201005 shell 89
20201006 java 70
20201008 c 100
20201007 java 88
20201006 go 97
输出结果:
2 java
3 go

awk '{print $2}' nowcoder.txt|sort -r|uniq -cd

line=`cat nowcoder.txt |wc -l`
cat nowcoder.txt |tr -s '\n' ' '|sort -n|awk 'BEGIN{line=""}{
    for(i=1;i<=NF;i++){
        if(i==4){
        	line+="\n"
        }
        line+=$i
    }
}'
```

### 转置文本文件中的文件内容

```shell
job salary
c++ 13
java 14
php 12

你的脚本应当输出（以词频升序排列）：
job c++ java php
salary 13 14 12

a=`head -n1 "nowcoder.txt" |awk -F ' ' '{print NF}'`
i=1
while [  $i -le  $a ];
do
    line=`cat "nowcoder.txt"|awk '{print $'$i'}'` #cat "nowcoder.txt"|awk '{print $'$i'}'|xargs echo
    echo $line
    let i+=1
done

awk '{printf  $1" "}' nowcoder.txt
awk '{printf $2" "}' nowcoder.txt

#精妙
transpose=`head -n1 file.txt | wc -w`
for i in `seq 1 $transpose`
do
    echo `cut -d' ' -f$i file.txt`
done
```

### 统计一个文本文件中每一行出现的1,2,3,4,5数字个数并且要计算一下整个文档中一共出现了几个1,2,3,4,5数字数字总数

```shell
a12b8
10ccc
2521abc
9asf
你的脚本应当输出：
line1 number: 2
line2 number: 1
line3 number: 4
line4 number: 0
sum is 7

awk '{
    gsub(/[^1-5]/,"",$0);
    print "line"NR" number: "length($0);
    sum+=length($0);
} END{ print "sum is "sum }'

awk -F '' 'BEGIN{
	total=0;
}{
	origin=length($0);
    gsub(/[1-5]/,"",$0);
    after=length($0);
	count=origin-after;
	total+=count;
    print "line"NR,"number:",count
}END{
	print "sum is",total
}' nowcoder.txt
```

### 求输入的一个的数组的平均值

```shell
第1行为输入的数组长度N
第2~N行为数组的元素，如以下为:
数组长度为4，数组元素为1 2 9 8
示例:
4
1
2
9
8
那么平均值为:5.000(保留小数点后面3位)
你的脚本获取以上输入应当输出：
5.000

awk 'NR==1{all=$0} NR>1{total+=$0}END{printf "%.3f",total/all}' nowcoder.txt
```

### 查找指定目录下文本文件含指定字符串的文件名称

```shell
grep -r "指定字符串"|cut -d ":" -f 1
```

