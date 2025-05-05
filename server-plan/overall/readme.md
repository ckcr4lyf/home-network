## Reference

On a fresh boot of my server, I need to do following:

### Start OpenVPN / Wireguard connections

Need to start the OpenVPN connections for the 10.8.0.0/24 (tun1) interfaces.

### Bring up interfaces, route them

#### First: Netgear Wifi AP should be connected to this port (physically left port when viewed from the back)

```sh
wg-quick up wg2
ip addr add 192.168.30.1/24 dev enp5s0f0
ip link set enp5s0f0 up

ip route add table 69 to 192.168.30.0/24 dev enp5s0f0 metric 10
ip route add table 69 to 10.69.0.0/24 dev wg2 metric 100
ip route add table 69 to default via 10.69.0.1 dev wg2 metric 100
ip rule add from 192.168.30.0/24 table 69 priority 70
ip rule add from 10.69.0.2 table 69 priority 69
iptables -A POSTROUTING -s 192.168.30.0/24 -o wg2 -j MASQUERADE -t nat
```

#### Second: TP-Link Wifi AP should be connected to this port (physically left port when viewed from the back)

```sh
ip addr add 192.168.60.1/24 dev enp5s0f1
ip link set enp5s0f1 up

ip route add table 79 to 192.168.60.0/24 dev enp5s0f1 metric 10
ip route add table 79 to 10.8.0.0/24 dev tun1 metric 100
ip route add table 79 to default via 10.8.0.101 dev tun1 metric 100
ip rule add from 192.168.60.0/24 table 79 priority 80
iptables -A POSTROUTING -s 192.168.60.0/24 -o tun1 -j MASQUERADE -t nat
```

#### Third: Wireguard stuffs:

```sh
wg-quick up wg0
ip route add table 91 to 10.10.0.0/24 dev wg0 metric 100
ip route add table 91 to default via 10.10.0.1 dev wg0 metric 100
ip rule add from 10.10.0.2 table 91 priority 91
```

To limit speed on the interface (e.g. for OVH DDoS "protection"):

```sh
sudo tc qdisc add dev wg0 root tbf rate 180mbit latency 50ms burst 409600*
```

### Block Smart Bulb

BLock my Tuya "smart bulb" from accessing the internet:

```
iptables -A FORWARD -s 192.168.30.107/32 -j DROP
```

### Enable IP Forwrding, restart DHCP service

```sh
sysctl -w net.ipv4.ip_forward=1
iptables -t nat -A POSTROUTING -o enp6s0 -j MASQUERADE
systemctl restart isc-dhcp-server.service
```
