#### 安装
根据[中文文档](https://prometheus.fuckcloudnative.io/di-san-zhang-prometheus/di-2-jie-an-zhuang/gettingstarted)安装

Prometheus 业务监控平台
通常一个监控系统主要包含 采集（信息源：log、metrics）、上报（协议:http、tcp）、聚合、存储、可视化以及告警等等。其中采集上报主要是客户端的核心功能，一般有定期外围探测的（早期的Nagios、Zabbix）、AOP方式手动织入代码的（埋点）、字节码自动织入等方式（无埋点）。
