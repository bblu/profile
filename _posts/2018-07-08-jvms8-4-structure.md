---
title: 《The JVM Specification》- ch4 The class File Format
date: 2018-07-08 17:30
categories: blog
tags: [jvm, java]
---
there is nothing but a map for class file format

![class file format](../assets/in-post/2018-07-08-jvms8-4-structure.svg)

#### an example
```java
public class Main {

    public static void main(String[] args) {
	// write your code here
        int a = 1;
        int b = 2;
        int c = a + b;
    }
}
```

#### to class file
```sh
macbook:src bblu$ javac Main.java 
macbook:src bblu$ ls
Main.class      Main.java       cn
macbook:src bblu$ javap -v Main.class 
Classfile /Users/bblu/IdeaProjects/Test/src/Main.class
  Last modified Jul 3, 2018; size 273 bytes
  MD5 checksum a3ecac22d2e8975b9bf21afa4ff0502b
  Compiled from "Main.java"
public class Main
  minor version: 0
  major version: 52
  flags: ACC_PUBLIC, ACC_SUPER
Constant pool:
   #1 = Methodref          #3.#12         // java/lang/Object."<init>":()V
   #2 = Class              #13            // Main
   #3 = Class              #14            // java/lang/Object
   #4 = Utf8               <init>
   #5 = Utf8               ()V
   #6 = Utf8               Code
   #7 = Utf8               LineNumberTable
   #8 = Utf8               main
   #9 = Utf8               ([Ljava/lang/String;)V
  #10 = Utf8               SourceFile
  #11 = Utf8               Main.java
  #12 = NameAndType        #4:#5          // "<init>":()V
  #13 = Utf8               Main
  #14 = Utf8               java/lang/Object
{
  public Main();
    descriptor: ()V
    flags: ACC_PUBLIC
    Code:
      stack=1, locals=1, args_size=1
         0: aload_0
         1: invokespecial #1                  // Method java/lang/Object."<init>":()V
         4: return
      LineNumberTable:
        line 1: 0

  public static void main(java.lang.String[]);
    descriptor: ([Ljava/lang/String;)V
    flags: ACC_PUBLIC, ACC_STATIC
    Code:
      stack=2, locals=4, args_size=1
         0: iconst_1
         1: istore_1
         2: iconst_2
         3: istore_2
         4: iload_1
         5: iload_2
         6: iadd
         7: istore_3
         8: return
      LineNumberTable:
        line 5: 0
        line 6: 2
        line 7: 4
        line 8: 8
}
SourceFile: "Main.java"

```
