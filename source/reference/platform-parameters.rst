.. -- Conditionals Gachain -------------------------------------------------

.. token naming
.. |tokens| replace:: GAC

.. _平台参数:

|platform| 区块链平台参数
############################



关于平台参数
==============

平台参数是区块链平台的配置参数。这些参数适用于区块链网络和网络中的所有生态系统。

存储平台参数的位置
------------------------------------

平台参数存储在 ``system_parameters`` 数据表。

该数据表在区块链网络上创建的第一个（默认）生态系统中

更改平台参数
----------------------------

必须在投票通过后才能更改平台参数。

平台参数配置
==============================


区块链网络配置
------------------

节点:

- :ref:`full_nodes`
- :ref:`number_of_nodes`

节点禁令:

- :ref:`incorrect_blocks_per_day`
- :ref:`node_ban_time`
- :ref:`node_ban_time_local`


新生态系统配置
--------------

默认页面和菜单:

- :ref:`default_ecosystem_page`
- :ref:`default_ecosystem_menu`

默认合约:

- :ref:`default_ecosystem_contract`


数据库配置
-----------

数据表限制:

- :ref:`max_columns`
- :ref:`max_indexes`


.. _parameters-block-limits:

区块生成配置
----------------

时间限制:

- :ref:`gap_between_blocks`
- :ref:`max_block_generation_time`

交易数量限制:

- :ref:`max_tx_block`
- :ref:`max_tx_block_per_user`

大小限制:

- :ref:`max_tx_size`
- :ref:`max_block_size`
- :ref:`max_forsign_size`

燃料限制:

- :ref:`max_fuel_block`
- :ref:`max_fuel_tx`

区块回滚限制:

- :ref:`rollback_blocks`


燃料通证配置
-------------------

奖励和佣金:

- :ref:`block_reward`
- :ref:`commission_wallet`
- :ref:`commission_size`

燃料费率转换:

- :ref:`fuel_rate`
- :ref:`price_create_rate`

交易大小数据价格:

- :ref:`price_tx_data`
- :ref:`price_tx_size_wallet`

新建元素价格:

- :ref:`price_create_ecosystem`
- :ref:`price_create_table`
- :ref:`price_create_column`
- :ref:`price_create_contract`
- :ref:`price_create_menu`
- :ref:`price_create_page`
- :ref:`price_create_application`

运营价格:

.. hlist::
    :columns: 2

    - :ref:`price_exec_bind_wallet`
    - :ref:`price_exec_address_to_id`
    - :ref:`price_exec_column_condition`
    - :ref:`price_exec_compile_contract`
    - :ref:`price_exec_contains`
    - :ref:`price_exec_contracts_list`
    - :ref:`price_exec_contract_by_name`
    - :ref:`price_exec_contract_by_id`
    - :ref:`price_exec_create_column`
    - :ref:`price_exec_create_ecosystem`
    - :ref:`price_exec_create_table`
    - :ref:`price_exec_unbind_wallet`
    - :ref:`price_exec_ecosys_param`
    - :ref:`price_exec_eval`
    - :ref:`price_exec_eval_condition`
    - :ref:`price_exec_flush_contract`
    - :ref:`price_exec_has_prefix`
    - :ref:`price_exec_id_to_address`
    - :ref:`price_exec_is_object`
    - :ref:`price_exec_join`
    - :ref:`price_exec_json_to_map`
    - :ref:`price_exec_len`
    - :ref:`price_exec_perm_column`
    - :ref:`price_exec_perm_table`
    - :ref:`price_exec_pub_to_id`
    - :ref:`price_exec_replace`
    - :ref:`price_exec_sha256`
    - :ref:`price_exec_size`
    - :ref:`price_exec_substr`
    - :ref:`price_exec_sys_fuel`
    - :ref:`price_exec_sys_param_int`
    - :ref:`price_exec_sys_param_string`
    - :ref:`price_exec_table_conditions`
    - :ref:`price_exec_update_lang`
    - :ref:`price_exec_validate_condition`

弃用配置
----------

已弃用参数:

- :ref:`blockchain_url`

平台参数详情
===================


.. _block_reward:

block_reward
------------

    授予生成区块的验证节点的 |tokens| 通证数量。

    接受奖励的帐户在 :ref:`full_nodes` 参数中指定。


.. _blockchain_url:

blockchain_url
--------------

    该参数已弃用。


.. _commission_size:

commission_size
---------------

    佣金百分比。
    
    这笔佣金数量为执行合约总费用按百分比计算得出。该佣金通证单位 |tokens|。

    佣金将转移到 :ref:`commission_wallet` 参数中指定的帐户地址。


.. _commission_wallet:

commission_wallet
-----------------

    收取佣金的账户地址。
    
    佣金数量由 :ref:`commission_size` 参数指定。


.. _default_ecosystem_contract:

default_ecosystem_contract
--------------------------

    新生态系统默认合约的源代码。

    该合约为生态系统创建者提供访问权限。


.. _default_ecosystem_menu:

default_ecosystem_menu
----------------------

    新生态系统的默认菜单的源代码。


.. _default_ecosystem_page:

default_ecosystem_page
----------------------

    新生态系统的默认页面的源代码。


.. _fuel_rate:

fuel_rate
---------

    不同生态系统通证对燃料单位的费率。

    该参数的格式：

        ``[["ecosystem_id", "token_to_fuel_rate"], ["ecosystem_id2", "token_to_fuel_rate2"], ...]``

        - ``ecosystem_id``

            生态系统ID。

        - ``token_to_fuel_rate``

            通证对燃料单位的费率。

    例如:

        ``[["1","1000000000000"], ["2", "1000"]]``

        生态系统1的一个通证被交换到1000000000000个燃料单位。生态系统2的一个通证被交换到1000个燃料单位。


.. _price_create_rate:

price_create_rate
--------------------

    新建元素的燃料费率


.. _full_nodes:

full_nodes
----------

    区块链网络的验证节点列表。

    该参数的格式：

        ``[["tcp_host:port1","api_host:port2","wallet_id","node_pub"], ["tpc_host2:port1","api_host2:port2","wallet_id2","node_pub2"]]``

        - ``tcp_host:port1``

            节点主机的TCP地址和端口。

            交易和新区块将发送到该主机地址。该主机地址还可用于从第一个区块开始获取完整的区块链。
        
        - ``api_host:port2``

            节点主机的API地址和端口。

            通过API地址可以在不使用Govis软件客户端的情况下访问平台的任何功能。详情 :doc:`RESTful API</reference/api2>`。

        - ``wallet_id``

            账户地址，用于收取生成新区块和处理交易的奖励。

        - ``node_pub``

            节点的公钥。此公钥用于验证区块签名。


.. _gap_between_blocks:

gap_between_blocks
------------------

    节点生成前后区块的时间间隔(以秒为单位)。

    网络中的所有节点都使用它来确定何时生成新区块，如果当前节点在此时间段内未生成新区块，则转向传递到验证节点列表中的下一个节点。

    该参数最小值为 ``1`` 秒。


.. _incorrect_blocks_per_day:

incorrect_blocks_per_day
------------------------

    节点每天在被禁令前允许生成的坏区块数量。

    当网络中超过一半的节点从某个节点收到此数量的坏区块时，此节点将在 :ref:`node_ban_time` 时间内从网络中被禁令。


.. _max_block_generation_time:

max_block_generation_time
-------------------------

    生成区块的最大时间，单位毫秒，该时间内如果未能成功生成区块，则报错超时。

.. _max_block_size:

max_block_size
--------------

    区块最大大小，单位字节。


.. _max_columns:

max_columns
-----------

    单个数据表的最大字段数。

    这个最大值不包括预定义的 ``id`` 列。

.. _max_forsign_size:

max_forsign_size
----------------

    交易签名最大大小，单位字节。


.. _max_fuel_block:

max_fuel_block
--------------

    单个区块的最大总燃料费用。


.. _max_fuel_tx:

max_fuel_tx
-----------

    单笔交易的最高总燃料费用。


.. _max_indexes:

max_indexes
-----------

    单个数据表中的最大主键字段数。


.. _max_tx_block:

max_tx_block
------------

    单个区块中的最大交易数。


.. _max_tx_block_per_user:

max_tx_block_per_user
---------------------

    一个账户在一个区块内的最大交易数。


.. _max_tx_size:

max_tx_size
-----------

    最大交易大小，以字节为单位。


.. _node_ban_time:

node_ban_time
-------------

    节点的全局禁令期，以毫秒为单位。

    当网络中超过一半的节点从某个节点收到坏区块达到 :ref:`incorrect_blocks_per_day` 数量时，该节点将在该时间内从网络中被禁令。


.. _node_ban_time_local:

node_ban_time_local
-------------------

    节点的本地禁令期，以毫秒为单位。

    当一个节点从另一个节点接收到不正确的块时，它将在这段时间内本地禁令发送方节点。


.. _number_of_nodes:

number_of_nodes
---------------

    :ref:`full_nodes` 参数中的最大验证节点数量。


.. _price_create_ecosystem:

price_create_ecosystem
-----------------------

    创建新单个生态系统的燃料费用。

    该参数定义了 ``@1NewEcosystem`` 合约的额外燃料费用。执行该合约时，还会计算执行本合约各项函数的燃料费用，并计入总费用。

    该参数以燃料单位计算。使用 :ref:`fuel_rate` 和 :ref:`price_create_rate` 将燃料单位转换为 |tokens| 通证。

.. _price_create_application:

price_create_application
---------------------------

    创建新单个应用程序的燃料费用。

    该参数定义了 ``@1NewApplication`` 合约的额外燃料费用。执行该合约时，还会计算执行本合约各项函数的燃料费用，并计入总费用。

    该参数以燃料单位计算。使用 :ref:`fuel_rate` 和 :ref:`price_create_rate` 将燃料单位转换为 |tokens| 通证。

.. _price_create_table:

price_create_table
---------------------

    创建新单个数据表的燃料费用。

    该参数定义了 ``@1NewTable`` 合约的额外燃料费用。执行该合约时，还会计算执行本合约各项函数的燃料费用，并计入总费用。

    该参数以燃料单位计算。使用 :ref:`fuel_rate` 和 :ref:`price_create_rate` 将燃料单位转换为 |tokens| 通证。


.. _price_create_column:

price_create_column
---------------------

    创建新单个表字段的燃料费用。

    该参数定义了 ``@1NewColumn`` 合约的额外燃料费用。执行该合约时，还会计算执行本合约各项函数的燃料费用，并计入总费用。

    该参数以燃料单位计算。使用 :ref:`fuel_rate` 和 :ref:`price_create_rate` 将燃料单位转换为 |tokens| 通证。


.. _price_create_contract:

price_create_contract
---------------------

    创建新单个合约的燃料费用。

    该参数定义了 ``@1NewContract`` 合约的额外燃料费用。执行该合约时，还会计算执行本合约各项函数的燃料费用，并计入总费用。

    该参数以燃料单位计算。使用 :ref:`fuel_rate` 和 :ref:`price_create_rate` 将燃料单位转换为 |tokens| 通证。


.. _price_create_menu:

price_create_menu
-----------------

    创建新单个菜单的燃料费用。

    该参数定义了 ``@1NewMenu`` 合约的额外燃料费用。执行该合约时，还会计算执行本合约各项函数的燃料费用，并计入总费用。

    该参数以燃料单位计算。使用 :ref:`fuel_rate` 和 :ref:`price_create_rate` 将燃料单位转换为 |tokens| 通证。


.. _price_create_page:

price_create_page
-----------------

    创建新单个页面的燃料费用。

    该参数定义了 ``@1NewPage`` 合约的额外燃料费用。执行该合约时，还会计算执行本合约各项函数的燃料费用，并计入总费用。

    该参数以燃料单位计算。使用 :ref:`fuel_rate` 和 :ref:`price_create_rate` 将燃料单位转换为 |tokens| 通证。


.. _price_exec_address_to_id:

price_exec_address_to_id
------------------------

    调用 :func:`AddressToId` 函数的燃料费用，以燃料单位计算。


.. _price_exec_bind_wallet:

price_exec_bind_wallet
----------------------

    调用 :func:`Activate` 函数的燃料费用，以燃料单位计算。


.. _price_exec_column_condition:

price_exec_column_condition
---------------------------

    调用 :func:`ColumnCondition` 函数的燃料费用，以燃料单位计算。


.. _price_exec_compile_contract:

price_exec_compile_contract
---------------------------

    调用 :func:`CompileContract` 函数的燃料费用，以燃料单位计算。


.. _price_exec_contains:

price_exec_contains
-------------------

    调用 :func:`Contains` 函数的燃料费用，以燃料单位计算。


.. _price_exec_contract_by_id:

price_exec_contract_by_id
-------------------------

    调用 :func:`GetContractById` 函数的燃料费用，以燃料单位计算。


.. _price_exec_contract_by_name:

price_exec_contract_by_name
---------------------------

    调用 :func:`GetContractByName` 函数的燃料费用，以燃料单位计算。


.. _price_exec_contracts_list:

price_exec_contracts_list
-------------------------

    调用 :func:`ContractsList` 函数的燃料费用，以燃料单位计算。


.. _price_exec_create_column:

price_exec_create_column
------------------------

    调用 :func:`CreateColumn` 函数的燃料费用，以燃料单位计算。


.. _price_exec_create_ecosystem:

price_exec_create_ecosystem
---------------------------

    调用 :func:`CreateEcosystem` 函数的燃料费用，以燃料单位计算。


.. _price_exec_create_table:

price_exec_create_table
-----------------------

    调用 :func:`CreateTable` 函数的燃料费用，以燃料单位计算。


.. _price_exec_ecosys_param:

price_exec_ecosys_param
-----------------------

    调用 :func:`EcosysParam` 函数的燃料费用，以燃料单位计算。


.. _price_exec_eval:

price_exec_eval
---------------

    调用 :func:`Eval` 函数的燃料费用，以燃料单位计算。


.. _price_exec_eval_condition:

price_exec_eval_condition
-------------------------

    调用 :func:`EvalCondition` 函数的燃料费用，以燃料单位计算。


.. _price_exec_flush_contract:

price_exec_flush_contract
-------------------------

    调用 :func:`FlushContract` 函数的燃料费用，以燃料单位计算。


.. _price_exec_has_prefix:

price_exec_has_prefix
---------------------

    调用 :func:`HasPrefix` 函数的燃料费用，以燃料单位计算。


.. _price_exec_id_to_address:

price_exec_id_to_address
------------------------

    调用 :func:`IdToAddress` 函数的燃料费用，以燃料单位计算。


.. _price_exec_is_object:

price_exec_is_object
--------------------

    调用 :func:`IsObject` 函数的燃料费用，以燃料单位计算。


.. _price_exec_join:

price_exec_join
----------------

    调用 :func:`Join` 函数的燃料费用，以燃料单位计算。


.. _price_exec_json_to_map:

price_exec_json_to_map
----------------------

    调用 :func:`JSONToMap` 函数的燃料费用，以燃料单位计算。


.. _price_exec_len:

price_exec_len
--------------

    调用 :func:`Len` 函数的燃料费用，以燃料单位计算。


.. _price_exec_perm_column:

price_exec_perm_column
----------------------

    调用 :func:`PermColumn` 函数的燃料费用，以燃料单位计算。


.. _price_exec_perm_table:

price_exec_perm_table
---------------------

    调用 :func:`PermTable` 函数的燃料费用，以燃料单位计算。


.. _price_exec_pub_to_id:

price_exec_pub_to_id
--------------------

    调用 :func:`PubToID` 函数的燃料费用，以燃料单位计算。


.. _price_exec_replace:

price_exec_replace
------------------

    调用 :func:`Replace` 函数的燃料费用，以燃料单位计算。


.. _price_exec_sha256:

price_exec_sha256
-----------------

    调用 :func:`Sha256` 函数的燃料费用，以燃料单位计算。


.. _price_exec_size:

price_exec_size
---------------

    调用 :func:`Size` 函数的燃料费用，以燃料单位计算。


.. _price_exec_substr:

price_exec_substr
-----------------

    调用 :func:`Substr` 函数的燃料费用，以燃料单位计算。


.. _price_exec_sys_fuel:

price_exec_sys_fuel
-------------------

    调用 :func:`SysFuel` 函数的燃料费用，以燃料单位计算。


.. _price_exec_sys_param_int:

price_exec_sys_param_int
------------------------

    调用 :func:`SysParamInt` 函数的燃料费用，以燃料单位计算。


.. _price_exec_sys_param_string:

price_exec_sys_param_string
---------------------------

    调用 :func:`SysParamString` 函数的燃料费用，以燃料单位计算。


.. _price_exec_table_conditions:

price_exec_table_conditions
---------------------------

    调用 :func:`TableConditions` 函数的燃料费用，以燃料单位计算。


.. _price_exec_unbind_wallet:

price_exec_unbind_wallet
------------------------

    调用 :func:`Deactivate` 函数的燃料费用，以燃料单位计算。


.. _price_exec_update_lang:

price_exec_update_lang
----------------------

    调用 :func:`UpdateLang` 函数的燃料费用，以燃料单位计算。


.. _price_exec_validate_condition:

price_exec_validate_condition
-----------------------------

    调用 :func:`ValidateCondition` 函数的燃料费用，以燃料单位计算。


.. _price_tx_data:

price_tx_data
-------------

    交易每1024字节数据的燃料费用，以燃料单位计算。


.. _price_tx_size_wallet:

price_tx_size_wallet
---------------------

    交易大小费用，以 |tokens| 通证为单位。

    除生态系统1之外，在其它生态系统内执行合约将按照比例产生区块空间使用费用，每兆交易大小产生 *price_tx_size_wallet* |tokens| 通证费用。

.. _rollback_blocks:

rollback_blocks
---------------

    在区块链中检测到分叉时可以回滚的最大区块数。
