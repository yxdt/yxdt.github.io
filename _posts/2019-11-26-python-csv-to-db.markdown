---
layout: post
title: 用Python对上市公司做价值分析：（三）报表数据清洗、整理与导入数据库
date: 2019-11-26 19:53:35 +0800
categories: [Python, Fintech, SQLServer]
tag: [Python, Fintech, Pandas]
---

# 一、前言：

本文是用 Python 获取企业年报数据并进行企业价值分析系列文章的第三篇，主要讲述将前面提取的企业财务报表包括：资产负债表、利润表、现金流量表以及现金流量表补充资料进行标准化整理并导入到数据库，以供后续企业价值分析使用。

![财务数据清洗整理](/assets/images/dbtbl.png)

# 二、知识点:

- 掌握相关库 pandas, sqlalchemy 的相关用法
- Python 字符串匹配操作
- 了解财务分析的三张表的内容以及标准会计科目
- SQL 数据库结构化查询语言的掌握

<!--more-->

# 三、程序结构：

程序运行的先决条件是从前面程序中获得的相关 CSV 文件，每个 csv 文件均对应企业某季度的一份财务报表。

```csv
项目,附注,期末余额,期初余额
流动资产:,,,
货币资金,1.0,"14,619,199.45","6,203,282.81"
存放中央银行款项,2.0,"1,827,040.08","1,279,592.47"
存放同业款项,3.0,"2,024,431.24","2,126,444.99"
衍生金融资产,4.0,"14,339.10","162,513.84"
应收票据,5.0,"26,229,378.71","17,097,233.37"
应收账款,6.0,"13,617,075.57","9,362,102.75"
预付款项,7.0,"1,813,795.05","1,414,470.45"
发放贷款和垫款,8.0,"8,864,168.57","5,940,800.47"

```

<big>1） 数据库表结构</big>

需要两张数据库表存储相关数据：

表一：会计科目表

| 序号 | 列名           | 数据类型     | 备注          |
| ---- | -------------- | ------------ | ------------- |
| 1    | ITEM_ID        | Integer      | PK 科目标识   |
| 2    | REPORT_ID      | Integer      | PK 所属表标识 |
| 3    | PARENT_ITEM_ID | Integer      | 父科目标识    |
| 4    | ITEM_SEQ       | Integer      | 科目序号      |
| 5    | ITEM_NAME      | Nvarchar(50) | 会计科目名称  |
| 6    | ACCT_ITEM_ID   | Integer      | 标准科目标识  |

表二：财报数据表

| 序号 | 列名      | 数据类型      | 备注        |
| ---- | --------- | ------------- | ----------- |
| 1    | RECORD_ID | Integer       | PK 记录标识 |
| 2    | STOCK_ID  | Integer       | FK 所属股票 |
| 3    | ITEM_ID   | Integer       | FK 所属科目 |
| 4    | YEAR      | SmallInt      | 财务年      |
| 5    | QUATER    | TinyInt       | 财务季度    |
| 6    | PRE_VALUE | money         | 前值        |
| 7    | CUR_VALUE | money         | 现值        |
| 8    | COMMENT   | nvarchar(100) | 备注说明    |

<big>2） 程序流程逻辑</big>

1. 按顺序读取相应的 csv 文件，并将其与现有数据库内的会计科目对应
2. 如果会计科目不在数据库中，则需要确认是否是新的科目，还是已有科目另外写法
3. 对应数据提取时出现的跨行、丢失、不同格式等情况进行相应的处理
4. 数据集的标准化
5. 数据导入备用

<big>3） 重点难点</big>

数据清洗是本程序的重点，csv 作为松散的文本格式存储灵活，而数据库表则是严格的数据存储格式。在转换过程中会遇到很多意想不到的问题：

- 不同编号、不同含义
- 非正规科目
- PDF -> CSV 识别错误、信息丢失
- CSV 前后列数不一致
- 正负数据表示不一致
- 同一类报表不同的表现方式如何统一
- 会计科目之间的层级关系的确定

因此，程序的重点在于对数据的确认和清洗,以下是部分非标准会计科目的标准化映射表：

```python

item_pairs = (
(5, '固定资产折旧油气资产折耗生产性生物资产折旧投资性房地产摊销等', '固定资产折旧油气资产折耗生产性生物资产折旧'),
(1, '以后不能重分分类进损益的其他综合收益', '以后不能重分类进损益的其他综合收益'),
(1, '1.重新计量设定受益计划变动额','1.重新计量设定受益计划净负债或净资产的变动'),
(1, '2.权益法下不能转损益的其他综合收益','2.权益法下在被投资单位不能重分类进损益的其他综合收益中享有的份额'),
(1, '1.权益法下可转损益的其他综合收益', '1.权益法下在被投资单位以后将重分类进损益的其他综合收益中享有的份额'),
(2, '子公司吸收少数股东投资收到的金','子公司吸收少数股东投资收到的现金'),
(2, '子公司支付给少数股东的股利润','子公司支付给少数股东的股利利润'),
(2, '五、现金及现金等价物净增加(减少)额','五、现金及现金等价物净增加额'),
(2, '加:年初现金及现金等价物余额','加:期初现金及现金等价物余额'),
(2, '六、年末现金及现金等价物余额','六、期末现金及现金等价物余额'),
(2, '取得投资收益所收到的现金净额', '取得投资收益收到的现金'),
(5, '现金的年末余额', '现金的期末余额'),

```

此外，由于 Python 没有成熟的 ORM 框架，大部分操作需要通过 SQl 语句来完成，所以操作起来会比较麻烦，一旦出现错误，则需要直接通过数据库来完成。还好 Pandas 的 DataFrame 本身功能非常强大，数据也是以二维表的形式存储，这跟数据库表很相似，因此掌握好 DataFrame 就基本能实现 80%的数据清洗与提取的相关工作。

```python
df.rename(columns={'项目':'ITEM_NAME', '附注':'COMMENT', '期末余额':'CUR_VALUE','期初余额':'PRE_VALUE'}, inplace=True) #资产负债表
df.rename(columns={'本期发生额':'CUR_VALUE', '上期发生额':'PRE_VALUE'}, inplace=True) #利润表
df.rename(columns={'本期金额':'CUR_VALUE','上期金额':'PRE_VALUE'}, inplace=True) #利润表\现金流量表
df.rename(columns={'期末金额':'CUR_VALUE','期初金额':'PRE_VALUE'}, inplace=True) #利润表\现金流量表-600498
df.rename(columns={'年初余额':'PRE_VALUE'}, inplace=True)

```

<big>3） Python 标准库 difflib——相似度比较</big>

class difflib.SequenceMatcher

> 这是一个灵活的类，可用于比较任何类型的序列对，只要序列元素为 hashable 对象。 其基本算法要早于由 Ratcliff 和 Obershelp 于 1980 年代末期发表并以“格式塔模式匹配”的夸张名称命名的算法，并且更加有趣一些。 其思路是找到不包含“垃圾”元素的最长连续匹配子序列；所谓“垃圾”元素是指其在某种意义上没有价值，例如空白行或空白符。 （处理垃圾元素是对 Ratcliff 和 Obershelp 算法的一个扩展。） 然后同样的思路将递归地应用于匹配序列的左右序列片段。 这并不能产生最小编辑序列，但确实能产生在人们看来“正确”的匹配。

简而言之，在本程序应用中，由于种种异常情况的出现，会计科目字符串在不同时期报表或者不同公司的报表中不完全相同。对于人类而言显而易见的同一个科目，对于电脑非 0 即 1，仅仅用词不同极有可能判断为 2 个不同科目。
通过该方法可以明确某个会计科目是新的还是已有科目的另外写法两者之间的相似度，如果相似度在 85%以上，可以认为是同一科目的不同表示方法。

> ===============done - join splitted===============
>
> sim_str:归属于少数股东的其他综合收益的税后净额--cur_str:少数股东损益者的其他综合收益的税后净额
>
> sim_ratio:0.8421052631578947
>
> 当前科目:[者的其他综合收益的税后净额]可能变更为[其他综合收益的税后净额],相似度:0.9166666666666666
>
> 继续请输入[Y]或回车, 取消合并请输入 N 或其他任意字符:
>
> ===============done - join splitted===============
