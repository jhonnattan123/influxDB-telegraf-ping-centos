# Telegraf + InfluxDB + Grafana

## Prepare your server

Depending on the environment that your servers are in, you should update and install all the essential packages that you usually use such as text editor (vim or nano), wget or curl and others that you cannot do without.

```console
sudo yum update
sudo yum install vim wget curl -y
```

## Install the Telegraf agent in Centos 7

Telegraf is a service that collects and sends metrics, and then sends them to other systems. It can collect data from the system it is running on, such as disk usage, RAM, CPU, system load, connections and many more, and it also includes a growing list of input plugins, such as apache, consul, couchDB, Docker, Elast

We add the repository:

```console
sudo vi /etc/yum.repos.d/influxdb.repo
```

We modify content to:

```console
[InfluxDB]
name = InfluxDB Repository - RHEL 
baseurl = https://repos.influxdata.com/rhel/7/x86_64/stable/
enabled = 1
gpgcheck = 1
gpgkey = https://repos.influxdata.com/influxdb.key
```

We save, and install:

```console

sudo yum update
sudo yum install telegraf
```

Once this is done we add the service at startup:

```console
sudo systemctl enable telegraf
```

## Configuration

Setting the service configuration file is located in the path "/etc/telegraf/telegraf.conf", in said file we must change the corresponding URL of our Stack to the corresponding one

```console
sudo vim /etc/telegraf/telegraf.conf
```

modify:

```console
[[outputs.influxdb]]
urls = ["http://localhost:8086"]
database = “telegraf”
```

Once this is done we save and exit

Now we can turn on the service:

```console
sudo systemctl start telegraf
```

if the configuration is correct, we should already see the connection between the hosts through port 8086

configure ping plugin:

```console
sudo vim /etc/telegraf/telegraf.conf
```

```console
[[inputs.ping]]
  ## Hosts to send ping packets to.
  urls = ["http://host-to-ping.com"]
```

## References (APA)

- How To Configure NTP Server Using Chrony on RHEL 8 / CentOS 8. (2021, August 17). computingforgeeks.com.
https://computingforgeeks.com/how-to-configure-ntp-server-using-chrony-on-rhel-8/
<br>

- Cómo instalar y configurar el agente de Telegraf en RHEL 7 y Centos 7. (2021, August 17). ochobitshacenunbyte.com
https://www.ochobitshacenunbyte.com/2019/09/18/como-instalar-y-configurar-el-agente-de-telegraf-en-rhel-7-y-centos-7/
<br>
- Ping Input Plugin. (2021, August 17). github.com
https://github.com/influxdata/telegraf/tree/master/plugins/inputs/ping