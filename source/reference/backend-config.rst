服务端配置文件
##################

该章节介绍服务端配置文件参数。

服务端配置文件简介
====================================

服务端配置文件定义了 |platform| 节点的配置。


位置
========

该文件位于服务端工作目录下，名为 ``config.toml``。

部分
========


配置文件有以下几个部分：

.. describe:: 普通部分

    定义工作目录DataDir，第一个区块目录FirstBlockPath等参数。

.. describe:: [TCPServer]

    定义TCP服务参数。

    TCPServer用于节点之间的网络交互。

.. describe:: [HTTP]

    定义HTTP服务参数。

    HTTPServer提供RESTful API。

.. describe:: [DB]

    定义节点数据库PostgreSQL的参数。

.. describe:: [StatsD]

    定义节点操作指标收集器StatsD的参数。

.. describe:: [Centrifugo]

    定义通知服务Centrifugo的参数。

.. describe:: [Log]

    定义了日志服务Log的参数。

.. describe:: [TokenMovement]

    定义了通证流通服务TokenMovement的参数。
    
配置文件示例
==========================

.. code-block:: js

  PidFilePath = "/gachain-data/go-gachain.pid"
  LockFilePath = "/gachain-data/go-gachain.lock"
  DataDir = "/gachain-data"
  KeysDir = "/gachain-data"
  TempDir = "/var/folders/_l/9md_m4ms1651mf5pbng1y1xh0000gn/T/gachain-temp"
  FirstBlockPath = "/gachain-data/1block"
  TLS = false
  TLSCert = ""
  TLSKey = ""
  OBSMode = "none"
  HTTPServerMaxBodySize = 1048576
  MaxPageGenerationTime = 3000
  NodesAddr = []

  [TCPServer]
    Host = "127.0.0.1"
    Port = 7078

  [HTTP]
    Host = "127.0.0.1"
    Port = 7079

  [DB]
    Name = "gachain"
    Host = "127.0.0.1"
    Port = 5432
    User = "postgres"
    Password = "gachain"
    LockTimeout = 5000

  [StatsD]
    Host = "127.0.0.1"
    Port = 8125
    Name = "gachain"

  [Centrifugo]
    Secret = "127.0.0.1"
    URL = "127.0.0.1"

  [Log]
    LogTo = "stdout"
    LogLevel = "ERROR"
    LogFormat = "text"
    [Log.Syslog]
      Facility = "kern"
      Tag = "go-gachain"

  [TokenMovement]
    Host = ""
    Port = 0
    Username = ""
    Password = ""
    To = ""
    From = ""
    Subject = ""


