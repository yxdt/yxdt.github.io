---
layout: post
title: 用Python进行公司价值分析：（一）上市公司定期报告的下载
date: 2020-04-20 14:23:56 +0800
categories: Python, Fintech
---

{% include header.html %}

# 一、前言：

本文是用 Python 获取企业年报数据并进行企业价值分析系列文章的第一篇，主要讲述如何用 Python 从巨潮网站下载相应的企业定期报告。

# 二、知识点:

- 了解 Python 的基本语法，了解类的概念
- 掌握爬虫库 selenium 基本用法
- 安装了 Python 运行开发环境，如 Anaconda

# 三、准备工作：

<big>1.上市公司价值分析之 DCF 模型</big>

对上市公司的价值分析主要是通过折现现金流模型 DCF（Discounted Cashflow Model），该模型的主要计算方法是将企业未来所产生的现金流量折算为当前时点的现值，即为企业的内在价值。

$$P=\sum_{t=1}^n\frac{CF_t}{(1+r)^t}$$

- $P$：企业的价值
- $CF_t$: 企业在 $t$ 时刻产生的现金流
- $r$：预期现金流的折现率
- $n$：企业的续存期

<big>2.关于巨潮资讯 cninfo</big>

[巨潮资讯网](http://www.cninfo.com.cn) 是证监会指定的深圳上市公司法定信息披露网站，其实不论沪市还是深市的上市公司的定期报告、公告都可以在这里查到，以 PDF 格式为主。

_备注：似乎从 2019 年开始，巨潮资讯网改版后本来可以直接通过上市公司代码下载对应的公告的功能，现在还需要找到上市公司对应的 orgId 后才能下载。而这个 orgId 本身没有什么规律可言，所以每次都需要人工打开网站进行查询，获取 orgId。_

<big>3.下载数据范围</big>

要计算企业的内在价值，就需要通过企业的三张表（资产负债表、利润表、现金流量表以及现金流量表补充资料）提取相应的财务数据进行计算，因此需要下载企业的一季报、半年报、三季报和年报。

<big>4.关于 Python 开发环境</big>

Python 语言本身就不用多说，近年来随着大数据人工智能的兴起，越来越火，语言语法本身除了没有大括号和分号，采用退格缩进来进行语句块的划分比较另类外，基本属于 C-Java-C#-Javascript 这类 C 系列的影子，所以有上述语言基础的学习起来相当顺手。

Anaconda 作为 Python 语言的综合开发环境可谓功能强大不但免费量又足，JupterLab 现在也支持暗黑模式了，作为首选开发环境就可以了。当然，作为程序员，对显示字体的要求可谓苛刻，Source Code Pro 当仁不让。这些都可以通过系统的设置进行修改：

```json
{
  "codeCellConfig": {
    "lineNumbers": true,
    "fontFamily": "Source Code Pro Light",
    "lineHeight": 1.8
  }
}
```

# 四、程序及结构：

<big>1. 流程说明</big>

财务报表下载流程逻辑本身很简单：

1. 确定要下载的报表的上市公司，获取对应的代码，如：中国国旅对应的是 _601888_
2. 打开[巨潮资讯网](http://www.cninfo.com.cn) ,在最上方的搜索框输入代码，转到对应的查询页面，通过地址栏的字符串“http://www.cninfo.com.cn/new/disclosure/stock?orgId=9900008313&stockCode=601888” 获取对应的 orgId：_9900008313_
3. 确认下载目录和报告存放目录后，以及确认拷贝成功后删除下载目录的对应文件。定义并初始化下载类，并运行下载程序：
   ```python
   dl = PdfDownloader("601888", "9900008313", downFolder, copyToFolder, True)
   ```
4. 下载类 PdfDownloader 的参数分别为:

   - stockCode：要下载的上市公司股票代码
   - orgId： 对应的 orgId
   - downFolder: 下载目录
   - copyToFolder: 目标保存目录
   - downloadFlag: 是否真正下载 PDF

5. 下载程序会模拟人点击，仅下载上市公司的一季报、半年报、三季报、年报。
6. PDF 文件下载后进行重命名，名称包括了年份以及报告季度，便于后续程序识别。
7. 如果有多页，则按顺序翻页，下载全部所需报告
8. 下载完成后，将 PDF 文件移动到存放目录以备后续报表数据识别清洗程序使用

<big>2. 类结构</big>

```python

# 根据上市公司股票代码，在巨潮网站上下载其全部定期报告（一、三季报、半年报、年报）PDF备用
class PdfDownloader:
    # 类初始化
    # stockCode：    要下载的上市公司股票代码
    # orgId：        对应的 orgId
    # downFolder:   下载目录
    # copyToFolder: 目标保存目录
    # downloadFlag: 是否真正下载 PDF
    def __init__(self, stockCode, orgId, downFolder, copyToFolder, downloadFlag = True):
        ...

    #将下载目录下的所有文件拷贝到目标文件
    def copyPdf(self, del_src=True):
        ...

    #下载指定上市公司的定期报告
    def downloadReports(self):
        ...

```

<big>3. 关键包简介</big>

- selenium：功能强大的测试工具，这里用于从网站获取报表存放路径并下载。首先在 Anaconda 中的“Environments”下确认 selenium 包是否已经安装，如果成功安装则可以在 python 程序中进行引用。[selenium 官方网站相关文档](https://www.selenium.dev/documentation/en/)

- BeautifulSoup: 通过该包可以对下载的网页进行分析，获取需要下载的报告的链接以及相关信息。当前使用的是 Beautiful Soup 4，[BeautifulSoup 官方文档](https://www.crummy.com/software/BeautifulSoup/)

<big>4. 主要功能实现</big>

- 下载文件的前置条件：

  - 首先需要安装 Chromedriver，由于众所周知的原因国内无法直接从官方网站下载，只能绕道镜像站点：[Chromedriver 淘宝镜像](https://npm.taobao.org/mirrors/chromedriver)
  - PDF 文件的 Chrome 下载首选项不是保存到本机，而是打开，这不符合实际需要，因此应该重新设置为下载而非打开。_这里需要注意的是，目前还没有找到自动设置 PDF 下载而非打开的方法。_

```python
driver = webdriver.Chrome()
driver.get('chrome://settings/content/pdfDocuments')
time.sleep(5) #give you 5 seconds to click the button to allow browser to download PDF instead of open it.
```

- 文件定位及下载

  - 利用 selenium 的 .find_element_by_xpath 强大网页元素定位功能，定位 PDF 下载链接，获取相应的下载地址：

    ```python
      year_report_name = "/html/body/div/div/label/span/span[@title='年报']"
      quater_report_name = "/html/body/div/div/label/span/span[@title='半年报']"
      half_report_name = "/html/body/div/div/label/span/span[@title='一季报']"
      third_report_name = "/html/body/div/div/label/span/span[@title='三季报']"

    ```

  - 获取当前页面的全部报告链接并下载：
    ```python
    rpts = soup.select('.data-detail table tbody td div a')
    ...
    # 逐一下载
    for rpt in rpts:
      i+=1
      tmp_url = rpt.get('href')
    ```
  - 下载文件，如果文件太大则等待 1 秒，最多等待 20 秒：
    ```python
    down_file = self.down_dir+'\\'+annId+'.PDF'
    print(os.path.isfile(down_file))
    if (len(annId) > 0 and len(annTime) > 0 ):
      driver.get(pdf_down_url + annTime+'/'+annId+'.PDF')
      print('download it!')
      while (not os.path.isfile(down_file)) and wait_pdf :
        print('not downloaded yet, wait for a sec.')
        time.sleep(1)
        sleepMax += 1
        if (sleepMax > 20) :
          wait_pdf = False
    ```
  - 成功下载后，根据命名规则对文件进行重命名，使其具有相应的信息，哪一年，哪一季度的哪个报告。

    ```python
    #判断是否成功下载到本机
    if(os.path.isfile(down_file)) and len(rpt.get_text()) > 5 :
      print('found , rename')
      print(down_file)
      print(rpt.get_text())
      if not os.path.isfile(self.down_dir+'\\'+rpt.get_text().replace("*","_")+'.PDF'):
        os.rename(down_file, self.down_dir + '\\' + rpt.get_text().replace("*","_") + '.PDF')

    ```

  - 检测下载列表是否有分页，如果有则继续跳转到下一页，直到分页的最后一页：

    ```python
      not_last_page = False
      alllink = pagnav.find_element_by_class_name("btn-next").get_attribute('disabled')
      if alllink == None:
        print('found next page')
        ...
      else:
        print('reached last page')

    ```

* 下载文件的分类存储

  将已下载的 PDF 文件移动到指定的目标目录下，按照股票代码建立一级目录，然后建立 PDF 子目录，将全部 pdf 文件移入，并按照实际需要，将对应的定期报告进行重命名：

  ```python
    ...
    for afile in dirs:
      #print(type(file))
      if afile.lower().endswith('.pdf') :
        s_file = os.path.join(self.down_dir, afile)
        the_year = re.findall("\d{4}", afile)[0] if len(re.findall('\d{4}', afile)) > 0 else '0000'
        bfile = stock_code1 + the_year
        if afile.find('摘要') > 0 :        #忽略文件
          bfile = 'zz_'+ bfile + afile
        elif afile.find('正文') > 0:       #忽略文件
          bfile = 'zz_'+ bfile + afile
        elif afile.find('更正') > 0:       #忽略文件
          bfile = 'zz_' + bfile + afile
        elif afile.find('公告') > 0:       #忽略文件
          bfile = 'zz_' + bfile + afile
        elif afile.find('半年度报告') > 0:  #关键报告
          bfile += 'q2.pdf'
        elif afile.find('年度报告') > 0:    #关键报告
          bfile += 'q4.pdf'
        elif afile.find('第一季度') > 0:    #次要文件
          bfile += 'q1.pdf'
        elif afile.find('第三季度') > 0:    #次要文件
          bfile += 'q3.pdf'
        else :
          bfile = 'zz_' + bfile + afile
    ...
  ```

<big>5. 完整源代码</big>

```python
## Author: T.Y.X.D
## Created At : 2018-06-12
## Last Update: 2020-04-06
##
## 从巨潮网站下载对应上市公司的一季报\半年报\三季报\年报.
## 下载后将pdf重新命名并移动到对应的目录下
##
## 参数:
##  stock_code  : 股票代码
##  base_url    : 巨潮网站网址
##  down_dir    : 浏览器下载存放目录
##  target_dir  : 分类存放目录
##  ju_chao     : 下载页面
##
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.support.wait import WebDriverWait
from selenium.webdriver.chrome.options import Options
from bs4 import BeautifulSoup
import time
import shutil
import re
import os

class PdfDownloader:
  def __init__(self, stockCode, orgId, downFolder, copyToFolder, downloadFlag = True):
      self.stock_code = stockCode
      self.org_id = orgId
      self.ju_chao = "http://www.cninfo.com.cn/new/disclosure/stock?orgId="+ orgId +"&stockCode="
      self.base_url = 'http://www.cninfo.com.cn'
      self.down_dir = downFolder # 'C:\\Users\\Downloads'
                                # down_dir = "F:\\downloads"
      self.to_dir = copyToFolder # 'f:\\work2018\\stock\\reports'

      # False if you don't want to download the file
      self.download_file = downloadFlag
      #ju_chao = "http://www.cninfo.com.cn/new/disclosure/stock?plate=szse&orgId=9900002162&stockCode="
      #ju_chao = "http://www.cninfo.com.cn/new/disclosure/stock?plate=sse&orgId=9900008313&stockCode="
      #ju_chao = "http://www.cninfo.com.cn/new/disclosure/stock?orgId=9900005965&stockCode="

  #def downloadPdf(driver, url, burl):
  #    #url  = '/cninfo-new/disclosure/sse/bulletin_detail/true/63219331?announceTime=2013-10-31'
  #    #durl = '/cninfo-new/disclosure/sse/download/63219331?announceTime=2013-10-31'
  #    durl = url.replace('bulletin_detail/true', 'download')
  #    driver.get(burl+durl)

  def copyPdf(self, del_src=True):

      pre = 'sz'
      if(self.stock_code.find('60') == 0):
          pre = 'sh'
      stock_code1 = pre + self.stock_code[-6:] #for target_dir
      print(stock_code1)
      iCnt = 0

      if not os.path.exists(self.to_dir+'\\'+stock_code1):
          os.mkdir(self.to_dir+'\\'+stock_code1)
      if not os.path.exists(self.to_dir+'\\'+stock_code1 + '\\pdf') :
          os.mkdir(self.to_dir+'\\' + stock_code1 + '\\pdf')

      to_dir_pdf = self.to_dir + '\\'+stock_code1+'\\pdf'
      dirs = os.listdir(self.down_dir)

      for afile in dirs:
          #print(type(file))
          if afile.lower().endswith('.pdf') :
              s_file = os.path.join(self.down_dir, afile)
              the_year = re.findall("\d{4}", afile)[0] if len(re.findall('\d{4}', afile)) > 0 else '0000'
              bfile = stock_code1 + the_year
              if afile.find('摘要') > 0 :
                  bfile = 'zz_'+ bfile + afile
              elif afile.find('正文') > 0:
                  bfile = 'zz_'+ bfile + afile
              elif afile.find('更正') > 0:
                  bfile = 'zz_' + bfile + afile
              elif afile.find('公告') > 0:
                  bfile = 'zz_' + bfile + afile
              elif afile.find('半年度报告') > 0:
                  bfile += 'q2.pdf'
              elif afile.find('年度报告') > 0:
                  bfile += 'q4.pdf'
              elif afile.find('第一季度') > 0:
                  bfile += 'q1.pdf'
              elif afile.find('第三季度') > 0:
                  bfile += 'q3.pdf'
              else :
                  bfile = 'zz_' + bfile + afile

              t_file = os.path.join(to_dir_pdf, bfile)
              while os.path.exists(t_file) :
                  if afile.find('更新后') > 0 :
                      cfile = os.path.join(to_dir_pdf, 'old_' + bfile)
                      while os.path.exists(cfile):
                          cfile = cfile.replace('.pdf','_a.pdf')
                      shutil.move(t_file, cfile)
                  else :
                      bfile = 'dup_'+bfile + afile
                  t_file = os.path.join(to_dir_pdf, bfile)
              shutil.move(s_file, t_file)
              #print(s_file)
              iCnt += 1
              print(iCnt)
              print(t_file)

  def downloadReports(self): #  the_stock_code, ju_chao_url, web_url, from_dir, to_dir):

      #input_id = "search_input_obj"
      #c5_class_name = "drop-down-list top-search-list"
      #cat_list_id = 'category_list'

      c5_class_name = "el-popover__reference" #"page-more-filter"#"drop-down-title"
      #filter_name = "分类"
      year_report_name = "/html/body/div/div/label/span/span[@title='年报']" #'category_ndbg_szsh'
      quater_report_name = "/html/body/div/div/label/span/span[@title='半年报']" #"半年报"#'category_yjdbg_szsh'
      half_report_name = "/html/body/div/div/label/span/span[@title='一季报']" #'category_bndbg_szsh'
      third_report_name = "/html/body/div/div/label/span/span[@title='三季报']" #'category_sjdbg_szsh'
      #pdf_option = "html> body> settings-ui> settings-main> settings-basic-page> settings-section> settings-privacy-page> settings-subpage> settings-pdf-documents> settings-toggle-button"
      #pdf_option = "/html/body/settings-ui/div/settings-main/settings-basic-page/div/settings-section/settings-privacy-page/settings-animated-pages/settings-subpage/settings-pdf-documents/settings-toggle-button"
      pdf_option = "/html/body/settings-ui/div[@id='container']/settings-main/settings-basic-page/div[@id='basicPage']/settings-section[@section='privacy']/settings-privacy-page/settings-animated-pages/settings-subpage[@route-path='/content/pdfDocuments']/settings-pdf-documents/settings-toggle-button"
      #             /html/body/settings-ui//div[2]/settings-main//settings-basic-page//div[1]/settings-section[3]/settings-privacy-page//settings-animated-pages/settings-subpage/settings-pdf-documents//settings-toggle-button
      pdf_option = "/html/body/settings-ui/div"
      #driver = webdriver.Firefox() #works but buggy
      #driver = webdriver.Edge()

      #down_dir = "f:\\work2018\\stock\\reports\\" + the_stock_code + "\\pdf"

      #fp = webdriver.FirefoxProfile()
      #fp = webdriver.ChromeProfile()
      #fp.set_preference("browser.download.folderList", 2)
      #fp.set_preference("browser.download.manager.showWhenStarting", False)
      #fp.set_preference("browser.download.dir", down_dir)
      #fp.set_preference("plugin.disable_full_page_plugin_for_types", "application/pdf")
      #fp.set_preference("pdfjs.disabled", True)
      #fp.set_preference("browser.helperApps.neverAsk.saveToDisk", "application/pdf")

      #down_dir = "C:\\Users\\terry\\Downloads"
      #down_dir = "F:\\downloads"
      #chromeOptions = Options()
      #profile =  {"plugins.plugins_list": [{"enabled": False, "name": "Chrome PDF Viewer"}],
      #           "download.default_directory": self.down_dir,
      #           "download.extensions_to_open":"",
      #           "plugins.alwarys_open_pdf_externally": True}
      #chromeOptions.add_experimental_option("prefs", profile)
      #driver = webdriver.Chrome(options=chromeOptions)

      driver = webdriver.Chrome()
      driver.get('chrome://settings/content/pdfDocuments')
      time.sleep(5)
      #
      #????如何自动点击pdf按钮,更改设置??????
      #pdfbtn = driver.find_element_by_xpath(pdf_option)
      #print(pdfbtn)
      #pdfbtn.click()
      #time.sleep(30000000)

      #data = driver.page_source
      #print(data)
      #driver.find_element_by_tag_name('button').click()
      #time.sleep(5)

      #driver= webdriver.Firefox(fp)
      print("download file dir:")
      print(self.down_dir)
      rptDict = {}
      try:
          wait = WebDriverWait(driver, 10)
          driver.get(self.ju_chao + self.stock_code+'#')
          #driver.find_element_by_class .switch_to_frame("i_nr");
          driver.find_element_by_class_name(c5_class_name).click()
          #driver.find_element_by_link_text(filter_name).click()
          time.sleep(2)
          #driver.find_element_by_link_text(year_report_name).click()
          driver.find_element_by_xpath(year_report_name).click()
          time.sleep(2)
          driver.find_element_by_xpath(quater_report_name).click()
          time.sleep(2)
          driver.find_element_by_xpath(half_report_name).click()
          time.sleep(2)
          driver.find_element_by_xpath(third_report_name).click()
          time.sleep(6)
          not_last_page = True
          pdf_down_url = 'http://static.cninfo.com.cn/finalpage/'
          while not_last_page:
              print('loop'.center(50,'+'))
              data = driver.page_source
              #print('data:')
              #print(data)
              #对html进行解析，如果提示lxml未安装，直接pip install lxml即可
              soup=BeautifulSoup(data,'lxml')
              #print(soup)
              #pagnav = driver.find_element_by_id('pagination')
              pagnav = driver.find_element_by_class_name('el-pagination')
              alllink = pagnav.find_elements_by_tag_name('li')
              #遍历全部链接
              #rpts = soup.find(id='ul_his_fulltext').findChildren("li")
              #view pdf href="/new/disclosure/detail?plate=szse&amp;stockCode=002115&amp;
              #announcementId=22459000&amp;announcementTime=2007-04-06 06:30"
              #download pdf http://static.cninfo.com.cn/finalpage/2007-04-06/22459000.PDF
              #             http://static.cninfo.com.cn/finalpage/2007-04-06/22459000.PDF
              rpts = soup.select('.data-detail table tbody td div a')
              print('rpts:')
              print(rpts)
              if not self.download_file:
                  rpts = [] # if testing not downloading , un-comment it .
              i = 0
              for rpt in rpts:
                  i+=1
                  print(i)
                  #a = rpt.find('a')
                  tmp_url = rpt.get('href')
                  sleepMax = 0
                  wait_pdf = True
                  annId = ''
                  annTime = ''
                  valid = False
                  rptDict[rpt.get_text()] = tmp_url
                  #print(rptDict[rpt.get_text()])
                  annIdPos = tmp_url.find('announcementId=')

                  print(tmp_url)
                  rpt_name = rpt.get_text()
                  print(rpt_name)
                  print(rpt_name.find('浏览'))
                  if rpt_name.find('浏览') >= 0:
                      print('dup-no-need to download')
                      continue

                  if annIdPos > 0 :
                      annIdPos += 15
                      #print(annIdPos)
                      annIdEndPos = tmp_url.find('&', annIdPos)
                      #print(annIdEndPos)
                      annId = tmp_url[annIdPos: tmp_url.find('&', annIdPos)]
                      #print('announcementId:')
                      #print(annId)

                  annTimePos = tmp_url.index('announcementTime=')
                  if (annTimePos > 0) :
                      annTimePos += 17
                      annTime = tmp_url[annTimePos: annTimePos+10]
                      #print('announcementTime:')
                      #print(annTime)
                  down_file = self.down_dir+'\\'+annId+'.PDF'
                  print(os.path.isfile(down_file))

                  if (len(annId) > 0 and len(annTime) > 0 ):
                      driver.get(pdf_down_url + annTime+'/'+annId+'.PDF')
                      print('download it!')
                      while (not os.path.isfile(down_file)) and wait_pdf :
                          print('not downloaded yet, wait for a sec.')
                          time.sleep(1)
                          sleepMax += 1
                          if (sleepMax > 20) :
                              wait_pdf = False

                      if(os.path.isfile(down_file)) and len(rpt.get_text()) > 5 :
                          print('found , rename')
                          print(down_file)
                          print(rpt.get_text())
                          if not os.path.isfile(self.down_dir+'\\'+rpt.get_text().replace("*","_")+'.PDF'):
                              os.rename(down_file, self.down_dir + '\\' + rpt.get_text().replace("*","_") + '.PDF')
              #print(rptDict)
              #检测是否到最后一页
              not_last_page = False
              alllink = pagnav.find_element_by_class_name("btn-next").get_attribute('disabled')
              print('alllink:[')
              print(alllink)
              print(']')
              if alllink == None:
                  print('found next page')
                  not_last_page = True
                  pagnav.find_element_by_class_name("btn-next").click()
                  time.sleep(2)
                  wait.until(EC.presence_of_element_located((By.CLASS_NAME, "footer")))
                  #break
              else:
                  print('reached last page')

              #for ia in alllink:
              #    print(ia.text)
              #    print(ia.get_attribute('class'))
              #    if ia.text == '>':
              #        #print(ia.get_attribute('class').find('disabled'))
              #        if ia.get_attribute('class').find('disabled') < 0:
              #            print('找到下一页')
              #            not_last_page = True
              #            ia.click()
              #            time.sleep(2)
              #            wait.until(EC.presence_of_element_located((By.CLASS_NAME,"footer")))
              #            break
              #        else:
              #            print('已经到达最后一页')
      finally:
          print('done')

      time.sleep(3)
      #将下载pdf移到对应目录下 : reports/#stock_code#/pdf/
      self.copyPdf()

print('Class Declared.')
```
