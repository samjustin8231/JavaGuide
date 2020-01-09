
# find
查看已.log为后缀的日志文件列表

find -name "*.log" 或者 find -name "*.gz"

# cat
cat 从第一行開始显示档案内容。

cat -n test.log |grep "debug"  得到关键日志的行号

cat -n test.log |grep "debug" >debug.txt 输出到文件
## grep
有时候文件比较大，通过less命令来查询效率会非常低下。这个时候我们就需要使用grep命了来过滤去我们需要的信息， 比如我要查找 info.log 文件中 关键词为 keyword 的日记，命令如下：

grep 'keyword' info.log

keyword 消息记录太多，我们可以结合less命令来展示,
grep keyword -n info.log | less

### or and
and
grep -n '日志排查' test.log | grep '日志'

or 
用-E 例如：grep -n -E '日志排查|hello' test.log  满足两个关键字的都可以找出来

### 前后日志

我要显示keyword关键词前后 n行记录的日记
grep keyword -n info.log

cat error.log | grep -B 5 'nick' 显示nick及前5行

cat error.log | grep -A 5 'nick' 显示nick及后5行

cat error.log | grep -C 5 'nick' 显示file文件里匹配nick字串那行以及上下5行

cat error.log | grep -n -B10 -A10 5 'nick' 显示file文件里匹配nick字串前后10行

### 行数范围查找
cat -n test.log |tail -n +1000|head -n 20   从第1000行开始，显示20行
tail -n +1000表示查询1000行之后的日志
head -n 20 则表示在前面的查询结果里再查前20条记录
            
            
### sed根据时间段搜索

一般在日志系统中都会记录打印日志的时间，通常我们非常需要查找指定时间端的日志：

sed -n '/2014-12-17 16:17:20/,/2014-12-17 16:17:36/p'  test.log

特别说明:该命令中的两个日期值必须是日志文件中包含的值,否则该命令无效.； 先 grep '2014-12-17 16:17:20' test.log 来确定日志中是否有该 时间点


### sed根据行号搜索
sed -n '100,200p' diamond-client.log.2020-01-02

# head
head 仅仅显示前面几行

head -n 10  test.log   查询日志文件中的头10行日志;


# tail
tail 仅仅显示后面几行(默认会显示日志的最后10行记录)

tail -n 100 filename   查看最尾部100行
-n 可以用来指定行数  n也就是number的意思

tail -f filename 
-f  会把实时产生的日志追加到标准输出中来，也就是我们的终端 
f 我认为是flush的缩写  也就是刷新的意思 

tail -100f test.log      实时监控100行日志

# more
more 分页显示档案内容。

日志内容特别多，打印在屏幕上不方便查看时，用more或less




# less
less 与 more 相似，但支持向前翻页

less 可以对文件进行分页，
less filename
可以用/或者? 去匹配过滤信息
然后n是匹配下一个 
N匹配上一个 
b 是上一页 
d是下一页

可以通过输入大写的G，快速翻页到文件末尾或开始
# vim
最开始我就是用vim去查看日志的
慢慢觉得vim有点笨重
vim 也是用 /或者? 去过滤
n 匹配next
N 匹配上一个