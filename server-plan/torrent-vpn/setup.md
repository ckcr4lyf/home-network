# OpenVPN

Outgoing connection to my VPS ruunning OpenVPN.

Local IP: `10.16.0.7`

Routing:

```
ip route add table 69 to 192.168.30.1 dev enp5s0f0 metric 10
ip route add table 69 to 10.16.0.0/24 dev tun0 metric 100
ip route add table 69 to default via 10.16.0.1 dev tun0 metric 100
```