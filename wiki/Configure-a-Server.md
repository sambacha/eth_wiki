Set up a server by editing `rc.local` and inserting:

```
/home/username/serv.sh &
```

And then writing the file accordingly:

```
cat > /home/username/serv.sh << EOF
#!/bin/bash
cd /home/username/cpp-ethereum/build/eth
while [ 1 ]; do
  HOME=/home/username sudo -u username ./eth -o peer -x 256 -l 30303 -m off -v 1 > /home/username/eth.log
  mv /home/username/eth.log /home/username/eth.log-\$(date +%F_%T)
done
EOF
chmod +x /home/username/serv.sh
```

**Note:** Anywhere where it says `username` you can change it to your own home directory

Only ubuntu users - improve perfomance on the you nodes. 
Сreate a file optimize.sh in home directory:
`nano optimize.sh
`
insert into this script:
```#!/bin/bash
# HDD if memory >=2 GB
echo 'vm.swappiness=10' >> /etc/sysctl.conf #set up 1 if this VPS
echo 'vm.vfs_cache_pressure=1000' >> /etc/sysctl.conf
echo 'vm.dirty_background_ratio = 10' >> /etc/sysctl.conf
echo 'vm.dirty_ratio = 10' >> /etc/sysctl.conf
echo 'vm.laptop_mode = 5' >> /etc/sysctl.conf
sudo sync
echo 3 > /proc/sys/vm/drop_caches
# network
echo 'net.core.netdev_max_backlog = 10000' >> /etc/sysctl.conf
echo 'net.core.somaxconn=65535' >> /etc/sysctl.conf
echo 'net.ipv4.tcp_syncookies=1' >> /etc/sysctl.conf
echo 'net.ipv4.tcp_max_syn_backlog = 262144' >> /etc/sysctl.conf
echo 'net.ipv4.tcp_max_tw_buckets = 720000' >> /etc/sysctl.conf
echo 'net.ipv4.tcp_tw_recycle = 1' >> /etc/sysctl.conf
echo 'net.ipv4.tcp_timestamps = 1' >> /etc/sysctl.conf
echo 'net.ipv4.tcp_tw_reuse = 1' >> /etc/sysctl.conf
echo 'net.ipv4.tcp_fin_timeout = 30' >> /etc/sysctl.conf
echo 'net.ipv4.tcp_keepalive_time = 1800' >> /etc/sysctl.conf
echo 'net.ipv4.tcp_keepalive_probes = 7' >> /etc/sysctl.conf
echo 'net.ipv4.tcp_keepalive_intvl = 30' >> /etc/sysctl.conf
echo 'net.core.wmem_max = 33554432' >> /etc/sysctl.conf
echo 'net.core.rmem_max = 33554432' >> /etc/sysctl.conf
echo 'net.core.rmem_default = 8388608' >> /etc/sysctl.conf
echo 'net.core.wmem_default = 4194394' >> /etc/sysctl.conf
echo 'net.ipv4.tcp_rmem = 4096 8388608 16777216' >> /etc/sysctl.conf
echo 'net.ipv4.tcp_wmem = 4096 4194394 16777216' >> /etc/sysctl.conf
#memory
page_size=`getconf PAGE_SIZE`
phys_pages=`getconf _PHYS_PAGES`
shmall=`expr $phys_pages / 2`
shmmax=`expr $shmall \* $page_size`
echo kernel.shmmax = $shmmax >> /etc/sysctl.conf
echo kernel.shmall = $shmall >> /etc/sysctl.conf
sysctl -p
```
Then run:
`sudo sh optimize.sh`
Add to /etc/network/interfaces next line:
`up ifconfig $IFACE txqueuelen 10000
`
If you have made changes to interfaces, it will be necessary to restart the service or restart the computer

