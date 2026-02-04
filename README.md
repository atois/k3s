
root@10-35-156-170:~# ls /etc/systemd/
journald.conf  logind.conf  network  networkd.conf  pstore.conf  resolved.conf  sleep.conf  system  system.conf  system.conf.d  timesyncd.conf  timesyncd.conf.d  user  user.conf
root@10-35-156-170:~# mkdir -p /etc/systemd/journald.conf.d/
root@10-35-156-170:~# cat > /etc/systemd/journald.conf.d/limits.conf << 'EOF'
[Journal]
SystemMaxUse=500M
RuntimeMaxUse=100M
EOF
root@10-35-156-170:~# systemctl restart systemd-journald
root@10-35-156-170:~# journalctl --unit=systemd-journald | grep "max" | tail -n 1
Feb 04 16:15:33 10-35-156-170 systemd-journald[22399]: System Journal (/var/log/journal/b1f65513bae2a6ed833eb6ded5d197db) is 8.0M, max 500.0M, 491.9M free.



针对您提出的 Debian 12 (Bookworm) 优化要求，以下是具体的实施步骤。由于 Debian 12 默认内核（6.1.x）已经编译了 BBR 模块，我们无需额外安装，只需通过 `sysctl` 启用并调整相关参数即可。

建议以 `root` 权限执行以下操作：

### 1. 启用 TCP BBR 拥塞控制

BBR 对提升云主机在高延迟/丢包环境下的吞吐量效果显著。

```bash
# 验证内核是否支持（应返回 y 或 m）
grep CONFIG_TCP_CONG_BBR /boot/config-$(uname -r)

# 写入配置
cat >> /etc/sysctl.conf << EOF

# 启用 BBR
net.core.default_qdisc = fq
net.ipv4.tcp_congestion_control = bbr
EOF

```

### 2. 内存与虚拟内存 (VM) 优化

针对您 8GB 内存且希望减少交换倾向（swappiness）的要求：

```bash
cat >> /etc/sysctl.conf << EOF

# 内存优化
vm.swappiness = 10
vm.vfs_cache_pressure = 50
# 脏页参数：当脏页达到物理内存的 5% 时后台开始异步写回，20% 时阻塞写回
vm.dirty_background_ratio = 5
vm.dirty_ratio = 20
EOF

```

### 3. 网络并发与缓冲区优化

调整 TCP 栈以应对高并发，并增加缓冲区大小以提升网络吞吐：

```bash
cat >> /etc/sysctl.conf << EOF

# 网络缓冲区优化 (16MB)
net.core.rmem_max = 16777216
net.core.wmem_max = 16777216
net.ipv4.tcp_rmem = 4096 87380 16777216
net.ipv4.tcp_wmem = 4096 65536 16777216

# 高并发连接处理
net.core.somaxconn = 8192
net.core.netdev_max_backlog = 5000
net.ipv4.tcp_max_syn_backlog = 8192
net.ipv4.tcp_max_tw_buckets = 2000000
net.ipv4.tcp_tw_reuse = 1
net.ipv4.tcp_fin_timeout = 15

# 连接跟踪优化 (针对可能存在的防火墙环境)
net.netfilter.nf_conntrack_max = 262144
net.netfilter.nf_conntrack_tcp_timeout_established = 1200
EOF

```

### 4. 应用配置

执行以下命令使上述所有 `sysctl` 修改立即生效：

```bash
sysctl -p

```

### 5. 验证优化结果

您可以运行以下命令检查关键参数是否已按预期更改：

* **验证 BBR：**
```bash
sysctl net.ipv4.tcp_congestion_control
# 应输出: net.ipv4.tcp_congestion_control = bbr
lsmod | grep bbr

```


* **验证 Swappiness：**
```bash
cat /proc/sys/vm/swappiness
# 应输出: 10

```



### 补充建议：

由于您目前没有 Swap 分区，虽然 `swappiness` 设置为 10 会极大减少交换，但如果未来内存耗尽，系统会直接触发 **OOM Killer** 强制杀死进程。
**建议：** 即使内存充足，也可以创建一个 1-2GB 的 Swap 文件作为“安全垫”，配合上述 `swappiness=10` 的设置，既能保证性能，又能提升极端情况下的系统稳定性。

```bash
# 创建 2GB Swap (可选)
fallocate -l 2G /swapfile
chmod 600 /swapfile
mkswap /swapfile
swapon /swapfile
echo '/swapfile none swap sw 0 0' >> /etc/fstab

```


# k3s


curl -sfL https://get.k3s.io | sh -s - \
  --tls-san atois.ink \
  --bind-address 0.0.0.0 \
  --advertise-address $(curl -s ifconfig.me)
  
 cat /etc/rancher/k3s/k3s.yaml 


