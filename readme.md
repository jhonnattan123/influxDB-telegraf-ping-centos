# Telegraf + InfluxDB + Grafana

## Prepare your server

Depending on the environment that your servers are in, you should update and install all the essential packages that you usually use such as text editor (vim or nano), wget or curl and others that you cannot do without.

```console
sudo yum update
sudo yum install vim wget curl -y
```

## Installing InfluxDB on CentOS 7

Influxdata provides the repository for installing InfluxDB and telegraf on CentOS 7. To add the repository to your system, use the commands:
```console

cat <<EOF | sudo tee /etc/yum.repos.d/influxdb.repo
[influxdb]
name = InfluxDB Repository - RHEL \$releasever
baseurl = https://repos.influxdata.com/rhel/7/x86_64/stable/
enabled = 1
gpgcheck = 1
gpgkey = https://repos.influxdata.com/influxdb.key
EOF
```

Update cache to confirm that the repository is working fine:
```console

sudo yum makecache fast
```
Then install influxDB:
```console

sudo yum -y install influxdb vim curl
```

Start and enable the service:

```console
sudo systemctl start influxdb && sudo systemctl enable influxdb
```

install firewalld
```console

yum install firewalld
sudo systemctl unmask firewalld
sudo systemctl enable firewalld
sudo systemctl start firewalld
```


Configure InfluxDB firewall
By default, InfluxDB uses the following network ports:
TCP port 8086 is used for client-server communication over InfluxDB’s HTTP API
TCP port 8088 is used for the RPC service for backup and restore.
To open it on the firewall, use the command:
```console

sudo firewall-cmd --add-port=8086/tcp --permanent
sudo firewall-cmd --reload
```

Port mappings can be modified by changing the file /etc/influxdb/influxdb.conf. Since all is configured now, we can start the service now.
```console

sudo systemctl start influxdb && sudo systemctl enable influxdb
```

## Install the Telegraf agent in Centos 7

Telegraf is a service that collects and sends metrics, and then sends them to other systems. It can collect data from the system it is running on, such as disk usage, RAM, CPU, system load, connections and many more, and it also includes a growing list of input plugins, such as apache, consul, couchDB, Docker, Elast

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

```console
sudo systemctl restart telegraf
```

validate status telegraf:

```console
sudo systemctl status telegraf
```
expected:
```console
● telegraf.service - The plugin-driven server agent for reporting metrics into InfluxDB
   Loaded: loaded (/usr/lib/systemd/system/telegraf.service; enabled; vendor preset: disabled)
   Active: active (running) since Wed 2021-08-18 13:12:27 UTC; 19min ago
     Docs: https://github.com/influxdata/telegraf
 Main PID: 20289 (telegraf)
   CGroup: /system.slice/telegraf.service
           └─20289 /usr/bin/telegraf -config /etc/telegraf/telegraf.conf -config-directory /etc/telegraf/telegraf....


```
It is necessary to remember that we must also open the input ports on our host
if everything is ok, we should be able to connect our influxDB to grafana, choosing the database called telegraf

## References (APA)



- Install Grafana and InfluxDB on CentOS 7. (2021, August 17). computingforgeeks.com.
https://computingforgeeks.com/install-grafana-and-influxdb-on-centos-7/

- How To Configure NTP Server Using Chrony on RHEL 8 / CentOS 8. (2021, August 17). computingforgeeks.com.
https://computingforgeeks.com/how-to-configure-ntp-server-using-chrony-on-rhel-8/
<br>

- Cómo instalar y configurar el agente de Telegraf en RHEL 7 y Centos 7. (2021, August 17). ochobitshacenunbyte.com
https://www.ochobitshacenunbyte.com/2019/09/18/como-instalar-y-configurar-el-agente-de-telegraf-en-rhel-7-y-centos-7/
<br>

- Ping Input Plugin. (2021, August 17). github.com
https://github.com/influxdata/telegraf/tree/master/plugins/inputs/ping
<br>

- CentOS 7 firewall-cmd not found. (2021, August 17). serverfault.com 
https://serverfault.com/questions/710076/centos-7-firewall-cmd-not-found