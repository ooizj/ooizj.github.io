# linux按时间拷贝文件

linux按修改时间拷贝文件

```bash
find 目录 -type f -name 文件名 -newermt 起始日期（包含） ! -newermt 截止日期（不包含） -exec cp {} 目标目录  \;

# 拷贝“/tmp/1”下面2019年08年28日的所有jpg文件到“/tmp/2”
# cd /tmp/1
# find . -type f -name '*.jpg' -newermt 2019-08-28 ! -newermt 2019-08-29 -exec cp {} /tmp/2/  \;
```



