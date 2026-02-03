# k3s


curl -sfL https://get.k3s.io | sh -s - \
  --tls-san atois.ink \
  --bind-address 0.0.0.0 \
  --advertise-address $(curl -s ifconfig.me)
  
