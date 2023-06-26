# step 5: 局部变量和赋值

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

# step 6: if 语句和表达式


|节点|成员|含义|
|---|---|---|
|`IfStmt`|分支条件 `condition`，真分支 `true_brch`，假分支 `false_brch`|if 分支语句|
|`IfExpr`|分支条件 `condition`，真分支 `true_brch`，假分支 `false_brch`|if 条件表达式|

## 悬吊 Else 问题

*因为还没有引入大括号 `{}` 控制，所以存在这一问题*

```
if(a) if(b) c=0; else b=0;
/*两种理解*/
-> if(a) {if(b) c=0}; else b=0;
-> if(a) {if(b) c=0; else b=0;}
```

就近匹配原则：==`else` 和最近的 `if` 结合==，实现方法：

- 设置产生式的优先级，优先选择没有 else 的 if。

> bison 默认在 shift-reduce conflict 的时候选择 shift，从而对悬挂 else 进行就近匹配。

代码不需要修改

## 语义分析

扫描到 if 语句和条件表达式时递归地访问其子结点、 if 语句**不总是**有 else 分支，所以在递归到子结点时，请先判断子结点是否存在。

在实现中，通过设置一个空语句 AST 节点——EmptyStmt 来代替 NULL，方便使用。其实框架中已经写好了

build_sym. Cpp 没有需要改的

type_check.cpp 中，需要对于增加对于IfExpr 类型检查，要求真假表达式的类型都相同。

## IR 生成

IfStmt 节点的 IR 生成 visitor 已经在框架中实现，需要实现 IfExpr 节点的 IR 生成。注意，IfExpr 是有返回值的，因此需要在生成 IR 时需要绑定一些虚拟寄存器

```c++
/* Translating an ast::IfExpr node.

 */

void Translation::visit(ast::IfExpr *e) {
    // IR生成逻辑是线性的
    Label L1 = tr->getNewLabel(); // entry of the false branch
    Label L2 = tr->getNewLabel(); // exit
    e->condition->accept(this);
    e->ATTR(val) = tr->getNewTempI4();

    tr->genJumpOnZero(L1, e->condition->ATTR(val));

    e->true_brch->accept(this);

    tr->genAssign(e->ATTR(val), e->true_brch->ATTR(val));

    tr->genJump(L2); // done 无条件跳转指令
    tr->genMarkLabel(L1);
    
    e->false_brch->accept(this);
    
    tr->genAssign(e->ATTR(val), e->false_brch->ATTR(val));
	tr->genMarkLabel(L2);

}
```

## 目标代码生成

都已实现，无需修改
