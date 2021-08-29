---
title: 数据库基础-关系代数与SQL
mathjax: true
categories:
  - 数据库基础
  - SQL
tags:
  - 关系代数
  - SQL
abbrlink: 1189165209
date: 2021-01-31 18:22:33
---

&emsp;&emsp;关系型数据库使用SQL语言进行操作，而关系代数是表达查询的一种抽象的查询语言。在了解SQL之前，有必要学习关系代数的表达方式。

<!-- more -->

### 关系代数

#### 集合运算

- 并：属于R或属于S的元组的集合，表示为`$R\cup S$`。

- 差：属于R但不属于S的元组的集合，表示为`$R-S$`。

- 交：既属于R又属于S的元组的集合，表示为`$R\cap S$`。

- 广义笛卡尔积：两个分别为n目和m目的关系R和S的笛卡尔积是一个(n+m)列的元组的集合。表示为`$R\times S$`。

    ```
    R:                S:
    | A  | B  | C  |  | A  | B  |
    |----|----|----|  |----|----|
    | a1 | b1 | c1 |  | a2 | b1 |
    | a2 | b2 | c2 |  | a3 | b2 |
    | a3 | b3 | b4 |

    R×S:
    | A  | B  | C  | A  | B  |
    |----|----|----|----|----|
    | a1 | b1 | c1 | a2 | b1 |
    | a1 | b1 | c1 | a3 | b2 |
    | a2 | b2 | c2 | a2 | b1 |
    | a2 | b2 | c2 | a3 | b2 |
    | a3 | b3 | b4 | a2 | b1 |
    | a3 | b3 | b4 | a3 | b2 |
    ```

#### 关系运算
1. 选择（Selection）

&emsp;&emsp;表示在关系R中选择满足给定条件的元组，表示为`$\sigma_{\text{F}}(R)$`，其中F表示选择条件。

&emsp;&emsp;eg:`$\sigma_{\text{age<20}}(Student)$`

2. 投影（Projection）

&emsp;&emsp;指从关系R中选择出某些属性列组成新的关系。由于取消了部分列，可能出现重复元组，应取消相同的行。表示为`$\pi_\text{A}(R)$`。

&emsp;&emsp;eg:`$\pi_\text{dept}(Student)$`

```
Student            Projection:
| Sname | Sdept |  | Sdept |
|-------|-------|  |-------|
| aaa   | CS    |  | CS    |
| bbb   | IS    |  | IS    |
| ccc   | IS    |
```

3. 连接（Join）

&emsp;&emsp;连接指从笛卡尔积中选择属性间满足某条件的元组。表示为`$R{\bowtie\over{A\theta B}}S$`。`$\theta$`是比较运算符。

&emsp;&emsp;`$\theta$`为`$=$`时，该连接运算称为**等值连接**。

&emsp;&emsp;自然连接是一种特殊的等值连接，要求比较的分量是相同的属性组，并去掉重复的属性列。记作`$R\bowtie S$`

4. 除（Division）

&emsp;&emsp;给定关系R(X, Y)和S(Y, Z)，其中X, Y, Z为属性组。表示为`$R\div S$`。

```
R            S            R÷S
| X  | Y  |  | Y  | Z  |  | X  |
|----|----|  |----|----|  |----|
| x1 | y1 |  | y2 | z1 |  | x2 |
| x2 | y1 |  | y2 | z2 |
| x2 | y2 |  | y3 | z1 |
| x2 | y3 |
| x3 | y2 |
```

&emsp;&emsp;可以看出除操作就是将S对Y属性投影，然后取出R中包含该投影集合的X，消去重复项目得到的集合。

### SQL

&emsp;&emsp;以上都是关系运算的数学表达，为了转化成数据库系统能够识别的表达，引入了SQL语言。下面将使用SQL语言对各类关系操作进行表示。

#### 查询

```SQL
/******************************************************************/
/* 单表查询 */
SELECT Sno, Sname
FROM Student;

/* 单表查询 - 查询所有属性列 */
SELECT *
FROM Student;

/* 单表查询 - 查询计算值 */
SELECT Sname, 2021-Sage
FROM Student;

/* 单表查询 - 消除重复行 */
SELECT DISTINCT Sno           -- 默认为ALL，即保留重复行
FROM SC;

/* 单表查询 - 满足特定条件 */
SELECT Sname
FROM Student
WHERE Sdept = 'CS';

/* 以下两句等价 */
SELECT Sname, Sage
FROM Student
WHERE Sage < 20;

SELECT Sname, Sage
FROM Student
WHERE NOT Sage >= 20;

/* 单表查询 - 范围 */
SELECT Sname, Sdept, Sage
FROM Student
WHERE Sage BETWEEN 20 AND 23;

SELECT Sname, Sdept, Sage
FROM Student
WHERE Sage NOT BETWEEN 20 AND 23;

/* 单表查询 - 集合 */
SELECT Sname, Ssex
FROM Student
WHERE Sdept IN ('IS', 'MA', 'CS');

/* 单表查询 - 字符匹配 */
SELECT Sname, Sno, Ssex    -- % 代表任意长度字符串
FROM Student               -- _ 代表任意单字符
WHERE Sname LIKE '刘%';    -- 刘xx，刘x，刘xxxxxx
                           -- +----------------+
SELECT Sname               -- |    \%    \_    |   转义
FROM Student               -- +----------------+
WHERE Sname LIKE '欧阳__'; -- 欧阳xx

SELECT Sno
FROM SC
WHERE Grade IS NULL;       -- IS 不能换成 =

/* 多重条件 */
SELECT Sname
FROM Student
WHERE (Sdept='IS' OR Sdept='CS') AND Sage<20;

/* 排序 */
SELECT Sno, Grade
FROM SC
WHERE Cno='3'
ORDER BY Grade DESC;       -- DESC 降序， ASC 升序

/* 集函数 */
SELECT COUNT(*);           -- 统计元组个数
SELECT COUNT(Attr);        -- 统计某一列值的个数
SELECT SUM(Attr);          -- 计算某一列值总和
SELECT AVG(Attr);          -- 计算某一列平均值
SELECT MAX(Attr);          -- 求某一列最大值
SELECT MIN(Attr);          -- 求某一列最小值

/* 对结果分组 */
SELECT Cno, COUNT(Sno)
FROM SC
GROUP BY Cno;              -- 按照Cno进行分组，具有相同Cno的元组分为一组

/* HAVING */
SELECT Sno
FROM SC
GROUP BY Sno
HAVING COUNT(*)>3;

/******************************************************************/
/* 连接查询 */
/* 等值连接 */
SELECT Student.*, SC.*
FROM Student, SC
WHERE Student.Sno=SC.Sno;

/* 自然连接 */
SELECT Student.Sno, Sname, Ssex, Sage, Sdept, Cno, Grade
FROM Student, SC
WHERE Student.Sno=SC.Sno;

/* 自身连接 */
SELECT FIRST.Cno，SECOND.Cpno
FROM Course FIRST, Course SECOND
WHERE FIRST.Cpno = SECOND.Cno;

/* 外连接 */
SELECT Student.Sno, Sname, Ssex, Sage, Sdept, Cno, Grade
FROM Student, SC
WHERE Student.Sno = SC.Sno(*);    -- 右外连接， *表示允许空值

/* 复合条件连接 */
SELECT Student.Sno, Sname
FROM Student, SC
WHERE Student.Sno=SC.Sno AND
      SC.Cno='2' AND
      SC.Grade>90;

/******************************************************************/
/* 嵌套查询 */
SELECT Sname            -- 父查询
FROM Student
WHERE Sno IN
    SELECT Sno          -- 子查询
    FROM SC
    WHERE Cno='2';      -- 子查询不能使用ORDER BY

SELECT Sname
FROM Student
WHERE Sage < ANY(SELECT Sage
                 FROM Student
                 WHERE Sdept='IS')
      AND Sdept != 'IS';          -- 等价于Sage < (SELECT MAX(Sage) ...)

SELECT Sname
FROM Student
WHERE Sage < MAX(SELECT Sage
                 FROM Student
                 WHERE Sdept='IS')
      AND Sdept != 'IS';          -- 等价于Sage < (SELECT MIN(Sage) ...)

SELECT Sname
FROM Student
WHERE EXISTS                      -- 返回 TRUE 或 FALSE
    (SELECT *
     FROM SC
     WHERE Sno=Student.Sno AND Cno='1');

/******************************************************************/
/* 集合查询 */
SELECT *
FROM Student
WHERE Sdept='CS'
UNION              -- 合并两个查询的结果，自动消除重复项
SELECT *
FROM Student
WHERE Sage<=19
```

#### 总结

&emsp;&emsp;SELECT语句一般形式：
```SQL
SELECT [ALL|DISTINCT] <目标列表达式> [别名] [, <目标列表达式> [别名]]...
FROM <表名或视图名> [别名] [, <表名或视图名> [别名]]...
[WHERE <条件表达式>]
[GROUP BY <列名1> [HAVING <条件表达式>]]
[ORDER BY <列名2> [ASC|DESC]];

/*
注意的WHERE和HAVING的区别！
WHERE作用于表/视图，HAVING作用于组（GROUP）。
*/
```
