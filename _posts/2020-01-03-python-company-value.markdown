---
layout: post
title: 用Python对上市公司做价值分析：（四）企业价值分析
date: 2019-11-26 19:53:35 +0800
categories: [Python, Fintech, SQL Server]
tag: [Python, Fintech, Pandas]
---

{% include header.html %}

# 一、前言：

本文是用 Python 获取企业年报数据并进行企业价值分析系列文章的第四篇，通过对企业历年（一般设定为 5 年）的资产负债表、利润表、现金流量表以及现金流量表补充资料的数据提取，通过折现现金流模型（DCF）计算企业的价值，作为对投资的指导指标。

![股票Beta值计算](/assets/images/beta.png)

# 二、知识点:

- 掌握相关库 pandas, plotly, sklearn, sqlalchemy 的相关用法
- 掌握基本的统计学相关概念，线性回归
- 了解财务分析的三张表的内容以及标准会计科目
- SQL 数据库结构化查询语言的掌握
- 掌握 DCF 模型中相关变量的计算

<!--more-->

# 三、DCF 模型解析：

通过 DCF 模型计算企业内在价值是最符合价值投资理论的方法，可以通过对企业财务报表的数据分析获得相对客观的企业价值。

$$P=\sum_{t=1}^n\frac{CF_t}{(1+r)^t}$$

- $P$：企业的价值
- $CF_t$: 企业在 $t$ 时刻产生的现金流
- $r$：预期现金流的折现率
- $n$：企业的续存期

由于 DCF 模型建立在欧美财务报表的基础上，由于财税准则等方面的差异，在用中国上市公司的财务报表相关科目对其计算的过程中，难免会有一些不够直观的地方，需要额外计算才可得出。

DCF 的一般形式为：

$$ 公司价值 = \sum_{t=1}^n\frac{FCFF_t}{(1+WACC)^t} + \frac{FCFF_{n+1}/(WACC_n - g_n)}{(1 + WACC)^n} $$

- $FCFF_t$： t 时刻自由现金流，设 n 为 10
- WACC: 企业的加权平均资本成本
- $g_n$：永续增长率，设为无风险利率，0.036

<big>1.自由现金流 FCFF 的计算公式分解</big>

> FCFF（Free Cash Flow for the Firm）：企业产生的，在满足了再投资需求之后剩余的、不影响公司持续发展的前提下、可供企业资本提供者和各种利益要求人（股东、债权人）分配的现金。

FCFF = (税后净利润 + 利息费用 + 非现金支出) - 营运资本追加 - 资本性支出
FCFF = (1-税率 t) x 息税前利润 EBIT + 折旧 - 资本性支出 CAPX - 净营运资金 NWC 的变化

根据 FCFF 的计算公式，下面就分别求出各个变量值

- 企业税率 t：
  根据财务报表计算过去 5 年的企业平均税率:

$$ t = \frac{税收:（会计科目：所得税或所得税费用(133)）} {税前收入: （会计科目：利润总额(103)）} $$

- 息税前利润 EBIT：会计科目：利润总额(103)

- 折旧：会计科目：固定资产折旧、油气资产折耗、生产性生物资产折旧(447)

- 资本性支出 CAPX：
  资本性支出在 A 股上市公司的财务报表中没有直接对应科目，需要进行计算得出，而且中国股市重融资的传统，会不断有定向增发这类操作，对其计算也产生一定影响。

  - 标准公式：
    资本性支出(CAPX) = 会计科目：购建固定、无形、其他长期资产所支付的现金(250) — 会计科目：处置固定、无形、其他长期资产而收回的现金净额(246)

  - 有定向增发的公式变形：
    资本性支出(CAPX) == 期末固定资产净额 - 期初固定资产净额 + 在建工程净增加值 - 资本公积净增加值(356) + 折旧(447)
    其中：资本公积净增加值 == max(0, 期末资本公积 - 期初资本公积)
    在建工程净增加值 == max(0, 期末在建工程净额 - 期初在建工程净额)

- 净营运资本（NWC）的变化：

  - 营运资本 即为 会计科目: 流动资产(367)
  - 净营运资本(NWC: Net Working Capital) = 净流动资产的变化 = 流动资产(367)-流动负债(368)
  - 净营运资本的变化 = 本期 NWC - 上期 NWC

<big>2.加权平均资本成本 WACC 的计算</big>

> 加权平均资本成本（WACC: Weighted Average Cost of Capital)，是指企业以各种资本在企业全部资本中所占的比重为权数，对各种长期资金的资本成本加权平均计算出来的资本总成本。加权平均资本成本可用来确定具有平均风险投资项目所要求的收益率。

$$ WACC = (K_e * W_e) + (K_d(1-t)*W_d) $$

- $WACC$ :加权平均资本成本
- $K_e$ : 公司普通权益资本成本
- $K_d$ : 公司债务资本成本
- $W_e$ : 权益资本在资本结构中的百分比
- $W_d$ : 债务资本在资本结构中的百分比
- t : 公司实际所得税税率

1. 基准收益率 $r_f$：即市场长期无风险收益率，在这里以 30 年期国债收益率来代替，目前为 3.3%。 [国债收益率数据来源](http://yield.chinabond.com.cn/cbweb-mn/yield_main)
2. 普通股的期望收益率：
   $$ E(r) = r_f + beta*(r_m - r_f) $$
   - $beta$ : 股票的 beta 值
   - $r_m$ ： 市场期望回报率 用 A 股过去 5 年的平均涨幅来计算。得出平均市场回报为 6.6%
   - $r_f$ : 计准收益率，见上。
3. 增长假设：假设设未来 5 年平均增长率与过去 5 年平均增长率相同，未来第 6 年起的平均增长率为过去 5 年平均增长率的一半。
4. 永续增长 g: 可以用上面的计准收益率来代替。

<big>3.其他相关财务指标</big>

- 收入增长率：

  $$ 收入增长率 = 营业收入增长率 = \frac{(本期营业收入总额-上期营业收入总额)}{上年营业收入总额(100)} $$

- 净营运利润率 NOP：

  营运利润一般指息税前收入 EBIT,即营业利润 ITEM_ID: 102

  $$ 营运利润率= \frac{营运利润(102)}{营业总收入(100)} $$

- 固定资本投资

  $$ 固定资本投资 = \frac{净资本投资(250)}{营业总收入(100)} $$

  $$ 新增固定投资百分比 = \frac{净资本投资(250)}{营业总收入(100)} $$

- 折旧百分比

  $$ 折旧百分比 = \frac{固定资产折旧、油气资产折耗、生产性生物资产折旧(447)}{营业总收入(100)} $$

- 营运资本占总收入比例

  $$ 营运资本占总收入比 = \frac{应收账款(281) + 存货(290) - 应付账款(322)}{营业总收入(100)} $$

  $$ 营运资本的增加比率 = \frac{营运资本的增加}{总收入(100)} $$

<big>4.财务报表会计科目值的提取</big>

根据对 FCFF 的计算公式的变量分解，可以得出需要提取的财务报表相关会计科目为：

| 序号 | 编号 | 科目说明                                         |
| ---- | ---- | ------------------------------------------------ |
| 0    | 100  | 营业总收入                                       |
| 1    | 101  | 营业总成本                                       |
| 2    | 102  | 营业利润                                         |
| 3    | 103  | 利润总额                                         |
| 4    | 104  | 净利润                                           |
| 5    | 133  | 减所得税费用                                     |
| 6    | 217  | 现金及现金等价物净增加额                         |
| 7    | 246  | 处置固定资产无形资产和其他长期资产收回的现金净额 |
| 8    | 250  | 购建固定资产无形资产和其他长期资产支付的现金     |
| 9    | 281  | 应收账款                                         |
| 10   | 290  | 存货                                             |
| 11   | 301  | 固定资产                                         |
| 12   | 302  | 在建工程                                         |
| 13   | 322  | 应付账款                                         |
| 14   | 356  | 资本公积                                         |
| 15   | 367  | 流动资产合计                                     |
| 16   | 368  | 流动负债合计                                     |
| 17   | 445  | 净利润                                           |
| 18   | 447  | 固定资产折旧油气资产折耗生产性生物资产折旧       |
| 19   | 469  | 现金及现金等价物净增加额                         |

<big>5.程序流程</big>

1. 获取上市公司的相关信息：股份数量、每日开盘收盘价格等信息。
2. 通过上市公司的每日开盘收盘价格记录波动计算股票的 beta 值。
   beta 值的计算是通过线性回归模型得出的,即相对于相应的大盘指数（beta 为 1），股价变动的剧烈程度。

```python
def calcBeta(stock_code , index_code):
    # 获取2013年到现在的股票开盘、收盘、最高、最低交易价格信息以及对应的指数信息
    sql1  = "SELECT A.STOCK_CODE, A.RECORD_DATE, A.OPEN_PRICE, A.HIGH_PRICE, A.LOW_PRICE, A.CLOSE_PRICE, A.TOTAL_AMOUNT, A.TOTAL_QTY "
    sql1 += " FROM DAY_RECORDS AS A "
    sql1 += " WHERE A.STOCK_CODE='" + stock_code + "'"
    sql1 += " AND A.RECORD_DATE > '2013-01-01'"
    sql1 += " ORDER BY A.RECORD_DATE"
    df1 = pd.read_sql_query(sql1, conn)
    df1.columns = ['STOCK_CODE', 'RECORD_DATE', 'OPEN_PRICE','HIGH_PRICE', 'LOW_PRICE', 'CLOSE_PRICE', 'TOTAL_AMOUNT', 'TOTAL_QTY']
    df1 = df1.assign(RETURN_RATE = lambda x: x.CLOSE_PRICE.pct_change())
    df1.head()

    #股票对应指数信息，
    sql2 =  "SELECT A.STOCK_CODE, A.RECORD_DATE, A.OPEN_PRICE, A.HIGH_PRICE, A.LOW_PRICE, A.CLOSE_PRICE, A.TOTAL_AMOUNT, A.TOTAL_QTY "
    sql2 += " FROM DAY_RECORDS AS A "
    sql2 += " WHERE A.STOCK_CODE='" + index_code + "'"
    sql2 += " AND A.RECORD_DATE > '2013-01-01'"
    sql2 += " ORDER BY A.RECORD_DATE"
    dfi = pd.read_sql_query(sql2, conn)
    dfi.columns = ['STOCK_CODE', 'RECORD_DATE', 'OPEN_PRICE','HIGH_PRICE', 'LOW_PRICE', 'CLOSE_PRICE', 'TOTAL_AMOUNT', 'TOTAL_QTY']
    dfi = dfi.assign(RETURN_RATE = lambda x: x.CLOSE_PRICE.pct_change())
    dfi.head()
    #return_stock
    df1p = df1.loc[:,['RECORD_DATE','RETURN_RATE']]
    dfip = dfi.loc[:,['RECORD_DATE','RETURN_RATE']]
    dfr = pd.merge(df1p, dfip, on=('RECORD_DATE'))
    dfr.dropna(how='any', inplace = True)
    dfr.head()
    return_index = dfr.RETURN_RATE_y
    return_stock = dfr.RETURN_RATE_x

    plt.figure(figsize=(20,10))
    return_index.plot()
    return_stock.plot()
    plt.ylabel('股票与对应指数逐日回报率')
    plt.xlabel(stock_code)
    plt.show()
    x = sm.add_constant(return_index.values)
    y = return_stock.values
    model = regression.linear_model.OLS(y,x).fit()
    x = x[:, 1]
    # alpha: model.params[0]
    # beta: model.params[1]
    return model.params[0], model.params[1], df1
```

3. 按照计算 FCFF 所需财务数据，提取相应的财务数据，计算企业向相关增长率。

```python
#10. 计算
#    FCFF0  = 现金及现金等价物净增加额(217) # - 资本性支出(CAPX) - 净营运资金(NWC)的变化(OP_PURE_DELTA)
#    FCFF = 税后净利润+利息费用+非现金支出-营运资本追加-资本性支出
#    FCFF1 = (1-税率(TAX_RATE))x息税前利润EBIT(103)+折旧(447)-资本性支出CAPX-净营运资金(NWC)的变化
#
#    资本性支出(CAPX) == 购建固定、无形、其他长期资产所支付的现金(250) — 处置固定、无形、其他长期资产而收回的现金净额(246)
#          净营运资金(NWC)的变化 (OP_PURE_DELTA)
#
sdf = sdf.assign(FCFF0 = lambda x: (x.CUR_VALUE8))# - x.CAPX - x.OP_PURE_DELTA))
sdf = sdf.assign(FCFF = lambda x: (x.CUR_VALUE5*(1-x.TAX_RATE)+x.CUR_VALUE9 - x.CAPX - x.OP_PURE_DELTA))
sdf = sdf.assign(FCFF1 = lambda x: (x.CUR_VALUE5 * (1 - x.TAX_RATE) + x.CUR_VALUE9 - x.CAPX1 - x.OP_PURE_DELTA))

fcff5 = sdf.FCFF0.mean()
fcffl = sdf.loc[lidx:,'FCFF0'].values
```

4. 计算 WACC。
5. 根据假设条件计算出未来 10 年的 FCFF 预测值以及永续值折现的残值部分
6. 按照企业价值公式，计算出当前企业价值，并除以当前上市公司总股份计算出每股合理股价

```python
def calcWacc(lr, er, dr, beta, e, d,t, oi, r5, r10, g, stock):
    sr =  lr + beta*(0.0667 - lr)
    wacc = sr * (e/(e+d)) + er * (d/(e+d))* (1 - t)
    rate0 = (1 - t)*opr5 + dr5 - capx1r5 - wcr5
    rate = fcff25/oi #过去5年平均 有定增
    brrf = [1+r5,1+r5,1+r5,1+r5,1+r5,1+r10,1+r10,1+r10,1+r10,1+r10] #参考brr5与brrl
    dcr =  1.0 # = 1/(1+wacc)^year ~~ [0.9091, 0.8264, 0.7513,0.6830, 0.6209, 0.5644...]
    fcff = 0

    for iR in brrf:
        oi = oi * iR
        dcr = dcr / (1 + wacc)
        fcff += oi * rate * dcr
        print(fcff, oi, rate, dcr)
    fcf10 = oi * rate * dcr
    ppv = fcf10 * (1+ g)/(wacc - g)
    ppv = ppv / pow(1+wacc, 10)
    cf = fcff + ppv
    price = cf/stock
    return wacc, cf, price
```

7. 参考当前市场股价，得出股价相对价值是否有高估或低估的情况。
