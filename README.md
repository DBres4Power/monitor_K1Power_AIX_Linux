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

Telegraf工作原理：定时去执行输入插件收集数据，数据经过处理插件和聚合插件，批量输出到数据存储。

•	数据指标（Metrics）

  1	指标名（Measurement name）：指标描述和命名。

  2	标签集合（Tags）：Key/Value键值对，可以类比为关系型数据库的键值，常用于快速索引和唯一标识。标签在设计的时候，尽量避免各种数值型，尽量使用有限集合。

  3	字段集合（Fields）：Key/Value键值对，包含指标描述的数据类型和值。

  4	时间戳（Timestamp）：此条指标数据的时间戳。

•	插件（Plugins）Telegraf有四种类型的插件：

  1	输入插件（Inputs）：收集各种时间序列性指标，包含各种系统信息和应用信息的插件。
 
  2	处理插件（Process）：当收集到的指标数据流要进行一些简单处理时，比如给所有指标添加、删除、修改一个Tag。只是针对当前的指标数据进行。

  3	聚合插件（Aggregate）：聚合插件有别于处理插件，就在于它要处理的对象是某段时间流经该插件的所有数据（所以，每个聚合插件都有一个period设置，只会处理now()-period时间段内的数据），比如取最大值、最小值、平均值等操作。

  4	输出插件（Outputs）：收集到的数据，经过处理和聚合后，输出到数据存储系统，可以是各种地方，如：文件、InfluxDB、各种消息队列服务等等。
   
* [Prometheus](https://prometheus.io)

Prometheus（普罗米修斯）是一套开源的监控&报警&时间序列数据库的组合。Prometheus是继Kubernetes（k8s）之后，CNCF毕业的第二个开源项目，其来源于Google的Borgmon，是由SoundCloud公司开发的。目前Redhat Openshift与Kubernetes都在用Prometheus，越来越多公司和组织接受采用Prometheus，开源社会也十分活跃。

Prometheus daemon负责定时间隔去目标上Pull抓取监控指标(metrics) 数据，由各被抓取目标系统或应用对外暴露http服务接口(exporter)给Prometheus。

Prometheus：支持通过配置文件、文本文件、zookeeper、Consul、DNS SRV lookup等方式指定抓取目标。支持很多方式的图表可视化，例如十分精美的Grafana，自带的Promdash，以及自身提供的模版引擎等等，还可以提供HTTP API的查询方式，自定义输出。

Alertmanager：是独立于Prometheus的组件，支持Prometheus的查询语句，提供十分灵活的报警方式。

PushGateway：这个组件支持Client主动推送metrics到PushGateway，Prometheus则定时去Gateway上Pull抓取数据。
它有点类似于statsd，但statsd是直接发送给服务器端，而Prometheus主要是daemon进程主动去抓取。

Prometheus组件大多数用Go编写的，它们可以轻松地构建和部署为静态二进制文件。

* [Grafana](https://www.power-devops.com/grafana)

Grafana是一个跨平台的开源的度量分析和可视化工具，可以通过将采集的数据查询然后可视化的展示，并及时通知。它主要特点如下：

  1 展示方式：快速灵活的仪表盘图表，面板插件有许多不同方式的可视化指标和日志，官方库中具有丰富的仪表盘插件，比如热图、折线图、图表等多种展示方式；

  2 数据源，可以是Prometheus，Graphite，InfluxDB，OpenTSDB，Elasticsearch，CloudWatch和KairosDB等；

  3 告警通知：以可视方式定义最重要指标的警报规则，Grafana将不断计算并发送通知，在数据达到阈值时通过Slack、PagerDuty等获得通知；

  4 混合展示：在同一图表中混合使用不同的数据源，可以基于每个查询指定数据源，甚至自定义数据源；

  5 注释：使用来自不同数据源的丰富事件注释图表，将鼠标悬停在事件上会显示完整的事件元数据和标记；

  6 过滤器：Ad-hoc过滤器允许动态创建新的键/值过滤器，这些过滤器会自动应用于使用该数据源的所有查询。

# 可视化监控方案一

采用nimon采集AIX，VIOS，Linux性能指标数据，输出直接push到InfluxDB数据库中，Grafana配置InfluxDB数据源实时读取监控数据，来实现性能指标实时可视化监控。其它各个数据库和应用，启动监控exporter，pull到Prometheus中，Grafana读Prometheus数据源做可视化展示。

![image](https://github.com/DBres4Power/monitor_K1Power_AIX_Linux/blob/main/Solution_1_Njmon%2BInfluxDB%2BGrafana/Solution_1.jpg)

# 可视化监控方案二

目前Prometheus较为流行，很多上层数据库和应用都围绕Prometheus开发了监控exporter agent，此方案将可视化监控数据源统一为Prometheus。
我们可以将nimon采集AIX，VIOS，Linux性能指标数据push到Influxdata（含InfluxDB+Telegraf）中的监控数据，Prometheus经Telegraf  pull监控数据。
然后Grafana配置Prometheus数据源实时读取监控数据，来实现性能指标实时可视化监控。并通过Prometheus的Alert Manager组件实现告警。如下图示：

![image](https://github.com/DBres4Power/monitor_K1Power_AIX_Linux/blob/main/Solution_2_Nimon%2BInfluxData%2BPrometheus%2BGrafana/Solution_2.jpg)

# Mail list

lisongqing@inspur.com
