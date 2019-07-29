---
title: Hive使用
toc: true
date: 2019-07-28 21:16:32
tags: [Hive]
categories:
---

## Hive 设置

大多数的Hadoop job是需要hadoop提供的完整的可扩展性来处理大数据的。不过，有时hive的输入数据量是非常小的。在这种情况下，为查询出发执行任务的时间消耗可能会比实际job的执行时间要多的多。对于大多数这种情况，hive可以通过本地模式在单台机器上处理所有的任务。对于小数据集，执行时间会明显被缩短。如此一来，对数据量比较小的操作，就可以在本地执行，这样要比提交任务到集群执行效率要快很多。

配置如下参数，可以开启Hive的本地模式：

```sql
-- 默认为 false
set hive.exec.mode.local.auto=true;
```

当一个job满足如下条件才能真正使用本地模式：

1.job的输入数据大小必须小于参数：hive.exec.mode.local.auto.inputbytes.max(默认128MB)
2.job的map数必须小于参数：hive.exec.mode.local.auto.tasks.max(默认4)
3.job的reduce数必须为0或者1

## 加载数据

### 加载本地数据

#### 例子1

```sql
-- 创建数据库
CREATE DATABASE IF NOT EXISTS test;

-- 显示数据库
SHOW DATABASES;
DESCRIBE DATABASE test;

-- 删除表
DROP TABLE IF EXISTS test.stu1;

-- 创建表
CREATE TABLE IF NOT EXISTS test.stu1 (
    name STRING COMMENT 'student name',
    gender STRING COMMENT 'student gender',
    age INT COMMENT 'student age',
    sno STRING COMMENT 'student number'
)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ',';

-- 加载本地数据(使用 OVERWRITE 会覆盖数据), 本地数据见下面
LOAD DATA LOCAL INPATH './mydata/student.txt'
OVERWRITE INTO TABLE test.stu1;

-- 统计 (其实我就是想使用一个 GROUP BY)
SELECT name, gender, sum(case when gender='boy' then 1 else 0 end)
FROM test.stu1;
```

#### 例子1 中的数据

`./mydata/student.txt` 数据.

```bash
alex,boy,19,123
bob,boy,20,124
cool,girl,22,125
dodo,boy,21,126
bob,boy,22,127
alex,boy,22,128
else,girl,19,129
mary,girl,20,130
```

### 从 HDFS 中载入数据

其实从本地载入数据到 hive 表的过程中, 其实是先将数据临时复制到HDFS的一个目录下（典型的情况是复制到上传用户的HDFS home目录下,比如/home/wyp/），然后再将数据从那个临时目录下移动（注意，这里说的是移动，不是复制！）到对应的Hive表的数据目录里面。既然如此，那么Hive肯定支持将数据直接从HDFS上的一个目录移动到相应Hive表的数据目录下.

先将数据上传到 HDFS 中

```bash
hadoop fs -mkdir /mydata
hadoop fs -mkdir /mydata/student
hadoop fs -put ./mydata/student.txt /mydata/student
```

然后在 HIVE 中使用:

```sql
DROP TABLE IF EXISTS test.stu2;
CREATE TABLE IF NOT EXISTS test.stu2 (
    name STRING COMMENT 'student name',
    gender STRING COMMENT 'student gender',
    age INT COMMENT 'student age',
    sno STRING COMMENT 'student number'
)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ',';

-- 加载HDFS数据(使用 OVERWRITE 会覆盖数据), 这里面没有 LOCAL 这个关键字
LOAD DATA LOCAL INPATH '/mydata/student/student.txt'
OVERWRITE INTO TABLE test.stu2;

SELECT name, gender, sum(case when gender='boy' then 1 else 0 end)
FROM test.stu2;
```

### 从其他表中查询数据并导入到 Hive 表中

```sql
DROP TABLE IF EXISTS test.stu3;
-- 注意这里没有了 age
CREATE TABLE IF NOT EXISTS test.stu3 (
    name STRING COMMENT 'student name',
    gender STRING COMMENT 'student gender',
    sno STRING COMMENT 'student number'
)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ',';

-- 从 stu2 表中导入数据
INSERT INTO TABLE test.stu3
SELECT name, gender, sno FROM test.stu2;

SELECT name, gender, sum(case when gender='boy' then 1 else 0 end)
FROM test.stu2;
```

### 在创建表的时候通过其他表插值

在实际情况中，表的输出结果可能太多，不适于显示在控制台上，这时候，将Hive的查询输出结果直接存在一个新的表中是非常方便的，我们称这种情况为CTAS（create table .. as select）如下:

```sql
CREATE TABLE IF NOT EXISTS test.stu4 AS
SELECT * FROM test.stu3;
```

## 导出数据

### 将数据导出到本地

```sql
INSERT OVERWRITE LOCAL DIRECTORY './tmp/'
SELECT * FROM test.stu1;
```

### 将数据导出到HDFS

```sql
INSERT OVERWRITE DIRECTORY '/mydata/stu2'
SELECT * FROM test.stu2;
```

## Hive 中的函数

### 函数信息

```sql
-- 显示当前会话有多少函数可用
SHOW FUNCTIONS;

-- 显示函数的描述信息
DESC FUNCTION concat;

-- 显示函数的扩展描述信息 
DESC FUNCTION EXTENDED concat;
```


## 参考资料
> - [Hive的几种常见的数据导入方式](https://www.cnblogs.com/yejibigdata/p/6376421.html)
> - [Hive的内置函数](https://www.cnblogs.com/jifengblog/p/9286800.html)
> - [Hive group by操作](https://blog.csdn.net/lzm1340458776/article/details/43231707)
