---
title: ios开发TimePie第4天-数据入Sqlite库
date: 2018-03-18
categories: blog
tags: [ios,swift]
---

>  一个程序最有价值的就是数据了，todo是一个高度结构化的数据放到sqlite是最合适不过的了，以后往android上移植也很方便，搜索了几个包装Sqlite的库都比较恐怖比如Sqlite.swift，介于这个应用的数据逻辑很简单就自己封装了一下sqlite的操作。

### 引用依赖库
在项目 General/Linked Frameworks and Libraries/+ 添加libSqlite3.tbd

> 如果想让sqlite3库随版本更新可以考虑使用libsqlite3.0.tbd

### 常用的操作
- 注释： Command + / 

### 封装插入方法
> 二进制数据你去bind我还能理解，感觉sqlite没那么麻烦int和string竟然还需要bind，应该能修改的第一个版本先这样吧。

``` bash
    func insert(item:TodoItem, start:Int, span:Int){
        var stmt: OpaquePointer?
        
        let queryString = "INSERT INTO Done (code, name, start,end,span) VALUES (?,?,?,strftime('%s','now'),?)"
        
        if sqlite3_prepare(db, queryString, -1, &stmt, nil) != SQLITE_OK{
            let errmsg = String(cString: sqlite3_errmsg(db)!)
            print("error preparing insert: \(errmsg)")
            return
        }
        
        if sqlite3_bind_int(stmt, 1, Int32(item.code)) != SQLITE_OK{
            let errmsg = String(cString: sqlite3_errmsg(db)!)
            print("failure binding code: \(errmsg)")
            return
        }
        
        if sqlite3_bind_text(stmt, 2, item.name, -1, nil) != SQLITE_OK{
            let errmsg = String(cString: sqlite3_errmsg(db)!)
            print("failure binding name: \(errmsg)")
            return
        }
        if sqlite3_bind_int(stmt, 3, Int32(start)) != SQLITE_OK{
            let errmsg = String(cString: sqlite3_errmsg(db)!)
            print("failure binding start: \(errmsg)")
            return
        }
        
        if sqlite3_bind_int(stmt, 4, Int32(span)) != SQLITE_OK{
            let errmsg = String(cString: sqlite3_errmsg(db)!)
            print("failure binding span: \(errmsg)")
            return
        }
        
        if sqlite3_step(stmt) != SQLITE_DONE {
            let errmsg = String(cString: sqlite3_errmsg(db)!)
            print("failure inserting todo: \(errmsg)")
            return
        }
        print("itme saved successfully")
    }
```

view code:【[sqliteutil.swift](https://github.com/bblu/timepie/blob/e819a589dad15a79648b0375ff94665cb68a8cd4/TimePie/SqliteUtil.swift)】