1.  Const 改到 build_sym .cpp 中的 VaclDecl 节点处理，向 Variable 类增加 isConst 类属性，变量符号新增是否为 Const。AST 节点新增 Const 属性

## 修改数组初始化，采用 Map 方法实现复杂嵌套数组初始化

```C++
typedef util::TreeMap<int, util::List<int>> ConstInitialMap; // map of const initial

typedef util::TreeMap<int, ExprList> InitialMap; // map of initial
```

删除 DimList 和修改 IntList ，统一在构建符号表时处理成线性展开式

AST 节点增加下面几个属性

```C++
      ExprList *dim; // for array declartion

      DimList *dim_processed; // for array declaration 数组维度列表

      InitialMap *arr_init; // for array initialization 数组初始化

      DimList *arr_init_processed; // 数组初始化线性展开
```

## ParamList 也修改了

