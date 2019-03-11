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
