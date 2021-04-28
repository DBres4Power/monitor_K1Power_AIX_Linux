# monitor_K1Power_AIX_Linux
介绍K1 Power服务器，AIX/VIOS/Linux系统可视化监控解决方案

# 可视化监控工具组件介绍

* [nmon](http://nmon.sourceforge.net/pmwiki.php?n=Main.HomePage)

nmon是Nigel's performance Monitor的缩写，它是IBM员工 Nigel Griffiths开发的一款计算机性能监控和数据收集工具。这个工具已有将近20年历史，早在2003年，nmon工具已经成为用于AIX 4.1.5、4.2.0、4.3.2 和 4.3.3的成熟监控工具，后来不断为AIX 5.1,5.2,5.3,6.1,7.1,7.2迭代新的版本，并逐步拓展Power/AIX之外的Linux系统和其它平台。现在它可以监控AIX和Linux系统，支持Power，X86，Mainframe乃至ARM (Raspberry Pi)平台，是一个标准的支持跨多平台、多系统的监控工具。

* [njmon,nimon](https://tinyurl.com/njmon)

njmon是nmon的新一代监控工具，它实现了nmon的类似功能，性能指标输出为JSON格式。可以将njmon性能统计监控数据输出直接push到InfluxDB数据库中，Grafana配置InfluxDB数据源实时读取监控数据，来实现性能指标实时可视化监控。nimon功能与njmon相似，所不同的是它将性能指标输出为InfluxDB Line协议格式。

* [InfluxDB](https://www.power-devops.com/influxdb)

InfluxDB是由 InfluxData 开发的开源时序型数据库。它使用go 语言编写，致力于高效地存储和查询时序型数据。InfluxDB 被广泛应用于存储系统性能指标监控数据。InfluxDB语法是类SQL的，增删改查与MySQL基本相同。InfluxDB默认端口为8086，它的measurement 对应关系型数据库中的table概念。

* [Telegraf](https://www.power-devops.com/telegraf)

Telegraf是一个用Golang写的开源数据收集Agent，基于插件驱动。Telegraf是influxdata公司的时间序列平台TICK技术栈中的“T”，主要用于收集时间序列型数据，比如服务器CPU指标、内存指标、各种IoT设备产生的数据等等。
![image](https://github.com/DBres4Power/monitor_K1Power_AIX_Linux/)
Telegraf工作原理：定时去执行输入插件收集数据，数据经过处理插件和聚合插件，批量输出到数据存储。

•	数据指标（Metrics）

  o	指标名（Measurement name）：指标描述和命名。

  o	标签集合（Tags）：Key/Value键值对，可以类比为关系型数据库的键值，常用于快速索引和唯一标识。标签在设计的时候，尽量避免各种数值型，尽量使用有限集合。

  o	字段集合（Fields）：Key/Value键值对，包含指标描述的数据类型和值。

  o	时间戳（Timestamp）：此条指标数据的时间戳。

•	插件（Plugins）Telegraf有四种类型的插件：

  o	输入插件（Inputs）：收集各种时间序列性指标，包含各种系统信息和应用信息的插件。
 
  o	处理插件（Process）：当收集到的指标数据流要进行一些简单处理时，比如给所有指标添加、删除、修改一个Tag。只是针对当前的指标数据进行。

  o	聚合插件（Aggregate）：聚合插件有别于处理插件，就在于它要处理的对象是某段时间流经该插件的所有数据（所以，每个聚合插件都有一个period设置，只会处理now()-period时间段内的数据），比如取最大值、最小值、平均值等操作。

  o	输出插件（Outputs）：收集到的数据，经过处理和聚合后，输出到数据存储系统，可以是各种地方，如：文件、InfluxDB、各种消息队列服务等等。
本文示例中我们将用到Inputs和Outputs插件。
