## Reference

On a fresh boot of my server, I need to do following:

### Wireguard to NL Server (on enp5s0f0)

Netgear Wifi AP should be connected to this port (physically left port when viewed from the back)

This config (`wg2.conf`) is responsible for bringing up the WG connection to my NL VPS, but with no default routing.
The `PostUp` rules will add rule based routing so everything on the `192.168.30.0/24` subnet (clients connected to Netgear AP) will be routed over the VPN.
It also adds rule based routing for the client wireguard IP `10.69.0.2` , which is what qBittorrent will bind to.

Enabling (one-time)
```sh
sudo systemctl enable --now wg-quick@wg2
```

### (OLD) Second: TP-Link Wifi AP should be connected to this port (physically left port when viewed from the back)

```sh
ip addr add 192.168.60.1/24 dev enp5s0f1
ip link set enp5s0f1 up

ip route add table 79 to 192.168.60.0/24 dev enp5s0f1 metric 10
ip route add table 79 to 10.8.0.0/24 dev tun1 metric 100
ip route add table 79 to default via 10.8.0.101 dev tun1 metric 100
ip rule add from 192.168.60.0/24 table 79 priority 80
iptables -A POSTROUTING -s 192.168.60.0/24 -o tun1 -j MASQUERADE -t nat
```

### Third: Other Wireguard stuffs

Wireguard to my OVH VPS running my mail server and some other stuff. No default routing.

Enabling (one-time)
```sh
sudo systemctl enable --now wg-quick@wg0
```

To limit speed on the interface (e.g. for OVH DDoS "protection"):

```sh
sudo tc qdisc add dev wg0 root tbf rate 180mbit latency 50ms burst 409600*
```

### Block Smart Bulb

Block my Tuya "smart bulb" from accessing the internet:

```sh
iptables -A FORWARD -s 192.168.30.107/32 -j DROP
```

### Enable IP Forwrding, restart DHCP service

```sh
sysctl -w net.ipv4.ip_forward=1
iptables -t nat -A POSTROUTING -o enp6s0 -j MASQUERADE
systemctl restart isc-dhcp-server.service
```
