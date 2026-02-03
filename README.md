userdata

IyEvYmluL2Jhc2gKIyDkv67mlLlTU0jnq6/lj6PkuLoyNjgzOApzZWQgLWkgJ3MvXiNQb3J0IDIyL1BvcnQgMjY4MzgvJyAvZXRjL3NzaC9zc2hkX2NvbmZpZwpzZWQgLWkgJ3MvXlBvcnQgWzAtOV0qL1BvcnQgMjY4MzgvJyAvZXRjL3NzaC9zc2hkX2NvbmZpZwppZiAhIGdyZXAgLXEgJ15Qb3J0IDI2ODM4JyAvZXRjL3NzaC9zc2hkX2NvbmZpZzsgdGhlbgogICAgZWNobyAnUG9ydCAyNjgzOCcgPj4gL2V0Yy9zc2gvc3NoZF9jb25maWcKZmkKc3lzdGVtY3RsIHJlc3RhcnQgc3NoZA==



#!/bin/bash
# 修改SSH端口为26838
sed -i 's/^#Port 22/Port 26838/' /etc/ssh/sshd_config
sed -i 's/^Port [0-9]*/Port 26838/' /etc/ssh/sshd_config
if ! grep -q '^Port 26838' /etc/ssh/sshd_config; then
    echo 'Port 26838' >> /etc/ssh/sshd_config
fi
systemctl restart sshd

# k3s


curl -sfL https://get.k3s.io | sh -s - \
  --tls-san atois.ink \
  --bind-address 0.0.0.0 \
  --advertise-address $(curl -s ifconfig.me)
  
 cat /etc/rancher/k3s/k3s.yaml 
