|platform| 区块链网络部署
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

本章节演示如何部署自己的区块链网络。
    
    .. note::
    
        * 如果您想尝试使用 |platform| 区块链网络，请查看 `GAChain Testnet <https://devfront.gac.one/>`_。


.. _deployment-config:

部署示例
##################

以三个节点为示例部署区块链网络。

三个网络节点: 

    - 节点1是区块链网络中的第一个节点，它可以生成新区块并从连接到它的客户端发送交易；

    - 节点2是另一个验证节点，它可以生成新区块并从连接到它的客户端发送交易；
    
    - 节点3是一个全节点，它不能生成新区块，但可以从连接到它的客户端发送交易。

三个节点部署以下配置：

    - 每个节点都使用自己的PostgreSQL数据库系统实例；

    - 每个节点都使用自己的Centrifugo服务实例；

    - go-gachain服务与其他后端组件部署在同一主机上。


节点使用的示例地址和端口如下表所述：

.. list-table::
   :header-rows: 1
   :widths: auto

   * - 节点
     - 组件
     - IP和端口
   * - 1
     - PostgreSQL
     - 127.0.0.1:5432
   * - 1
     - Centrifugo
     - 192.168.1.1:8000
   * - 1
     - go-gachain (TCP服务)
     - 192.168.1.1:7078
   * - 1
     - go-gachain (API服务)
     - 192.168.1.1:7079
   * - 2
     - PostgreSQL
     - 127.0.0.1:5432
   * - 2
     - Centrifugo
     - 192.168.1.2:8000
   * - 2
     - go-gachain (TCP服务)
     - 192.168.1.2:7078
   * - 2
     - go-gachain (API服务)
     - 192.168.1.2:7079
   * - 3
     - PostgreSQL
     - 127.0.0.1:5432
   * - 3
     - Centrifugo
     - 192.168.1.3:8000
   * - 3
     - go-gachain (TCP服务)
     - 192.168.1.3:7078
   * - 3
     - go-gachain (API服务)
     - 192.168.1.3:7079


部署阶段
#################

您自己的区块链网络必须分几个阶段部署：

1. :ref:`服务端部署 <deployment-backend>`

    1. :ref:`部署第一个节点 <deployment-first-node>`
    2. :ref:`部署其他节点 <deployment-additional-nodes>`

2. :ref:`前端部署 <deployment-frontend>`

3. :ref:`区块链网络配置 <deployment-blockchain-config>`

    1. :ref:`创建创始人的帐户 <deployment-founder>`
    2. :ref:`导入应用、角色和模版 <deployment-import>`
    3. :ref:`将第一个节点添加到节点列表中 <deployment-full-nodes-first>`

4. :ref:`添加其他验证节点 <deployment-full-nodes-extra>`

    1. :ref:`将成员添加到共识角色 <deployment-consensus-member>`
    2. :ref:`创建节点所有者帐户 <deployment-validator-account>`
    3. :ref:`添加节点所有者为Validators角色并通过投票添加新验证节点 <deployment-validator-voting>`


.. _deployment-backend:

服务端部署
##################

.. _deployment-first-node:

部署第一个节点
========================

第一个节点是一个特殊节点，因为它必须用于启动区块链网络。区块链的第一个区块由第一个节点生成，所有其他节点从中下载区块链。第一个节点的所有者为平台创始人。


.. _dependencies:

依赖关系和环境设置
----------------------------------

sudo
""""

Debian 9的所有命令必须以非root用户身份运行。但是某些系统命令需要执行超级用户权限。默认情况下，Debian 9上没有安装sudo，您必须先安装它。

1) 成为超级用户。

.. code-block:: bash

    su -


2) 升级您的系统。
.. code-block:: bash
    
    apt update -y && apt upgrade -y && apt dist-upgrade -y

3) 安装sudo。

.. code-block:: bash

    apt install sudo -y


4) 将您的用户添加到sudo组。

.. code-block:: bash
    
    usermod -a -G sudo user

5) 重启后，更改生效。


Go 语言
"""""""""""

按照 `官方文档 <https://golang.org/doc/install#tarball>`_ 的说明按照Go。


1) 从 `Golang官方网站 <https://golang.org/dl/>`_ 或通过命令行下载最新的稳定版Go（> 1.10.x）：

.. code-block:: bash

    wget https://dl.google.com/go/go1.11.2.linux-amd64.tar.gz

2) 将安装包解压缩到 ``/usr/local``.

.. code-block:: bash

    tar -C /usr/local -xzf go1.11.2.linux-amd64.tar.gz


3) 添加 ``/usr/local/go/bin`` 到PATH环境变量 (位于 ``/etc/profile`` 或 ``$HOME/.profile``)。

.. code-block:: bash

    export PATH=$PATH:/usr/local/go/bin


4) 要使更改生效，请执行 ``source`` 该文件，例如：

.. code-block:: bash
    
    source $HOME/.profile


5) 删除临时文件：

.. code-block:: bash

    rm go1.11.2.linux-amd64.tar.gz


PostgreSQL
""""""""""

1) 安装PostgreSQL（> v.10）和psql：

.. code-block:: bash

    sudo apt install -y postgresql


Centrifugo
""""""""""

1) 从 `GitHub <https://github.com/centrifugal/centrifugo/releases/>`_ 或通过命令行下载Centrifugo 1.8.0版本：


.. code-block:: bash

    wget https://github.com/centrifugal/centrifugo/releases/download/v1.8.0/centrifugo-1.8.0-linux-amd64.zip \
    && unzip centrifugo-1.8.0-linux-amd64.zip \
    && mkdir centrifugo \
    && mv centrifugo-1.8.0-linux-amd64/* centrifugo/


2) 删除临时文件：

.. code-block:: bash

    rm -R centrifugo-1.8.0-linux-amd64 \
    && rm centrifugo-1.8.0-linux-amd64.zip


目录结构
"""""""""""

对于Debian 9 系统，建议将区块链平台使用的所有软件存储在单独的目录中。

在这里使用 ``/opt/gachain`` 目录，但您可以使用任何目录。在这种情况下，请相应地更改所有命令和配置文件。

1) 为区块链平台创建一个目录：

.. code-block:: bash

    sudo mkdir /opt/gachain

2) 使您的用户成为该目录的所有者：

.. code-block:: bash

    sudo chown user /opt/gachain/

3) 为Centrifugo、go-gachain和节点数据创建子目录。所有节点数据都存储在名为 ``nodeX`` 的目录中，其中 ``X`` 为节点号。根据要部署的节点，``node1`` 为节点1，``node2`` 为节点2，以此类推。

.. code-block:: bash

    mkdir /opt/gachain/go-gachain \
    mkdir /opt/gachain/go-gachain/node1 \
    mkdir /opt/gachain/centrifugo \


.. _database:

创建数据库
---------------------

1) 将用户密码postgres更改为GAChain的默认密码 *gachain*。您可以设置自己的密码，但必须在节点配置文件 *config.toml* 中进行更改。

.. code-block:: bash

    sudo -u postgres psql -c "ALTER USER postgres WITH PASSWORD 'gachain'"


2) 创建节点当前状态数据库，例如 **gachaindb**:

.. code-block:: bash

    sudo -u postgres psql -c "CREATE DATABASE gachaindb"

.. _centrifugo:

配置Centrifugo
----------------------

1) 创建Centrifugo配置文件：

.. code-block:: bash

    echo '{"secret":"CENT_SECRET"}' > /opt/gachain/centrifugo/config.json

您可以设置自己的 *secret*，但是您还必须在节点配置文件 *config.toml* 中更改它。

.. _go-gachain-install:

安装go-gachain
----------------------

1) 从GitHub下载 `最新版本的go-gachain <https://github.com/GACHAIN/gachain-bin/releases>`_ ：

2) 将go-gachain二进制文件复制到 ``/opt/gachain/go-gachain`` 目录。如果您使用的是 `默认的Go工作区 <https://golang.org/doc/code.html#Workspaces>`_ 则二进制文件位于 ``$HOME/go/bin`` 目录：

.. code-block:: bash

    cp $HOME/go/bin/go-gachain /opt/gachain/go-gachain


配置第一个节点
--------------------------

1) 创建节点1配置文件：

.. code-block:: bash

    /opt/gachain/go-gachain/go-gachain config \
        --dataDir=/opt/gachain/go-gachain/node1 \
        --dbName=gachaindb \
        --centSecret="CENT_SECRET" --centUrl=http://192.168.1.1:8000 \
        --httpHost=192.168.1.1 \
        --httpPort=7079 \
        --tcpHost=192.168.1.1 \
        --tcpPort=7078

4) 生成节点1的密钥，包括节点公私钥和账户公私钥：

.. code-block:: bash

    /opt/gachain/go-gachain/go-gachain generateKeys \
        --config=/opt/gachain/go-gachain/node1/config.toml

5) 生成第一个区块：

.. note:: 
    
    如果您要创建自己的区块链网络。你必须使用该 ``--test=true`` 选项。否则您将无法创建新帐户。

.. code-block:: bash

    /opt/gachain/go-gachain/go-gachain generateFirstBlock \
        --config=/opt/gachain/go-gachain/node1/config.toml \
        --test=true

6) 初始化数据库：

.. code-block:: bash

    /opt/gachain/go-gachain/go-gachain initDatabase \
        --config=/opt/gachain/go-gachain/node1/config.toml


启动第一个节点服务端
-------------------------------

.. _services: https://wiki.debian.org/systemd/Services

要启动第一个节点服务端，您必须启动两个服务：

-   centrifugo
-   go-gachain

如果您没有将这些文件创建 `services`_，那么您可以从不同控制台的目录中执行二进制文件。

1) 运行centrifugo:

.. code-block:: bash

    /opt/gachain/centrifugo/centrifugo \
        -a 192.168.1.1 -p 8000 \
        --config /opt/gachain/centrifugo/config.json


2) 运行go-gachain:

.. code-block:: bash

    /opt/gachain/go-gachain/go-gachain start \
        --config=/opt/gachain/go-gachain/node1/config.toml


.. _deployment-additional-nodes:

部署其他节点
==========================

所有其他节点（节点2和节点3）的部署与第一个节点类似，但有三个不同之处：

- 您不需要生成第一个区块。但是它必须从节点1复制到当前节点数据目录；
- 该节点必须通过配置 ``--nodesAddr`` 选项从节点1下载区块；
- 该节点必须使用自己的地址和端口。

节点2
------

按照以下一系列操作：

    1. :ref:`dependencies`

    2. :ref:`database`

    3. :ref:`centrifugo`

    4. :ref:`go-gachain-install`

    5. 创建节点2配置文件：

        .. code-block:: bash

            /opt/gachain/go-gachain/go-gachain config \
                --dataDir=/opt/gachain/go-gachain/node2 \
                --dbName=gachaindb \
                --centSecret="CENT_SECRET" --centUrl=http://192.168.1.2:8000 \
                --httpHost=192.168.1.2 \
                --httpPort=7079 \
                --tcpHost=192.168.1.2 \
                --tcpPort=7078 \
                --nodesAddr=192.168.1.1

    6. 复制第一个区块文件到节点2，例如，您可以通过 ``scp`` 在节点2执行该操作：

        .. code-block:: bash
            
            scp user@192.168.1.1:/opt/gachain/go-gachain/node1/1block /opt/gachain/go-gachain/node2/


    7. 生成节点2的密钥，包括节点公私钥和账户公私钥：

        .. code-block:: bash

            /opt/gachain/go-gachain/go-gachain generateKeys \
                --config=/opt/gachain/go-gachain/node2/config.toml

    8. 初始化数据库：

        .. code-block:: bash
        
            ./go-gachain initDatabase --config=node2/config.toml

    9. 运行centrifugo:

        .. code-block:: bash

            /opt/gachain/centrifugo/centrifugo \
                -a 192.168.1.2 -p 8000 \
                --config /opt/gachain/centrifugo/config.json

    10. 运行go-gachain:

        .. code-block:: bash

            /opt/gachain/go-gachain/go-gachain start \
                --config=/opt/gachain/go-gachain/node2/config.toml


结果，节点从第一个节点下载区块。该节点不是验证节点，因此无法生成新区块。节点2将后面添加到验证节点列表中。

节点3
------

按照以下一系列操作：

    1. :ref:`dependencies`

    2. :ref:`database`

    3. :ref:`centrifugo`

    4. :ref:`go-gachain-install`

    5. 创建节点3配置文件：

        .. code-block:: bash

            /opt/gachain/go-gachain/go-gachain config \
                --dataDir=/opt/gachain/go-gachain/node3 \
                --dbName=gachaindb \
                --centSecret="CENT_SECRET" --centUrl=http://192.168.1.3:8000 \
                --httpHost=192.168.1.3 \
                --httpPort=7079 \
                --tcpHost=192.168.1.3 \
                --tcpPort=7078 \
                --nodesAddr=192.168.1.1

    6. 复制第一个区块文件到节点3，例如，您可以通过 ``scp`` 在节点3执行该操作：

        .. code-block:: bash
            
            scp user@192.168.1.1:/opt/gachain/go-gachain/node1/1block /opt/gachain/go-gachain/node3/


    7. 生成节点3的密钥，包括节点公私钥和账户公私钥：

        .. code-block:: bash

            /opt/gachain/go-gachain/go-gachain generateKeys \
                --config=/opt/gachain/go-gachain/node3/config.toml

    8. 初始化数据库：

        .. code-block:: bash
        
            ./go-gachain initDatabase --config=node3/config.toml

    9. 运行centrifugo:

        .. code-block:: bash

            /opt/gachain/centrifugo/centrifugo \
                -a 192.168.1.3 -p 8000 \
                --config /opt/gachain/centrifugo/config.json

    10. 运行go-gachain:

        .. code-block:: bash

            /opt/gachain/go-gachain/go-gachain start \
                --config=/opt/gachain/go-gachain/node3/config.toml

结果，节点从第一个节点下载区块。该节点不是验证节点，因此无法生成新区块。客户端可以连接到该节点，它可以将交易发送到网络。


.. _deployment-frontend:

前端部署
###################

只有在Debian 9(Stretch)64位 `官方发行版 <https://www.debian.org/CD/http-ftp/#stable>`_ 上安装 **GNOME GUI**，Govis客户端才能由 ``yarn`` 包管理器构建。

软件先决条件
======================

Node.js
-------

1) 从 `Node.js官方网站 <https://nodejs.org/en/download/>`_ 或通过命令行下载Node.js LTS版本8.11 ：

.. code-block:: bash

    curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash


2) 安装Node.js:

.. code-block:: bash

    sudo apt install -y nodejs


Yarn
----

1) 从 `yarn的Github仓库 <https://github.com/yarnpkg/yarn/releases>`_ 或通过命令行下载Yarn版本1.7.0 ：

.. code-block:: bash

    cd /opt/gachain \
    && wget https://github.com/yarnpkg/yarn/releases/download/v1.7.0/yarn_1.7.0_all.deb

2) 安装Yarn:

.. code-block:: bash

    sudo dpkg -i yarn_1.7.0_all.deb && rm yarn_1.7.0_all.deb


构建Govis应用程序
==================

1) 通过git从 `Govis的GitHub仓库 <https://github.com/GACHAIN/gachain-front/releases>`_ 下载Govis的最新版本：

.. code-block:: bash

    cd /opt/gachain \
    && git clone https://github.com/GACHAIN/gachain-front.git

2) 通过Yarn安装Govis依赖项：

.. code-block:: bash

    cd /opt/gachain/gachain-front/ \
    && yarn install


.. _front-connections:

添加区块链网络配置
-------------------------------------------

1) 创建包含有关节点连接信息的 *settings.json* 文件：

.. code-block:: bash

    cp /opt/gachain/gachain-front/public/settings.json.dist \
        /opt/gachain/gachain-front/public/public/settings.json

2) 在任何文本编辑器中编辑 *settings.json* 文件，并以此格式添加所需的设置：

.. code-block:: text

    http://Node_IP-address:Node_HTTP-Port


三个节点的 *settings.json* 文件示例：

.. code-block:: json

    {
        "fullNodes": [
            "http://192.168.1.1:7079",
            "http://192.168.1.2:7079",
            "http://192.168.1.3:7079"
        ]
    }

构建Govis桌面版应用程序
-----------------------------

1) 使用yarn构建桌面版：

.. code-block:: bash
    
    cd /opt/gachain/gachain-front \
    && yarn build-desktop

2) 桌面版将打包成AppImage后缀格式:

.. code-block:: bash

    yarn release --publish never -l

构建之后，您的应用程序就可以使用了，但是其 :ref:`连接配置 <front-connections>` 将无法更改。如果这些设置需要更改，则必须构建新版本的应用程序。


构建Govis Web应用程序
-------------------------

1) 构建Web应用程序：

.. code-block:: bash
    
    cd /opt/gachain/gachain-front/ \
    && yarn build

构建之后，可再发行文件将放置到 */build* 目录中。您可以使用您选择的任何Web服务器进行部署，*settings.json* 文件也必须放在该目录。请注意，如果连接设置发生更改，则无需再次构建应用程序。而是编辑 *settings.json* 文件并重新启动Web服务器。

2) 出于开发或测试目的，您可以构建Yarn的Web服务器：

.. code-block:: bash

    sudo yarn global add serve \
    && serve -s build

之后，您的Govis Web应用程序将在以下位置可用: ``http://localhost:5000``。

.. _deployment-blockchain-config:

区块链网络配置
################################

.. _deployment-founder:

创建创始人的帐户
==============================

为第一个节点所有者创建一个帐户。该帐户是新区块链平台的创始人，并具有管理员访问权限。

1) 运行 Govis (前端)；

2) 使用以下数据导入现有帐户：

    - 节点所有者私钥的备份加载位于 ``/opt/gachain/go-gachain/node1/PrivateKey`` 文件中。

        .. note::

            该目录中有两个私钥文件。``PrivateKey`` 文件用于节点所有者的帐户，可创建节点所有者的帐户。``NodePrivateKey`` 文件是节点本身的私钥，必须保密。

3) 登录该账户后，由于此时尚未创建角色，因此请选择 *Without role* 或 *无* 选项。

.. _deployment-import:

导入应用、角色和模版
====================================

此时，区块链平台处于空白状态。您可以通过添加支持基本生态系统功能的角色、模版和应用程序框架来配置它。

1) 克隆应用程序存储库；

.. code-block:: bash

    cd /opt/gachain \
    && git clone https://github.com/GACHAIN/apps.git

2) 在Govis中导航到 *Developer* > *导入*；

3) 按此顺序导入应用：

    A. /opt/gachain/apps/1_system.json
    B. /opt/gachain/apps/2_lang_res.json
    C. /opt/gachain/apps/3_basic.json
    D. /opt/gachain/apps/4_conditions.json

4) 导航到 *Admin* > *角色*，然后单击 *安装默认角色*；

5) 通过右上角的配置文件菜单退出系统；

6) 以 *Admin* 角色登录系统；

7) 导航到 *Home* > *投票* > *模版列表*，然后单击 *安装默认模版*。

.. _deployment-full-nodes-first:

将第一个节点添加到节点列表中
======================================


1) 导航到 *Admin* > *平台参数*，然后单击 *full_nodes* 参数的齿轮图标；

2) 指定第一个区块链网络节点的参数。

    - **public_key** - 节点公钥位于 ``/opt/gachain/go-gachain/node1/NodePublicKey`` 文件；
    - **key_id** - 节点所有者的账户地址位于 ``/opt/gachain/go-gachain/node1/KeyID`` 文件。

.. code-block:: json

    {"api_address":"http://192.168.1.1:7079","key_id":"%node_owner_key_id%","public_key":"%node_public_key%","tcp_address":"192.168.1.1:7078"}


.. _deployment-full-nodes-extra:

添加其他验证节点
#############################

.. _deployment-consensus-member:

将成员添加到共识角色
=================================

默认情况下，只有共识角色（Consensus）的成员才能参与添加其他验证节点所需的投票。这意味着在添加新的验证节点之前，必须为该角色指定生态系统的成员。

在本章节中，创始人的帐户被指定为共识角色的唯一成员。在生产环境中，必须将该角色分配给平台执行治理的成员。

1) 导航到 *Home* > *角色* ，然后单击共识角色（Consensus）；

2) 单击 *分配* 将创始人的帐户分配给该角色。


.. _deployment-validator-account:

创建其他节点所有者帐户
=================================


1) 运行 Govis (前端)；

2) 使用以下数据导入现有帐户：

    - 节点所有者私钥的备份加载位于 ``/opt/gachain/go-gachain/node2/PrivateKey`` 文件中。

3) 登录该账户后，由于此时尚未创建角色，因此请选择 *Without role* 或 *无* 选项；

4) 导航到 *Home* > *个人信息*，然后单击个人信息名称；

5) 添加帐户详细信息（个人信息名称，说明等）。


.. _deployment-validator-voting:

添加节点所有者为Validators角色
===================================

1) 新节点所有者操作：

    A. 导航到 *Home* > *验证者*；

    B. 单击 *创建请求* 并填写验证者候选人的申请表；

    C. 单击 *发送请求*。

2) 创始人操作:

    A. 以共识角色（Consensus）登录；

    B. 导航到 *Home* > *验证者*；

    C. 根据候选人的请求点击“播放”图标开始投票；

    D. 导航到 *Home* > *投票*，然后单击 *更新投票状态*；

    E. 单击投票名称并为节点所有者投票。

结果，新节点所有者的帐户被分配给 *Validator* 角色，并且新节点也被添加到验证节点列表中。