---
title: nodejs工作计划管理
date: 2017-04-22
categories: web
tags: [express,mongodb]
---
express采用handlebars渲染，附加数据库设计。

## 后台服务

代码还没完成选了用户模块把相同功能的后台流程走通了。

## 数据库设计

### plan modle  
``` bash
 plan
  |_id:objectId
  |-name:string
  |-start: date, 计划起效日期
  |-end: date, 计划实效日
  |-schedule: string, 30 9 * * * 1
  |-span: int
  |-executor: array, stuffIds
  |-inspector: string, stuffId
  |-type: string[region|device]
  |-range: array, polygon geometry或者设备类型ID
  |-content: array,设备ID
  |-enable: boolean，是否启用
  |-deleted: boolean
```

### check standard modle
    check
      |_id: objectId
      |-type：string,[standard]
      |-device:int, 设备类型
      |-name: string,检查项名称
      |-content: string, 检查内容／标准

### task modle
    task
     |_id: objectId, taskId
     |-name: string,planName
     |-start: datetime,实际执行时间
     |-end: datetime
     |-executor: array
     |-inspector: string
     |-type:string,[task]
     |-content: int, 设备数量
     |-enable: boolean,是否显示
     |-deleted: boolean

### device check modle
``` bash
device check
 |_id: objectId，checkId
 |-name: string, 设备名称
 |-taskId: objectId
 |-type: string,[check]
 |-check: string, 检查项名称
 |-content: string, 检查结果
 |-start: datetime,实际执行时间
 |-end: datetime
```

### attachment modle  
``` bash
attachment 
  |_id: objectId
  |-checkId: objectId
  |-type:string, [attachment]
  |-format:strig, [jpg|mp4|png|...]
  |-content: buffer,附件内容
```
      
