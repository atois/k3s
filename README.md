userdata

IyEvYmluL2Jhc2gKCiMgMS4g5riF55CG5bm26K6+572uIFNTSCDnq6/lj6MKIyDliKDpmaTmiYDmnInku6UgUG9ydCDlvIDlpLTnmoTooYzvvIjml6DorrrmmK/lkKbmnInms6jph4rvvInvvIznhLblkI7ov73liqDmlrDnq6/lj6MKc2VkIC1pICcvXiNcP1BvcnQvZCcgL2V0Yy9zc2gvc3NoZF9jb25maWcKZWNobyAiUG9ydCAyNjgzOCIgPj4gL2V0Yy9zc2gvc3NoZF9jb25maWcKCiMgMi4g5riF55CG5bm26K6+572uIOi9rOWPkeWKn+iDvQojIEFsbG93VGNwRm9yd2FyZGluZzog5Z+656GA6L2s5Y+RCiMgR2F0ZXdheVBvcnRzOiDlhYHorrjov5znqIvkuLvmnLrov57mjqXovazlj5Hnq6/lj6MKc2VkIC1pICcvXiNcP0FsbG93VGNwRm9yd2FyZGluZy9kJyAvZXRjL3NzaC9zc2hkX2NvbmZpZwpzZWQgLWkgJy9eI1w/R2F0ZXdheVBvcnRzL2QnIC9ldGMvc3NoL3NzaGRfY29uZmlnCmVjaG8gIkFsbG93VGNwRm9yd2FyZGluZyB5ZXMiID4+IC9ldGMvc3NoL3NzaGRfY29uZmlnCmVjaG8gIkdhdGV3YXlQb3J0cyB5ZXMiID4+IC9ldGMvc3NoL3NzaGRfY29uZmlnCgojIDMuIOmHjeWQr+acjeWKoQpzeXN0ZW1jdGwgcmVzdGFydCBzc2hk


#!/bin/bash
# 1. 清理并设置 SSH 端口
# 删除所有以 Port 开头的行（无论是否有注释），然后追加新端口
sed -i '/^#\?Port/d' /etc/ssh/sshd_config
echo "Port 26838" >> /etc/ssh/sshd_config
# 2. 清理并设置 转发功能
# AllowTcpForwarding: 基础转发
# GatewayPorts: 允许远程主机连接转发端口
sed -i '/^#\?AllowTcpForwarding/d' /etc/ssh/sshd_config
sed -i '/^#\?GatewayPorts/d' /etc/ssh/sshd_config
echo "AllowTcpForwarding yes" >> /etc/ssh/sshd_config
echo "GatewayPorts yes" >> /etc/ssh/sshd_config
# 3. 重启服务
systemctl restart sshd


# k3s


curl -sfL https://get.k3s.io | sh -s - \
  --tls-san atois.ink \
  --bind-address 0.0.0.0 \
  --advertise-address $(curl -s ifconfig.me)
  
 cat /etc/rancher/k3s/k3s.yaml 


