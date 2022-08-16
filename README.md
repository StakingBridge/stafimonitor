# Monitoring your StaFi and StaFiHub Validator 

### Monitor your CPU, RAM, Network and StaFi Chain stats

This solution uses Telegraf, Prometheus and Grafana to provide users and node managers a monitoring tool to analyze CPU, RAM, network interfaces and I/O wait along with metrics from Stafi Chain that will be displayed in the public dashboard. Why should you monitor your node using [the public dashboard](https://stats.stakingbridge.com/d/stafimonitor/stafi?orgId=1)?	
- Control the use of resources in your server.
![](https://github.com/StakingBridge/stafimonitor/blob/main/images/fast_monitor.png?raw=true)
- It allows you to detect problems, even before they happen.
- Maximizes the security of the StaFi network and minimizes the risk of slashing.
- Transparency: allows everyone to see how stable your validator works.

![](https://github.com/StakingBridge/stafimonitor/blob/main/images/network_monitoring.png?raw=true)

### 1. INSTALL TELEGRAF IN YOUR NODE SERVER

**UBUNTU 20.04**

```

wget -qO- https://repos.influxdata.com/influxdb.key | sudo tee /etc/apt/trusted.gpg.d/influxdb.asc >/dev/null source /etc/os-release
echo "deb https://repos.influxdata.com/$%7BID%7D ${VERSION_CODENAME} stable" | sudo tee /etc/apt/sources.list.d/influxdb.list
sudo apt-get update && sudo apt-get install telegraf
apt update
apt install telegraf

```

### 2. ADD STAFI NODE AND BASIC CONFIGURATION TO TELEGRAF



Modify the file /etc/telegraf/telegraf.conf and use this 👉[config file](https://github.com/StakingBridge/stafimonitor/blob/main/telegraf.conf).

The file will be such that:

```
# Global Agent Configuration
[agent]
  hostname = "YOUR_NODE_ALIAS" # set this to a name you want to identify your node in the grafana dashboard
  flush_interval = "15s"
  interval = "15s"
# Input Plugins
[[inputs.cpu]]
    percpu = true
    totalcpu = true
    collect_cpu_time = false
    report_active = false
[[inputs.disk]]
    ignore_fs = ["devtmpfs", "devfs"]
[[inputs.io]]
[[inputs.mem]]
[[inputs.net]]
[[inputs.system]]
[[inputs.swap]]
[[inputs.netstat]]
[[inputs.processes]]
[[inputs.kernel]]
[[inputs.diskio]]
[[inputs.prometheus]]
#   ## An array of urls to scrape metrics from.
  urls = ["http://localhost:9615"]
[[inputs.prometheus]]
  urls = ["http://localhost:26660"]
# Output Plugin InfluxDB
[[outputs.influxdb]]
  database = "stafi"
  urls = [ "https://stats.stakingbridge.com:8086" ] 
  username = "stafi"
  password = "stafipassword"

```

### 3. CONFIGURE STAFIHUB AND ENABLE PROMETHEUS FOR MONITORING

Modify /home/*your_username*/.stafihub/config/config.toml and edit this three options:
```
[instrumentation]
# When true, Prometheus metrics are served under /metrics on
prometheus = true
# Address to listen for Prometheus collector(s) connections
prometheus_listen_addr = "127.0.0.1:26660"
# Instrumentation namespace
namespace = "tendermint_testnet"
```
### 4. LAUNCH TELEGRAF AND MONITOR YOUR NODE!

- To launch Telegraf: **sudo systemctl start telegraf**
- Visit [https://stafimonitor.stakingbridge.com/](https://stafimonitor.stakingbridge.com/)

> From top side, use the search engine where it says ¨**Node**¨ and look for the alias that you have configured in the telegraf.conf file. For example, searching “Stakingbridge_TR-3970X” shows stats for stakingbridge.com validator.


![](https://github.com/StakingBridge/stafimonitor/blob/main/images/search_nodes.gif?raw=true)




