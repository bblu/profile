---
title: php网络小爬虫
date: 2015-08-16
categories: hobby
tags: [spider, php, mysql]
---

在网站上抓取基金信息存储到本地到mysql数据库，配合php页面的收支记录完成了家庭资产收支管理的个人站点。
招行和工行的收支记录都是可以导出到本地倒入到数据库中，不用一条一条人肉输入。

### php框架
框架用的春哥的【[zantao](http://www.zentao.net/)】,禅道也是我多年用的敏捷工具非常好用，也方便自己修改，话说我的禅道首页改的还是挺狠的。

### 链接数据库

``` bash
    $con = mysql_connect("localhost","dbuser","passwd");
```

### 筛选有效信息

``` bash
    $div = $html->find('h3 big'); 
	$value = (float)$div[0]->innertext;
```

More code: [github](https://github.com/bblu/step2php/blob/master/getJijin/getInfo.php)
