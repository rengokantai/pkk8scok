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
export ETCD_DATA_DIR="/var/lib/etcd/ke.etcd"
export ETCD_LISTEN_CLIENT_URLS="http://0.0.0.0/8080"
export ETCD_ADVERTISE_CLIENT_URLS="http://localhost:8080"
```
