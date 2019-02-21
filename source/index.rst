.. gachain documentation master file, created by
   sphinx-quickstart on Thu Feb 21 19:43:54 2019.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

.. _gachain.org: https://gachain.org

构建数字生态系统的公共区块链平台
================================

关于该文档
----------

|platform| 是一个构建数字生态系统的公共区块链平台。

该文档为政务链GAChain系统平台的技术开发文档，包含平台描述、节点部署、开发示例、合约开发语言 |galangres| 、模版开发语言 |gastyleres| 的使用方法等。

关于该平台
^^^^^^^^^^^

|platform| 区块链是一个安全，简单和可扩展的区块链基础设施，适用于快速增长的协作经济部门。中小型企业将在降低运营成本、加快上市时间、早期发展阶段的筹资方案、业务流程自动化和可扩展性、综合结算系统、合法的反洗钱基础设施、不信任的经济合作、其产品和服务的影响力中受益。|platform| 平台的详细描述位于官网（`gachain.org`_）的白皮书。

|platform| 平台的使用，包括许可条款，受 |platform| 法律文件中规定的条款和条件的约束，该条款和条件可从官网(`gachain.org`_)下载。


政务链平台的基础研发于2011年开展，2016年正式启动政务链（|platform|）的研发，经过政务流程团队、电子政务专家顾问团队和技术团队的努力，至2016第三季度，完成了如下工作: 

1. 创建了主要网络协议和轻量级软件客户端；
2. 开发了智能合约的概念；
3. 发布了早期版本的接口界面和合约编程语言。

由于该平台最初是为了实施电子政务领域的项目而开发的，因此团队决定将项目命名为 **政务链** (*电子政务即服务*)。2017年秋季，创建了政务链第一版本（v1）的测试网络。至2017年年底，为广东省南沙自贸区电子服务中心、中央扶贫办、天津市市场监督管理局、香港机场服务管理公司等政企机构开发出相关概念验证项目和解决方案，如多部门电子政务自动协同办公和流转、社会化代收税电子发票系统、精准扶贫平台、供应链管理等。

也是在2017年冬季，政务链团队决定在现有平台的基础上建立一个公共区块链网络，与相关公共安全部门合作，将KYC（个人身份识别）程序整合到用户账户的注册阶段。

由于该项目将不再只适应于政务服务项目，还可广泛用于商业、个人等项目的开发、应用，因此团队将项目的品牌标识做了一定修改：

1. 面对政府、事业单位的项目，依然使用“政务链”，英文名为 **GAChain** ( Government Affairs Chain);
2. 面对商业机构、个人客户，中文使用“智乾链”，英文名依然为 **GAChain** 。

|platform| 平台的软件客户端起名为Govis和编程语言：智语言（|galangres| Language）为合约语言和乾语言（|gastyleres| Language）为界面编辑语言。同时，启动了源代码重构过程：软件客户端的代码被完全重写（基于JavaScript React库），创建了REST API v2的新版本，智语言（|galangres| Language）和乾语言（|gastyleres| Language）得到显着改善。另外，开发了专门生态系统的概念和功能，开始了视觉页面设计器的工作，并且实施了一些其它相关性改变和改进。

到2018年初，政务链（|platform|）平台已经做好与当前火热的区块链平台Ethereum、NEO相竞争的准备，团队相信，政务链（|platform|）在具有颠覆性的产品规划设计、优秀性能等优势，必将对现有区块链产品带来从概念到实际应用的冲击。

区块链颠覆了现有常规技术和观念，政务链颠覆了现有常规区块链！

本文档包含平台功能的最新描述，并且在更改或添加新功能时不断更新。

目录
========

.. toctree::
   :maxdepth: 2
   :caption: 概念

   concepts/about-the-platform.rst
   concepts/blockchain-layers.rst
   concepts/blockchain-basics.rst
   concepts/consensus.rst
   concepts/faq.rst
   concepts/thesaurus.rst


.. toctree::
   :maxdepth: 2
   :caption: 教程

   tutorials/app_tutorial.rst


.. toctree::
   :maxdepth: 2
   :caption: 指南

   topics/script.rst
   topics/templates2.rst
   topics/appexample.rst
   topics/vm.rst
   topics/daemons.rst


.. toctree::
   :maxdepth: 2
   :caption: 参考

   reference/api2.rst
   reference/platform-parameters.rst
   reference/backend-config.rst
   reference/desync_monitor.rst


.. toctree::
   :maxdepth: 2
   :caption: 部署

   howtos/deployment.rst

.. todolist::
