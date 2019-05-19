# 作业系统设计

## ieltsonlinetest雅思练习题

### 输入

入口页面:[http://top.zhan.com/](http://top.zhan.com/)

Test Collection: 入口页面academic下面的每一个是一个TestCollection

例如，第一个[http://top.zhan.com/ielts/listen/cambridge.html](http://top.zhan.com/ielts/listen/cambridge.html)

每一个TestCollection下会包含多个Practice Test。 每个Practice Test 又包含Listening、Reading、Writing、Speaking四个部分。其中Listening、Reading、Writing三个部分我们需要View Solution部分，Speaking部分我们需要Take Task部分。

### 输出

#### page存储

每个抓取的页面需要存储页面内容到文件目录，.html ，并在数据库中记录 url、文件路径

![](http://ossp.pengjunjie.com/mweb/15573122965100.jpg)

说明：

每个page代表一个抓取页，需要把每个抓取页存储

url : 记录抓取页面的url链接filename: 存储的文件名，记录在数据库中path:存储的位置，记录在数据库中

#### ielts考试item模块对应

![](http://ossp.pengjunjie.com/mweb/15573119459699.jpg)

item：一篇Reading文章对应一个item, 一篇Listening文章对应一个item \(mp3可能是包含三个的\)，一个Writing对应一个\(分小作为和大作文\)，一个part一个item。

collection\_name：对应每一个模块。例如：听力

![](file://C:/Users/31505/AppData/Roaming/Typora/typora-user-images/1557633067990.png?lastModify=1558253819)

xpath：//\*\[@id="hm\_header"\]/div\[2\]/ul

practice\_name：对应每一篇文章。例如：cork

![](file://C:/Users/31505/AppData/Roaming/Typora/typora-user-images/1557633649307.png?lastModify=1558253819)

xpath：/html/body/div\[1\]/div\[6\]/div\[2\]/div/div/div\[2\]/div\[2\]/div\[2\]/div\[1\]/div/div\[1\]/a\[1\]/div



url: 对应一个collection的url地址，例如`<http://top.zhan.com/ielts/read/cambridge.html>`



article: 阅读、听力的文章记录在这里，这里只记录文章内容，不记录格式

![](file://C:/Users/31505/AppData/Roaming/Typora/typora-user-images/1557633613705.png?lastModify=1558253819)

阅读xpath：/html/body/div\[3\]/div\[1\]/div\[2\]/div\[1\]

听力xpath：/html/body/div\[3\]/div\[1\]/div\[4\]/div\[1\]/div



article\_html:阅读、听力的文章记录在这里，这里记录整体的HTML

![](file://C:/Users/31505/AppData/Roaming/Typora/typora-user-images/1557633779024.png?lastModify=1558253819)

question: 预留，保持为空。现在不好解析。 后面可以尝试这里怎么写

![](file://C:/Users/31505/AppData/Roaming/Typora/typora-user-images/1557633811627.png?lastModify=1558253819)

question\_html: 阅读的问题、听力的问题、写作的题目、口语的题目

阅读xpath：/html/body/div\[3\]/div\[2\]/div/div\[1\]/div/div\[1\]/div\[2\]

听力xpath：//\*\[@id="1505"\]/p\[6\]/span\[2\]

口语xpath：/html/body/div\[4\]/div\[3\]/div\[2\]/div

写作xpath：//\*\[@id="kmyw"\]/div\[1\]/div

![](file://C:/Users/31505/AppData/Roaming/Typora/typora-user-images/1557633826925.png?lastModify=1558253819)

answer:

![](file://C:/Users/31505/AppData/Roaming/Typora/typora-user-images/1557633966090.png?lastModify=1558253819)

阅读xpath：//\*\[@id="4853"\]/p\[4\]/label

听力xpath：





answer\_html: 答案的HTML格式

![](file://C:/Users/31505/AppData/Roaming/Typora/typora-user-images/1557633977254.png?lastModify=1558253819)

title: 阅读文章的标题

![](file://C:/Users/31505/AppData/Roaming/Typora/typora-user-images/1557634018113.png?lastModify=1558253819)

order: 阅读、听力文章的顺序。单篇的存储方式可以使用。

sub\_type: 子类型，写作分为大作文、小作文。口语分为part1,2,3





### 实现流程

* 绑定启动url，start\_url :[https://ieltsonlinetests.com/ielts-exam-library\#academic](https://ieltsonlinetests.com/ielts-exam-library#academic)

* parse中解析每一个item的url链接，获取所有的acdemic的地址。注意这里是相对地址，需要一个base\_url进行绑定。使用yield scrapy.Request\(\)进行转发。如使用parse\_collection![](http://ossp.pengjunjie.com/mweb/15573128217327.jpg)

* parse\_collection解析，分别解析4个链接，每个链接都应该是一个list，是同类型的文章链接。同时分别yield scrapy.Requst到四个不同类型的解析方法中去。比如起名 parse\_reading，parse\_listening,parse\_writing,parse\_speaking。将不同的链接进入不同的解析包

![](http://ossp.pengjunjie.com/mweb/15573129609306.jpg)

* 定义item，在四个解析中分别将解析的内容写入item。

* 在pipeline中存储HTML文件，在讲page和item写入数据库。

这样就能获取ielts\_online上的所有考试内容。

## 扩展功能

* 将阅读的article写成txt，可以方便我们做词频分析



