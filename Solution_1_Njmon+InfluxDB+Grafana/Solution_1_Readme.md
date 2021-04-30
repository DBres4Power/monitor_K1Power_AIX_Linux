# 可视化监控方案一: Njmon+InfluxDB+Grafana

![image](https://github.com/DBres4Power/monitor_Power_AIX_Linux/blob/main/Solution_1_Njmon%2BInfluxDB%2BGrafana/Solution_1.jpg)

# 方案实施步骤

# 1. 安装与配置influxdb

# 1.1 安装influxdb

从influxdb　ppc64le版本下载链接：https://www.power-devops.com/influxdb 下载直接rpm安装即可：

$ rpm -Uvh influxdb-1.8.4-1.el7.ppc64le.rpm

查看InfluxDB版本

$ influx -version

# 1.2 启动并验证influxdb服务

$ systemctl start influxdb

$ systemctl status -l influxdb

# 1.3 创建njmon库, 添加管理员用户

$ **influx**
Connected to http://localhost:8086 version 1.8.4
InfluxDB shell version: 1.8.4

\> create database nimon

\> create user "admin" with password 'admin' with all privileges

\> exit

# 1.4 启用认证并重启InfluxDB

uncomment auth-enabled

$ vi /etc/influxdb/influxdb.conf

auth-enabled = true

$ systemctl stop influxdb

$ systemctl start influxdb

# 2. 配置nimon监控数据入库

# 2.1	K1 Power Linux平台监控采集入库

从https://sourceforge.net/projects/nmon/files/下载njmon_linux_binaries_v71.zip，解压并安装njmon & nimon:

$ unzip njmon_linux_binaries_v71.zip

$ sh ninstall

$ crontab -e
1 0 * * *  /usr/local/bin/nimon -s 5 -c 17280 -k -P -b -r -n -H  -i 172.16. xxx.xxx -p 8086 -x njmon -y admin -z admin

# 2.2	K1 Power AIX平台监控采集入库

从https://sourceforge.net/projects/nmon/files/下载njmon_aix_binaries_v73.zip，解压并安装njmon & nimon:

$ unzip njmon_aix_binaries_v73.zip

$ sh ninstall

$ crontab -e

1 0 * * *  /usr/lbin/nimon -s 5 -c 17280 -k -P -b -r -n -H  -i 172.16.xxx.xxx -p 8086 -x njmon -y admin -z admin


# 3. 安装部署Grafana

# 3.1 安装Grafana
下载grafana $ wget -c https://downloads.power-devops.com/grafana-7.3.7-1.el7.ppc64le.rpm

安装grafana

$  rpm -Uvh grafana-7.3.7-1.el7.ppc64le.rpm

启动grafana服务

$ systemctl daemon-reload

$ systemctl start grafana-server

$ systemctl status -l grafana-server

登录grafana，缺省用户/密码admin/admin

http://xxx.xxx.xxx.xxx:3000

# 3.2	配置InfluxDB数据源, 配置dashboard

* [详见：nimon+InfluxDB+Prometheus+Grafana实现AIX与Linux性能指标实时可视化监控](https://github.com/DBres4Power/monitor_K1Power_AIX_Linux/releases/download/nimon_solution/Nimon.InfluxDB.Prometheus.Grafana_Solutions.pdf)
