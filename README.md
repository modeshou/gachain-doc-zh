# 政务链GAChain系统平台的技术开发文档

[![Documentation Status](https://readthedocs.org/projects/gachain/badge/?version=latest)](https://gachain.readthedocs.io/zh/latest/?badge=latest)

政务链GAChain是一个可高效构建数字生态系统的公共区块链系统平台。  

该文档为政务链GAChain系统平台的技术开发文档，包含平台描述、节点部署、开发示例、合约开发语言GALang、模版开发语言GAStyle的使用方法等。

开始
======================

该项目使用Sphinx (http://www.sphinx-doc.org/en/stable/index.html) 构建发布到Read the Docs的html。要在计算机上运行此文档，您应该执行以下操作：

预备条件
--------------------------------------------------------------------------------
* Python 2.6 或更高
* git

安装Sphinx等
--------------------------------------------------------------------------------
对于OSX / Linux用户（根据以下说明： https://read-the-docs.readthedocs.org/en/latest/getting_started.html )。从1.4.0开始，Sphinx不再自动安装 ``sphinx_rtd_theme`` ，因此在下面添加它。

* 命令: ``sudo pip install sphinx sphinx_rtd_theme``

对于windows用户:

* http://www.sphinx-doc.org/en/stable/install.html#windows-install-python-and-sphinx

获取源代码
--------------------------------------------------------------------------------
* git clone: https://github.com/GACHAIN/gachain-doc-zh.git

构建和查看HTML
--------------------------------------------------------------------------------
* 在终端窗口中，转到gachain-doc-zh目录。
* ``make html``
* ``cd build/html``
* ``open index.html`` (在浏览器中打开）
* 提示：每次运行时 ``make html``, 只需重新加载浏览器即可查看更改
