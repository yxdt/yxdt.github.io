---
layout: post
title: 用Python对上市公司做价值分析：（四）报表数据清洗、整理与导入数据库
date: 2019-11-26 19:53:35 +0800
categories: [Python, Fintech, SQL Server]
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
