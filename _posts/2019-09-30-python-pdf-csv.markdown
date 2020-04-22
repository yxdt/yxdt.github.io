---
layout: post
title: 用Python对上市公司做价值分析：（二）从定期报告中抽取报表数据
date: 2019-09-30 14:23:56 +0800
categories: [Python, Fintech]
tag: [Python, Fintech, DCF, FCFF, CSV]
---

{% include header.html %}

# 一、前言：

本文是用 Python 获取企业年报数据并进行企业价值分析系列文章的第二篇，主要讲述如何将下载的 PDF 定期报告中提取用于企业价值分析的相关数据，提取的报表包括：资产负债表、利润表、现金流量表以及现金流量表补充资料。

# 二、知识点:

除了第一篇文章中的相关知识点外，还包括如下内容：

- 掌握库 tabula，pdfminer，pandas 的基本用法
- 了解 Adobe PDF 文件格式
- 了解财务分析的三张表的内容以及会计科目

# 三、程序结构：

程序运行的先决条件是从上篇文章中所下载的对应企业的 PDF 格式的定期报告。

<big>1） 流程说明</big>

1. 给出需要进行分析的企业代码，报告起始年份，季度和截至年份等数据
   ```python
   class PdfReader:
       def __init__(self, stockCode, yearStart, yearEnd, qtrStart, fistLoop, showDetail):
       #stockCode: 上市公司代码
       #yearStart: 数据提取起始年份
       #yearEnd：数据提取截至年份
       #qtrStart： 数据提取起始季度，仅对第一年有效，以后每年均从第一季度开始
       #firstLoop： 不同企业的报表格式均不同，因此需要多次遍历，首次遍历用于确定表格的最大可能边界
       #showDetail： 系统在提取过程中会产生相关的操作日志，该标志确定是否详细记录相关日志
   ```
2. 程序开始按顺序打开企业的定期报告（每季度一份报告），定位并提取三张表+补充资料的财务数据
   ```python
   def readReports(self):
       start_qtr = self.qtr_start
       for fyear in range(self.year_start, self.year_end + 1):
           logging.warning('fyear:'+str(fyear))
           for fqtr in range(start_qtr,5):
               logging.warning('fqtr:'+str(fqtr))
               logging.warning(('current year: ' + str(fyear) + ' current quater:' + str(fqtr)).center(120,' '))
               genFinCsvs(self.stock_code, fyear, fqtr)
           start_qtr = 1
   ```
3. 报告中对财务报表的组织排列是有顺序的，依次为《资产负债表》、《利润表》、《现金流量表》、《现金流量表补充资料》，因此按照顺序逐表定位并提取相应数据。
4. 对提取的数据进行简单的过滤，将每一个报告的每一份财务报表以 dataframe 格式读入内存。
5. 对 dataframe 进行相应的数据处理，并保存为 csv 以供下一步导入数据库进行进一步的处理。

<big>2） 关键点</big>

- PDF 的数据定位与提取
- 不同公司、不同年份的报表格式如何统一定位
- 数据提取不完整的问题
