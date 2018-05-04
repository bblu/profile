---
title: 《Go Web编程》-类型和控制
date: 2018-04-24 00:10
categories: blog
tags: [go]
---

> go语言和C很像，像到是有指针甚至到了有函数指针和goto的地步。符号首字母的大小写决定了符号的可见性（比Fortran有进步😇），for和c相比有变强，为什么多行字符串是`multiLinestring`呢。。。

## 定义变量和常量

###  var 变量 类型 [= 值]
```go
 var isABug bool (默认false)
 var Name, addr type
 var name, Addr type = "bblu", "china"
 Name, addr := "bblu", "china"
 Valuef32 := float32(3.14)
 primes := [4]int{0, 1, 2, 3}
 Arrays := [...]int{1,4,6,9,12,45}
 arra10 := [10]int{0,1,2}   //部分初始化
 var silce0 = Arrays[2:5]   //[前开后闭)
 var slice1,slice2 []byte
 var dict map[string] int
 dict := make(map[string] int)
 dict["one"] = 1
 rating := map[rune]string{0:"red",1:"green"}
 _, b : = 0, 1
 c := a + b 
 
 type Person struct{
     name string
     addr string
     age int
 }
 type Programmer struct{
     Person     //匿名字段可以继承或者组合
     language string
 }
 bar p1 person{"bb", "cn", "35"}
```
### const 常量 = 值
```go
const PI = 3.14159265358979323846264338
const Prefix = "cn_bj"
const greeting = \`multi   
                line string!\`
```
## 内置类型
- 布尔：bool[false, true]
- 整形 [int8, int16, rune=int32, int64]
- 无符 [byte=uint8, uint16, uint32, uint64]
- 浮点 [float32, float64]
- 复数 [complex64, complex128]
- 字符 [string]
- 错误 [error]
- 数组 [array, slice]
- 字典 [map]需要make初始化
- 组合 [struct]
- 函数 [func， method]
- 接口 [interface]


### 操作技巧

#### 字符串转可编辑的数组 
    c := []byte("hello")

#### 分组声明
```go
"大写开头的符号可以导出，小写符号是私有的"
    import{
        "fmt"
        "os"
    }
    const{
        Pi = 3.14
        Db = "pg"
    }
    var{
        i int
        f float32
        s string
    }
```

#### iota枚举默认从0开始
```go
"同一组常量声明省略类型则和前面一致"
cosnt{
    r = iota    // r = 0
    g = iota    // g = 1
    b = iota    // b = 2
    a           // a = 3
}

```

#### for
```go
for i < 10{
    i += 1
}

for i:=0; i < sum; i++{
    amount += arr[i]
}

for k,v := range dict{
    fmt.Println("dict[", k, "]=", v)
}

```

#### func 返回值的声明可以省略
```go
func add(input1 int32,input2 int32) int{
    return input1 + input2
}

func subAdd(a int32,b int32) (sub int, add int){
    return a-b, a+b
}

func getIndicesByTarget(nums []int, leng int, tar int)(i0 int,i1 int){
	for i:=0; i< leng-1; i++{
	}
	return -1, -1
}
```
#### defer栈 延迟执行一般用了确保资源被释放

#### Panic & Recover
先不管这些用不到的东西

#### main & init
main 入口函数（这点上C编译时候是可以-nostartfiles [-nostdlib]然后-e指定自定义的入口函数）

#### make 和 new
- make T 给内建类型分配内存，返回一个有初始值的T类型
- new(T) 分配了零值填充的T类型的内存空间，返回地址