# 局部变量和赋值

- 变量的声明
- 变量的使用（读取/赋值）

为了加入变量，我们需要确定：变量存放在哪里、如何访问。 为此，我们会引入 **栈帧** 的概念，并介绍它的布局。

## 语法分析 Parser

新增如下语法

```Yacc
function // function体
    : type Identifier '(' ')' '{' statement* '}'

statement
    : 'return' expression ';'

    | expression? ';'
    | declaration

declaration
    : type Identifier ('=' expression)? ';'

expression
    : assignment

assignment
    : logical_or
    | Identifier '=' expression

primary
    : Integer
    | '(' expression ')'

    | Identifier
```

参考实现：

1. 添加 VarDeclStmt 语法正则式，但是生成 VarDecl 节点，所以应该不需要改动太多，对于数组，之后会加
2.  ~~对于赋值运算，添加 AssignMent 语法正则式，需要创建新的节点，AssignMent 节点~~，SysY 只支持单个变量或者数组元素赋值，没必要，直接扔到 Expr 就行，但是若支持数组还得增加左值节点，之后再说吧
3. 现在前端 IR 翻译啥的只需检查一下，看有啥需要增加的没有，看看 TAC 和 ASM 目录。

## 语义分析

增加 ValDecl 的 translation IR 相关。