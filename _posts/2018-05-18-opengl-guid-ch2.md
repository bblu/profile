---
title: OpenGL4.5 Programming Guid V9 - Chapter II
date: 2018-05-18 20:00
categories: blog
tags: [opengl,book]
---
> 变蓝的红宝书--<<OpenGL编程指南>>

## 2.4.数据块接口
### 2.4.1.顶点着色器的主要功能是
- 顶点法线变换及单位化
- 纹理坐标变换
- 光照参数生成

顶点着色器的输入内容包括：
- 着色器源代码
- attribute变量
- uniform变量

顶点着色器的输出内容包括：
- varying变量
- 内置的特殊变量(如gl_Position、gl_FrontFacing、gl_PointSize)

### 2.4.2.片元着色器的主要功能：
- 在差值得到的值上进行操作
- 访问纹理
- 应用纹理
- 雾化
- 颜色融合

片元着色器的输入内容包括：
- 着色器源代码
- 用户自定义的varying变量
- uniform变量
- 采样器（Sampler）
- 一些内置的特殊变量（gl_PointCoord、gl_FragCoord、gl_FrontFacing等）

片元着色器的输出：
- 内置的特殊变量gl_FragColor

## 2.5.着色器编译
准备好着色程序的源代码后使用着色器一般需要依次执行以下步骤：
1. 创建着色器对象：glCreateshader(GLenum type)；
2. 绑定代码到着色器对象上：使用glShaderSource(shaderObj,shaerSrc);
3. 编译着色器：glCompileShader(shaderObj)（用glGetShaderiv检查一下编译的结果）
4. 创建着色程序对象:GLuint glCreaterProgram()；
5. 关联着色器对象到着色程序上：glAttachShader(program, shader)(glDetachShaer)；
6. 链接生成一个可执行程序:glLinkProgram(program)（检查是否成功）；
7. 最后将渲染管道切换到着色器模式并执行着色器程序：glUseProgram()；
> 总结起来就是两句话：两个创建两个绑定，编译链接然后使用。记住后后一句就可以了。

## 2.6.着色器子程序subroutine
> 着色器子程序类似与C函数指针

```glsl
#version 330 core
void func_one(){...}
void func_two(){...}
uniform int flag;
void main(){
    if(flag == 1)
        func_one();
    else
        func_two();
}
```

### 2.6.1.GLSL子程序设置
1. 声明子程序 subroutine returnType subroutineType(type param, ...);
2. 定义子程序 subroutine (subroutineType) returnType functionName(...);
3. 声明选择子 subroutine uniform subroutineType variableName;
```glsl
//1
subroutine vec4 LightFunc(vec3);
//2
subroutine (LightFunc) vec4 ambient(vec3 n){
    return Materials.ambient;
}
subroutin (LightFunc) vec4 diffuse(vec3 n){
    return Materials.diffuse * max(dot(normailize(n),LightVec.xyz), 0.0);
}
//3
subroutine uniform LightFunc materialShader;
```

### 2.6.2.Use subroutine
> 在链接后的着色器程序查询不同subroutine在着色器中的索引（相当于函数地址）,然后把这个函数地址赋给第一步声明的函数指针，完成subroutine的选择。不同的是subroutine的uniform使用glGetSubroutineUniformLocation(pro,type,name)来获取。

```glsl
glUseProgram(program);
//1.定位子程序uniform
matShaderLoc = glGetSubroutineUniformLocation(program, GL_VERTEX_SHADER, "materialShader");
//2.获得子程序索引
ambIndex = glGetSubroutineIndex(program, GL_VERTEX_SHADER, "ambient");
difIndex = glGetSubroutineIndex(program, GL_VERTEX_SHADER, "diffuse");

GLsize n;
glGetIntegerv(GL_MAX_SUBROUTINE_UINFORM_LOCATIONS, &n);
GLuint *indices = new GLuint[n];
indices[matShaderLoc] = ambIndex;
//3.指定要执行的子程序
glUniformSubroutinesuiv(GL_VETEX_SHADER, n, indices);
delete [] indices;
```