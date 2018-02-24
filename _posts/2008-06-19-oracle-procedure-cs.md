---
title: c#调用oracle存储过程返回结果集
date: 2008-06-19 13:49:51
categories: database
tags: [procedure,oracle]
--- 

首先在ORACLE建立PACKAGE和PACKAGE BODY，将在这里面定义函数和存储过程返回结果集。 

## 数据库

### 1：建立PACKAGE： 
CREATE OR REPLACE package SCOTT.pk_wt 
is 
type mytype is ref cursor; 
procedure p_wt(mycs out mytype); 
function f_get(str in varchar2) 
return varchar2; 
end; 

/ 说明：其实PACKAGE只是个声明罢了。我们在这里定义了一个存储过程返回结集和一个函数，返回字符串。 

### 2：建立PACKAGE BODY： 

CREATE OR REPLACE package BODY SCOTT.pk_wt   
is   
procedure p_wt(mycs out mytype)   
is  
begin  
open mycs for select * from test;  
end p_wt; 

function f_get(str varchar2) 
return varchar2 
is 
str_temp varchar2(100) := 'good luck!'; 
begin 
str_temp := str_temp || str; 
return str_temp; 
end f_get; 

end pk_wt; 

/ 说明：这里建立PACKAGE BODY是具体的说明和使用，将采用什么方式实现。。 

## C#段： 

在C#中代码将分为两部分，一部分是使用函数，另外一部分是使用结果集。 
定义一个连接，从WEBCONFIG里去取得： 

private OracleConnection orcn=new OracleConnection(System.Configuration.ConfigurationSettings.AppSettings["scott"]); 
C#调用ORACLE函数： 

OracleCommand cmd=new OracleCommand("pk_wt.f_get",orcn); 
cmd.CommandType=CommandType.StoredProcedure; 
OracleParameter p1=new OracleParameter("str",OracleType.VarChar,10); 
p1.Direction=System.Data.ParameterDirection.Input; 
p1.Value=this.TextBox1.Text; 
OracleParameter p2=new OracleParameter("result",OracleType.VarChar,100); 
p2.Direction=System.Data.ParameterDirection.ReturnValue; 
cmd.Parameters.Add(p1); 
cmd.Parameters.Add(p2); 
orcn.Open(); 
cmd.ExecuteNonQuery(); 
orcn.Close(); 
this.Button_function.Text=p2.Value.ToString();   
其中RESULT是系统自定义的函数返回变量，特别要注意的是，函数的参数的返回类型要指定，另外就是COMMAND类型也需要指定，另外和一般的存储过程没什么差别。 

C#调用ORACLE返回结果集：   
OracleCommand cmd=new OracleCommand("pk_wt.p_wt",orcn); 
cmd.CommandType=CommandType.StoredProcedure; 
OracleParameter p1=new OracleParameter("mycs",OracleType.Cursor); 
p1.Direction=System.Data.ParameterDirection.Output;   
cmd.Parameters.Add(p1); 
OracleDataAdapter da=new OracleDataAdapter(cmd);   
DataSet ds=new DataSet(); 
da.Fill(ds,"test"); 
this.DataGrid1.DataSource=ds; 
this.DataGrid1.DataBind();   

这里没什么可说的类。。只是定义的数据类型是游标，类型是OUTPUT，另外没什么了。。