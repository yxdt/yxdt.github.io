---
layout: post
title: 用Python对上市公司做价值分析：（二）从定期报告中抽取报表数据
date: 2019-09-30 14:23:56 +0800
categories: [Python, Fintech]
tag: [Python, Fintech, DCF, FCFF, CSV]
---

# 一、前言：

本文是用 Python 获取企业年报数据并进行企业价值分析系列文章的第二篇，主要讲述如何将下载的 PDF 定期报告中提取用于企业价值分析的相关数据，提取的报表包括：资产负债表、利润表、现金流量表以及现金流量表补充资料。
![从报告中提取的报表数据](/assets/images/pdf_csv.png)

# 二、知识点:

除了第一篇文章中的相关知识点外，还包括如下内容：

- 掌握库 tabula，pdfminer3k，pandas 的基本用法
- 了解 Adobe PDF 的文件格式
- 了解财务分析的三张表的内容以及会计科目
<!--more-->

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
   ![提取的数据按序存放](/assets/images/csv.png)

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
4. 首先根据不同的表格的命名规则，用 Pdfminer3k 进行定位，然后用 tabula 将相应的表格读出并转换为 csv 格式。
5. 对提取的数据进行简单的过滤，将每一个报告的每一份财务报表以 dataframe 格式读入内存。
6. 对 dataframe 进行相应的数据处理，并保存为 csv 以供下一步导入数据库进行进一步的处理。

<big>2） 关键点</big>

- PDF 的数据定位与提取

  中国上市公司分为上海和深圳两个交易市场，不同市场的股票报表格式略有不同，而且 PDF 文件本身并不是一个对数据友好的文件格式，从其中提取表格中的数据尤其困难。经过挑选，确定了首先使用[pdfminer3k](https://github.com/jaepil/pdfminer3k/)进行表格的定位，然后使用[tabula](https://tabula.technology/)提取相应的表格数据并转化为 csv 格式的文本文件。

- 不同公司、不同年份的报表格式如何统一定位？

  上市公司财务报表的编制虽然要遵循会计准则，然而不同公司甚至同一公司不同年份的财务报表的编制都有不同。这给数据提取造成了相当的困难， 而解决方法也只能通过大量读取财务报告，“训练”程序员来解决

- 数据提取不完整的问题
