---
title: Code Measurement & Analysis for SVN
date: 2013-01-26
categories: blog
tags: [devops,shell,python]
---
> 对svn管理代码的所有项目的代码提交情况进行分析，采集每个产品，每个开发者，每个任务的代码变动情况提交到mysql数据库，信息提取有python脚本实现。
扩展了禅道的统计功能可以按照产品项目，开发者度量代码量和需求的开发进度，测试用例通过后会自动更新需求的状态。
通过hudson（jenkins）每日夜里调用更新从上一次统计的代码的提交情况。

this repo is a set of shell scripts for c++ code analyzing;

## codeStat
代码分析脚本，需要注意的问题就是像更新名字空间，大批量的改变量和接口的名字的提交要过滤掉。

## hooks
svn钩子保证提交日志的格式正确，以区分本次提交代码是某个需求的任务还是解决了一个bug。

view code: [github](https://github.com/bblu/pma/)
