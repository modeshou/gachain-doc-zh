编译器和虚拟机
###############

本节涉及程序编译和虚拟机中 |galangres| 语言的操作。

源代码存储和编译
===================================

合约和功能用Galang语言编写，并存储在生态系统的合约表。

执行合约时，将从数据库中读取其源代码并将其编译为字节码。

合约更改后，其源代码将更新并保存在数据库中。然后编译该源代码，导致相应的虚拟机字节码也被改变。

字节码在任何地方都没有物理保存，因此当再次执行程序时，会重新编译源代码。

所有生态系统的合约表中描述的整个源代码都严格按顺序编译到一个虚拟机中，虚拟机的状态在所有节点上都相同。

调用合约时，虚拟机不会以任何方式更改其状态。执行任何合约或调用函数都发生在每个外部调用时创建的单独运行堆栈上。

每个生态系统都可以拥有一个所谓的虚拟生态系统，可以在一个节点内与区块链外的数据表一起使用，并且不能直接影响区块链或其他虚拟生态系统。在这种情况下，托管这样虚拟生态系统的节点会编译其合约并创建自己的虚拟机。

虚拟机结构
==========================


VM结构
------------

虚拟机按如下结构定义在内存中。

.. code:: 

    type VM struct {
        Block
        ExtCost       func(string) int64
        FuncCallsDB   map[string]struct{}
        Extern        bool
        ShiftContract int64
        logger        *log.Entry
    }

VM结构具有以下元素：

  * **Block** - 包含一个 :ref:`块结构 <Blocks structure>`；

  * **ExtCost** - 一个函数，该函数返回执行外部golang函数的费用；

  * **FuncCallsDB** - golang函数名称集合，该函数名返回执行成本作为第一个参数。这些函数使用 **EXPLAIN** 计算处理数据库的成本；

  * **Extern** - 一个表示合约是否为外部合约的布尔标识，创建VM时，它设置为true，编译代码时不需要显示调用的合约。也就是说，它允许调用将来确定的合约代码；

  * **ShiftContract** VM中第一个合约的ID；

  * **logger** VM的错误日志输出。

.. _Blocks structure:

块结构
----------------

虚拟机是由 **块Block** 对象类型组成的树。

块是包含一些字节码的独立单元。简单地说，您在语言的大括号(``{}``)中放入的所有内容都是一个块。

例如，下面的代码创建一个带有函数的块。该块又包含一个带有 *if* 语句的块，该语句又包含一个带有 *while* 语句的块。

.. code:: 

    func my() {
         if true {
              while false {
                   ...
               }
         }
    } 

块按如下结构定义在内存中。

.. code:: 

    type Block struct {
        Objects  map[string]*ObjInfo
        Type     int
        Owner    *OwnerInfo
        Info     interface{}
        Parent   *Block
        Vars     []reflect.Type
        Code     ByteCodes
        Children Blocks
    }

块结构具有以下元素：

  * **Objects** - 一个 :ref:`ObjInfo <ObjInfo structure>` 指针类型的内部对象的映射。例如，如果块中有一个变量，那么可以通过它的名称获得关于它的信息；

  * **Type** - 块的类型。块为函数时，类型为 **ObjFunc**。块为合约时，类型为 **ObjContract**；

  * **Owner** - 一个 **OwnerInfo** 指针类型的结构。该结构包含有关已编译合约所有者的信息。它在合同编译期间指定或从 **contracts** 表中获取；

  * **Info** - 包含有关对象的信息，这取决于块类型；

  * **Parent** - 指向父块的指针；

  * **Vars** - 一个包含当前块变量类型的数组；

  * **Code** - 块本身字节码，当控制权传递给该块时会执行该块字节码，例如，函数调用或者循环体；

  * **Children** - 一个包含子块的数组，例如，函数嵌套、循环、条件操作符。

.. _ObjInfo structure:

ObjInfo结构
-----------------

**ObjInfo** 结构包含有关内部对象的信息。

.. code:: 

    type ObjInfo struct {
       Type int
       Value interface{}
    }

ObjInfo结构具有以下元素：

  * **Type** 是对象类型。它可以是以下值之一：

    * **ObjContract** – :ref:`合约 <ContractInfo structure>`；
    * **ObjFunc** – 函数；
    * **ObjExtFunc** – 外部golang函数；
    * **ObjVar** – 变量；
    * **ObjExtend** – $name 变量。

  * **Value** – 包含每种类型的结构。

.. _ContractInfo structure:

ContractInfo结构
""""""""""""""""""""""

指向 **ObjContract** 类型，**Value** 字段包含 **ContractInfo** 结构。

.. code:: 

    type ContractInfo struct {
        ID uint32
        Name string
        Owner *OwnerInfo
        Used map[string]bool
        Tx *[]*FieldInfo
    }

ContractInfo结构具有以下元素：

  * **ID** – 合约ID。调用合约时，该值在区块链中显示；
  
  * **Name** – 合约名称；

  * **Owner** – 关于合约的其他信息；
  
  * **Used** – 已被调用的合约名称的映射；

  * **Tx** – 合约 :ref:`数据部分 <datasection>` 描述的数据数组。

FieldInfo结构
^^^^^^^^^^^^^^^^^^^

FieldInfo结构用于 **ContractInfo** 结构并描述合约 :ref:`数据部分 <datasection>` 的元素。

.. code::

    type FieldInfo struct {
          Name string
          Type reflect.Type
          Original uint32
          Tags string
    }

FieldInfo结构具有以下元素：

  * **Name** - 字段名称；

  * **Type** - 字段类型；

  * **Original** - 可选项字段；

  * **Tags** – 该字段的附加标签。

.. _FuncInfo structure:

FuncInfo结构
""""""""""""""""""

指向 **ObjFunc** 类型，**Value** 字段包含 **FuncInfo** 结构。

.. code:: 

    type FuncInfo struct {
        Params []reflect.Type
        Results []reflect.Type
        Names *map[string]FuncName
        Variadic bool
        ID uint32
    }

FuncInfo结构具有以下元素：

  * **Params** – 参数类型数组；

  * **Results** – 返回结果类型数组；

  * **Names** – 尾部函数的数据映射，例如，``DBFind().Columns ()``；

  * **Variadic** – 如果函数可以具有可变数量的参数，则为true；

  * **ID** – 函数ID。


FuncName结构
^^^^^^^^^^^^^^^^^^

FuncName结构用于 **FuncInfo** 并描述尾部函数的数据。

.. code:: 

    type FuncName struct {
       Params []reflect.Type
       Offset []int
       Variadic bool
    }

FuncName结构具有以下元素：

  * **Params** – 参数类型数组；

  * **Offset** – 这些变量的偏移量数组。实际上，所有参数在函数中都可以使用点 ``.`` 来初始化值；

  * **Variadic** – 如果尾部函数可以具有可变数量的参数。则为true。

ExtFuncInfo结构
"""""""""""""""""""""

指向 **ObjExtFunc** 类型，**Value** 字段包含 **ExtFuncInfo** 结构。用于描述golang函数。

.. code:: 

    type ExtFuncInfo struct {
       Name string
       Params []reflect.Type
       Results []reflect.Type
       Auto []string
       Variadic bool
       Func interface{}
    }

ExtFuncInfo结构具有以下元素：

  * **Name**、**Params**、**Results** 参数和 :ref:`FuncInfo <FuncInfo structure>` 结构相同；

  * **Auto** – 一个变量数组，如果有，则作为附加参数传递给函数，例如，*SmartContract* 类型的变量 *sc*；

  * **Func** – golang函数。


VarInfo结构
"""""""""""""""""

指向 **ObjVar** 类型，**Value** 字段包含一个 **VarInfo** 结构。

.. code:: 

    type VarInfo struct {
       Obj *ObjInfo
       Owner *Block
    }

VarInfo结构具有以下元素：

  * **Obj** – 关于变量类型和变量值的信息；

  * **Owner** – 指向所属块的指针。


ObjExtend值
"""""""""""""""""""

指向 **ObjExtend** 类型，**Value** 字段包含一个字符串，其中包含变量或函数的名称。



虚拟机指令
========================

ByteCode结构
------------------

字节码是 **ByteCode** 类型结构的序列。

.. code:: 

    type ByteCode struct {
       Cmd uint16
       Value interface{}
    }

该结构具有以下字段：

  * **Cmd** - 存储指令的标识符；

  * **Value** - 包含操作数（值）。

通常情况，指令对堆栈的顶部元素执行操作，并在必要时将结果值写入其中。

指令标识符
-------------------

*packages/script/cmds_list.go* 文件描述了虚拟机指令的标识符。

  * **cmdPush** – 将 *Value* 字段的值放到堆栈。例如，将数字和行放入堆栈；

  * **cmdVar** – 将变量的值放入堆栈。*Value* 包含一个指向 *VarInfo* 结构的指针以及关于该变量的信息；

  * **cmdExtend** – 将外部变量的值放入堆栈。*Value* 包含一个带有变量名称的字符串（以 ``$`` 开头）；

  * **cmdCallExtend** – 调用外部函数（名称以 ``$`` 开头）。函数的参数从堆栈中获取，函数的结果被放入堆栈。*Value* 包含一个函数名称（以 ``$`` 开头）；

  * **cmdPushStr** – 将 *Value* 中的字符串放入堆栈；

  * **cmdCall** – 调用虚拟机函数，*Value* 包含 **ObjInfo** 结构。该指令适用于 **ObjExtFunc** golang函数和 **ObjFunc** |galangres| 函数。调用函数时，将从堆栈中获取其参数，并将结果值放入堆栈；

  * **cmdCallVari** – 类似于 **cmdCall** 指令，调用虚拟机函数。该指令用于调用具有可变数量参数的函数；

  * **cmdReturn** – 用于退出函数，返回值将放入到堆栈，不使用 *Value* 字段；

  * **cmdIf** – 将控制权转移到 **块** 结构中的字节码，该指令在 *Value* 字段中传递。仅当 *valueToBool* 函数调用堆栈顶部元素返回 ``true`` 时才会将控制权转移到堆栈。否则控制权转移到下一个指令；

  * **cmdElse** – 该指令的工作方式与 **cmdIf** 指令相同，但仅当 *valueToBool* 函数调用堆栈顶部元素返回 ``false`` 时控制权才会转移到指定的块；

  * **cmdAssignVar** – 从 *Value* 获取 **VarInfo** 类型的变量列表。这些变量使用 **cmdAssign** 指令获取值；

  * **cmdAssign** – 将堆栈中的值赋给 **cmdAssignVar** 指令获得的变量；

  * **cmdLabel** – 控制权在while循环期间被返回时定义一个标记；

  * **cmdContinue** – 该指令将控制权传递给 **cmdLabel** 标记。执行循环的新迭代时，不使用 *Value* ；

  * **cmdWhile** – 使用 *valueToBool* 检查堆栈的顶部元素。如果该值为 ``true``，则从 *value* 字段调用 **块** 结构；

  * **cmdBreak** – 退出循环；

  * **cmdIndex** – 通过索引将 *map* 或 *array* 中的值放入堆栈，不使用 *Value*。例如，``(map | array) (index value) => (map | array [index value])``；

  * **cmdSetIndex** – 将堆栈顶部元素的值分配给 *map* 或 *array* 的元素，不使用 *Value*。例如， ``(map | array) (index value) (value) => (map | array)``；

  * **cmdFuncName** – 添加的参数通过用点 ``.`` 划分顺序来描述。例如，``func name => Func (...) .Name (...)``；

  * **cmdUnwrapArr** – 如果堆栈顶部元素为数组，则定义一个布尔标记；

  * **cmdMapInit** – 初始化 *map* 的值；

  * **cmdArrayInit** – 初始化 *array* 的值；

  * **cmdError** – 当合约或者函数以某个指定的 ``error, warning, info`` 错误终止时，该指令创建。


堆栈操作指令
--------------------

.. note::

    在当前版本中，这些指令是不完全的自动类型转换。例如， ``string + float | int | decimal => float | int | decimal``，``float + int | str => float``，但是 ``int + string => runtime error``。

下面是直接处理堆栈的指令。这些指令中不使用 *Value* 字段。

* **cmdNot** – 逻辑否定。``(val) => (!ValueToBool(val))``；

* **cmdSign** – 符号变化。``(val) => (-val)``；

* **cmdAdd** – 加法。``(val1)(val2) => (val1 + val2)``；

* **cmdSub** – 减法。``(val1)(val2) => (val1 - val2)``；

* **cmdMul** – 乘法。``(val1)(val2) => (val1 * val2)``；

* **cmdDiv** – 除法。``(val1)(val2) => (val1 / val2)``；

* **cmdAnd** – 逻辑与。``(val1)(val2) => (valueToBool(val1) && valueToBool(val2))``；

* **cmdOr** – 逻辑或。 ``(val1)(val2) => (valueToBool(val1) || valueToBool(val2))``；

* **cmdEqual** – 等式比较，返回bool。``(val1)(val2) => (val1 == val2)``；

* **cmdNotEq** – 不等式比较，返回bool。``(val1)(val2) => (val1 != val2)``；

* **cmdLess** – 小于式比较，返回bool。``(val1)(val2) => (val1 < val2)``；

* **cmdNotLess** – 大于等于式比较，返回bool。``(val1)(val2) => (val1 >= val2)``；

* **cmdGreat** – 大于式比较，返回bool。``(val1)(val2) => (val1 > val2)``；

* **cmdNotGreat** – 小于等于式比较，返回bool。``(val1)(val2) => (val1 <= val2)``。


Runtime结构
-----------------

执行字节码不会影响虚拟机。例如，它允许在单个虚拟机中同时运行各种函数和合约。**Runtime** 结构用于运行函数和合约，以及任何表达式和字节码。

.. code:: 

    type RunTime struct {
       stack []interface{}
       blocks []*blockStack
       vars []interface{}
       extend *map[string]interface{}
       vm *VM
       cost int64
       err error
    }

* **stack** – 执行字节码的堆栈；

* **blocks** – 块调用堆栈；

* **vars** – 变量堆栈。在块中调用字节码时，其变量将添加到该变量堆栈中。退出块后，变量堆栈的大小将返回到先前的值；

* **extend** – 指向外部变量值（``$name``）映射指针；

* **vm** – 虚拟机指针；

* **cost** – 执行结果的燃料单位；

* **err** – 执行时的错误。


blockStack结构
""""""""""""""""""""

blockStack结构用于 **Runtime** 结构。

.. code:: 

    type blockStack struct {
         Block *Block
         Offset int
    }


* **Block** – 正在执行的块的指针；

* **Offset** – 在指定块的字节码中执行的最后一个指令的偏移量。


RunCode函数
----------------

字节码在 **RunCode** 函数中执行。它包含一个循环，为每个字节码指令执行相应的操作。在处理字节码之前，必须初始化必要的数据。

在这里新块被添加到其他块中。

.. code:: 

    rt.blocks = append(rt.blocks, &blockStack{block, len(rt.vars)})

接下来，获得尾部函数的相关参数信息。这些参数包含在堆栈的最后一个元素中。

.. code:: 

    var namemap map[string][]interface{}
    if block.Type == ObjFunc && block.Info.(*FuncInfo).Names != nil {
        if rt.stack[len(rt.stack)-1] != nil {
            namemap = rt.stack[len(rt.stack)-1].(map[string][]interface{})
        }
        rt.stack = rt.stack[:len(rt.stack)-1]
    }

然后，必须使用初始值初始化当前块中定义的所有变量。

.. code:: 

   start := len(rt.stack)
   varoff := len(rt.vars)
   for vkey, vpar := range block.Vars {
      rt.cost--
      var value interface{}
      
由于函数中的变量也是变量，所以我们需要按照函数本身所描述的顺序从堆栈的最后一个元素中取出它们。

.. code::

    if block.Type == ObjFunc && vkey < len(block.Info.(*FuncInfo).Params) {
      value = rt.stack[start-len(block.Info.(*FuncInfo).Params)+vkey]
    } else {

在此使用初始值初始化局部变量。

.. code:: 

        value = reflect.New(vpar).Elem().Interface()
        if vpar == reflect.TypeOf(map[string]interface{}{}) {
           value = make(map[string]interface{})
        } else if vpar == reflect.TypeOf([]interface{}{}) {
           value = make([]interface{}, 0, len(rt.vars)+1)
        }
     }
     rt.vars = append(rt.vars, value)
   }
   
接下来，更新在尾部函数中传递的变量参数的值。

.. code:: 

   if namemap != nil {
     for key, item := range namemap {
       params := (*block.Info.(*FuncInfo).Names)[key]
       for i, value := range item {
          if params.Variadic && i >= len(params.Params)-1 {
          
如果传递的变量参数为可变数量的参数，那么将它们组合成一个变量数组。

.. code:: 

                 off := varoff + params.Offset[len(params.Params)-1]
                 rt.vars[off] = append(rt.vars[off].([]interface{}), value)
             } else {
                 rt.vars[varoff+params.Offset[i]] = value
           }
        }
      }
   }
   
之后，我们要做的就是删除作为函数参数从堆栈顶部传递的值，从而移动堆栈。我们已经将它们的值复制到一个变量数组中。

.. code:: 

    if block.Type == ObjFunc {
         start -= len(block.Info.(*FuncInfo).Params)
    }
    
字节码指令循环执行结束后，我们必须正确地清除堆栈。

.. code:: 

    last := rt.blocks[len(rt.blocks)-1]
    
将当前块从块堆栈中删除。

.. code:: 

    rt.blocks = rt.blocks[:len(rt.blocks)-1]
    if status == statusReturn {

如果成功退出已执行的函数，我们将返回值添加到上一个堆栈的尾部。

.. code:: 

   if last.Block.Type == ObjFunc {
      for count := len(last.Block.Info.(*FuncInfo).Results); count > 0; count-- {
        rt.stack[start] = rt.stack[len(rt.stack)-count]
        start++
      }
      status = statusNormal
    } else {

如您所见，如果我们不执行函数，那么我们就不会恢复堆栈状态并按原样退出函数。原因是函数中已经执行的循环和条件结构也是字节码块。

.. code:: 

        return
      }
    }
    rt.stack = rt.stack[:start]


VM的其他函数操作
-----------------------------------

使用 **NewVM** 函数创建虚拟机。每个虚拟机都 ****Extend**** 函数添加了四个函数：**ExecContract**、**MemoryUsage**、**CallContract** 和 **Settings**。

.. code:: 

   for key, item := range ext.Objects {
       fobj := reflect.ValueOf(item).Type()

我们遍历所有传递的对象，只查看函数。

.. code:: 

   switch fobj.Kind() {
   case reflect.Func:
   
根据接收到的相关该函数的信息填充 **ExtFuncInfo** 结构，并按名称将其结构添加到顶层的 **Objects** 映射。

.. code:: 

  data := ExtFuncInfo{key, make([]reflect.Type, fobj.NumIn()), make([]reflect.Type, fobj.NumOut()), 
     make([]string, fobj.NumIn()), fobj.IsVariadic(), item}
  for i := 0; i < fobj.NumIn(); i++ {
  
**ExtFuncInfo** 结构有一个 **Auto** 参数数组。通常第一个参数为 ``sc *SmartContract`` 或 ``rt *Runtime``，我们不能从 |galangres| 语言中传递它们，因为在执行一些golang函数时它们对我们来说是必需的。因此，我们指定在调用函数时将自动使用这些变量。在这种情况下，上述四个函数的第一个参数为 ``rt *Runtime``。 

.. code:: 

  if isauto, ok := ext.AutoPars[fobj.In(i).String()]; ok {
    data.Auto[i] = isauto
  }

赋值有关参数的信息。

.. code:: 

    data.Params[i] = fobj.In(i)
  }
  
以及返回值的类型。

.. code:: 

   for i := 0; i < fobj.NumOut(); i++ {
      data.Results[i] = fobj.Out(i)
   }
   
向根 **Objects** 添加一个函数，这样编译器可以稍后在使用合约时找到它们。

.. code:: 

             vm.Objects[key] = &ObjInfo{ObjExtFunc, data}
        }
    }
    

编译器
===========

*compile.go* 文件的函数负责编译从词法分析器获得的标记数组。编译可以有条件地分为两个级别，在高层级别，我们处理函数、合约、代码块、条件语句和循环语句、变量定义等等。在底层级别，我们编译循环和条件语句中的代码块或条件内的表达式。

首先，让我们描述简单的低层级别。在 **compileEval** 函数可以完成将表达式转换为字节码。由于我们是使用堆栈的虚拟机，因此有必要将普通的中缀记录表达式转换为后缀表示法或逆波兰表示法。例如，``1+2`` 转换为 ``12+``，然后将 ``1`` 和 ``2`` 放入堆栈，然后我们对堆栈中的最后两个元素应用加法运算，并将结果写入堆栈。这种 `转换算法 <https://master.virmandy.net/perevod-iz-infiksnoy-notatsii-v-postfiksnuyu-obratnaya-polskaya-zapis/>`_ 可以在互联网上找到。

全局变量 ``opers = map [uint32] operPrior`` 包含转换成逆波兰表示法时所必需的操作的优先级。

以下变量在 **compileEval** 函数开头定义：

  * **buffer** – 字节码指令的临时缓冲区；
  * **bytecode** – 字节码指令的最终缓冲区；
  * **parcount** – 调用函数时用于计算参数的临时缓冲区；
  * **setIndex** – 当我们分配 *map* 或 *array* 元素时，工作过程中的变量被设置为 `true`。例如，``a["my"] = 10``，在这种情况下，我们需要使用指定的**cmdSetIndex** 指令。

