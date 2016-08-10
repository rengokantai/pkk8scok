#### pkk8scok
#####Chapter1: Building Your Own Kubernetes
install etcd  
u15
```
apt install etcd
service stop etcd
etcd -name ke --data-dir /tmp.ke.etcd &
```
use another machine, connect
```
etcdctl set ke "ke"
etcd get ke
```
use curl
```
curl -L http://localhost:2379/v2/keys/ke
```
put/update
```
curl http://localhost:2379/v2/keys/ke -X PUT -d value="ke2"
```
delete
```
curl http://localhost:2379/v2/keys/ke?recursive=true -X DELETE
```
note, an key could be multiple lavel,like
```
/ke/ke1/ke2
```
the above delete recursive=true still apply

######enalbe startup service
in c7,u15+,use systemd-based
```
vim /lib/systemd/system/etcd.service
```
point to
```
systemctl enable etcd
```

create /etc/etcd/etcd.conf
```
cd /etc
mkdir etcd
cd etcd && vim etcd.conf
```
edit
```
export ETCD_NAME=ke
export ETCD_DATA_DIR="/tmp/ke.etcd"
export ETCD_LISTEN_CLIENT_URLS="http://0.0.0.0/8080"
export ETCD_ADVERTISE_CLIENT_URLS="http://localhost:8080"
```

#####install flannel on u16
```
apt-get install linux-libc-dev golang gcc -y
```

```
vim ke.json
```
edit
```
{
"Network":"10.136.0.0/16",
"SubnetLen":28
"SubnetMin":"10.136.16.0",
"SubnetMax":"10.136.99.0",
"Backend":{
        "Type":"udp",
        "Port":7890
}
}
```
then create service file in system
```
vim /lib/systemd/system/flanneld.service
```
edit
```
[Unit]
Description=Flannel
Wants=etcd.service
After=etcd.service
Before=docker.service

[Service]
Type=Notify

EnvironmentFile=/etc/sysconfig/flanneld
EnvironmentFile=-/etc/sysconfig/docker-network
ExecStart=/usr/bin/flanneld -etcd-endpoints=${FLANNEL_ETCD} -etcd-prefix=${FLANN
EL_ETCD_KEY} $FLANNEL_OPTIONS
Restart=on-failure

RestartSec=5s

[Install]
WantedBy=multi-user.target
```
then create flanneld:
```
cd /etc && mkdir sysconfig && cd sysconfig && vim flanneld
```
FLANNEL_ETCD="http://localhost:2379"
FLANNEL_ETCD_KEY="/coreos.com/network"
```
