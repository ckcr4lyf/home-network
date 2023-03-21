## Reference

On a fresh boot of my server, I need to do following:

### Start OpenVPN connections

Need to start the OpenVPN connections for the 10.16.0.0/24 (tun0) & 10.8.0.0/24 (tun1) interfaces.

### Bring up interfaces, route them

First: Netgear Wifi AP should be connected to this port (physically left port when viewed from the back)

```sh
ip addr add 192.168.30.1/24 dev enp5s0f0
ip link set enp5s0f0 up

ip route add table 69 to 192.168.30.0/24 dev enp5s0f0 metric 10
ip route add table 69 to 10.16.0.0/24 dev tun0 metric 100
ip route add table 69 to default via 10.16.0.1 dev tun0 metric 100
ip rule add from 192.168.30.0/24 table 69 priority 70
iptables -A POSTROUTING -s 192.168.30.0/24 -o tun0 -j MASQUERADE -t nat
```

Second: TP-Link Wifi AP should be connected to this port (physically left port when viewed from the back)

```sh
ip addr add 192.168.60.1/24 dev enp5s0f1
ip link set enp5s0f1 up

ip route add table 79 to 192.168.60.0/24 dev enp5s0f1 metric 10
ip route add table 79 to 10.8.0.0/24 dev tun1 metric 100
ip route add table 79 to default via 10.8.0.101 dev tun1 metric 100
ip rule add from 192.168.60.0/24 table 79 priority 80
iptables -A POSTROUTING -s 192.168.60.0/24 -o tun1 -j MASQUERADE -t nat
```

Third: IP rule for local interface on VPN to make outgoing connections via VPN

```sh
ip rule add from 10.16.0.7 table 69 priority 71
```

### Enable IP Forwrding, restart DHCP service

```sh
sysctl -w net.ipv4.ip_forward=1
iptables -t nat -A POSTROUTING -o enp6s0 -j MASQUERADE
systemctl restart isc-dhcp-server.service
```
