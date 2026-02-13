---
title: 运维监控Promethues
abbrlink: 989fe672
date: 2026-02-12 10:54:48
tags:
---

# 一、Promethues安装

1.1  prometheus下载页面地址

    https://prometheus.io/download/ 
    
1.2 解压
    
    tar -zxvf prometheus-2.48.0-rc.0.linux-amd64.tar.gz
    
1.3 移动软件并修改名称
    
    mv /usr/local/prometheus-2.48.0-rc.0.linux-amd64/  /usr/local/prometheus
    
1.4创建prometheus的用户及数据存储目录
    
    useradd  -s /sbin/nologin -M prometheus
    mkdir  /data/prometheus -p
    
修改目录属主

    chown -R prometheus:prometheus /usr/local/prometheus/
    chown -R prometheus:prometheus /data/prometheus/

1.5 添加prometheus服务【路径根据实际情况修改】

    vim /etc/systemd/system/prometheus.service

```
[Unit]
Description=Prometheus Server
Documentation=https://prometheus.io
After=network.target

[Service]
Type=simple
User=prometheus
ExecStart=/usr/local/prometheus/prometheus \
--config.file=/usr/local/prometheus/prometheus.yml \
--storage.tsdb.path=/usr/local/prometheus/data/ \
--storage.tsdb.retention=15d \
--web.enable-lifecycle

ExecReload=/bin/kill -HUP $MAINPID
Restart=on-failure

[Install]
```

# 二、Promethues监控配置


配置文件
/usr/local/prometheus/prometheus.yml

数据库持久化目录
/usr/local/prometheus/data


2.2配置文件解释

报警配置
```
alerting:
alertmanagers:
- static_configs:
- targets:
#报警服务的监听端口
# - alertmanager:9093
```


报警规则
```
rule_files:
- "alertrules/*.yml"
# - "second_rules.yml"

# A scrape configuration containing exactly one endpoint to scrape:
# 监控对象配置
scrape_configs:
# The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
- job_name: "prometheus"

  # metrics_path defaults to '/metrics'
  # scheme defaults to 'http'.

  static_configs:
    - targets: ["192.168.100.235:9090"]
      #分组名称
- job_name: "agent"
  static_configs:
  #node_exporter点的ip端口号
  -targets: ["192.168.100.202:9100"]
```


# 三、Promethues报警配置

```
groups:
  - name: node-alert
    rules:
    - alert: 主机停止运行
      expr: up{job="node_info"} == 0
      for: 15s
      labels:
        severity: 1
        nodename: "{{ $labels.app }}"
      annotations:
        summary: "{{ $labels.app }}已停止运行超过15s!"
        description: ""
    - alert: 主机内存使用率过高
      expr: (1 - (node_memory_MemAvailable_bytes / (node_memory_MemTotal_bytes))) * 100 > 90
      for: 10s # 告警持续时间，超过这个时间才会发送给alertmanager
      labels:
        severity: warning
        nodename: "{{ $labels.app }}"
      annotations:
        summary: "服务器实例 {{ $labels.app }}内存使用率过高"
        description: "{{ $labels.app }}的内存使用率超过90%，当前使用率[{{ $value }}]."
    - alert: 主机cpu使用率过高
      expr: 100-avg(irate(node_cpu_seconds_total{mode="idle"}[5m])) by(instance)*100 > 80
      for: 1m
      labels:
        severity: warning
        nodename: "{{ $labels.app }}"
      annotations:
        summary: "服务器实例 {{ $labels.app }} cpu使用率过高"
        description: "{{ $labels.app }}的cpu使用率超过80%,当前使用率[{{ $value }}]."
    - alert: 主机磁盘使用率过高
      expr: 100 - node_filesystem_avail_bytes{fstype=~"ext4|xfs",mountpoint="/"}  * 100 / node_filesystem_size_bytes{fstype=~"ext4|xfs",mountpoint="/"} > 80
      for: 1m
      labels:
        severity: warning
        nodename: "{{ $labels.app }}"
      annotations:
        summary: "服务器实例 {{ $labels.app }} 磁盘使用率过高"
        description: "{{ $labels.app }}的disk使用率超过80%,当前使用率[{{ $value }}]."
    - alert: 主机磁盘写过大
      expr: sum by (instance) (rate(node_disk_written_bytes_total[2m])) > 50 * 1024 * 1024
      for: 5m
      labels:
         severity: warning
      annotations:
        summary: "磁盘写过大, 实例: {{$labels.instance}}，当前值: {{ $value | humanize1024 }}。"
    - alert: 主机iowait较高
      expr: (sum(increase(node_cpu_seconds_total{mode='iowait'}[5m]))by(instance)) / (sum(increase(node_cpu_seconds_total[5m]))by(instance))  *100 >= 10
      for: 5m
      labels:
        severity: warning
      annotations:
        summary: "CPU ioWait近5分钟占比大于等于10%, 实例: {{ $labels.instance }}，当前值：{{ $value }}%"
    - alert: 主机Tcp TimeWait数量过多告警
      expr: node_sockstat_TCP_tw >= 5000
      for: 1m
      labels:
         severity: warning
      annotations:
        summary: "Tcp TimeWait数量大于5000, 实例: {{ $labels.instance }}，当前值：{{ $value }}%"
    - alert: 硬盘inode使用率过高
      expr: node_filesystem_files_free{mountpoint ="/rootfs"} / node_filesystem_files{mountpoint ="/rootfs"} * 100 < 10
      for: 5m
      labels:
        severity: warning
      annotations:
        summary: "Host out of inodes (instance {{ $labels.instance }})"
        description: "Disk is almost running out of available inodes (< 10% left)n  VALUE = {{ $value }}n  LABELS: {{ $labels }}"
    - alert: HostUnusualNetworkThroughputOut 
      expr: sum by (instance) (rate(node_network_transmit_bytes_total[2m])) / 1024 / 1024 > 10 
      for: 5m 
      labels: 
        severity: warning 
      annotations: 
        summary: "Host unusual network throughput out (instance {{ $labels.instance }})" 
        description: "Host network interfaces are probably sending too much data (> 10 MB/s)n  VALUE = {{ $value }}n  LABELS: {{ $labels }}"
    - alert: HostUnusualNetworkThroughputIn 
      expr: sum by (instance) (rate(node_network_receive_bytes_total[2m])) / 1024 / 1024 > 10 
      for: 5m 
      labels: 
        severity: warning 
      annotations: 
        summary: "Host unusual network throughput in (instance {{ $labels.instance }})" 
        description: "Host network interfaces are probably receiving too much data (> 10 MB/s)n  VALUE = {{ $value }}n  LABELS: {{ $labels }}"

```


## 3.2 Promethues的alertManager模块安装
官网下载软件包 解压 安装即可
加入系统服务

```
/usr/lib/systemd/system/alertmanager.service
[Unit]
Description=alertmanager System
Documentation=alertmanager System
[Service]
ExecStart=/usr/local/alertmanager/alertmanager --config.file=/usr/local/alertmanager/alertmanager.yml
[Install]
WantedBy=multi-user.target
```


# 四、Grafana 安装

安装官网地址安装

    https://grafana.com/grafana/download

查看服务状态

    systemctl status grafana-server.service

设置服务开机自启动

    systemctl enable grafana-server.service

启动服务

    systemctl start grafana-server.service

关闭服务

    systemctl stop grafana-server.service

登录
安装完成后，在浏览器输入访问网址：http://ip:3000
默认的登录用户名/密码：admin/admin
首次登录后，建议根据导航提示修改密码


好用模版
https://grafana.com/grafana/dashboards/16098-1-node-exporter-for-prometheus-dashboard-cn-0417-job/

# 五、Grafana 监控配置


# 六、Grafana 报警配置