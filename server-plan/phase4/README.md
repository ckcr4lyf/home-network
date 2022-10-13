This phase involves doing a lot of phase 1 & 2, as well as OpenVPN setup, again!

### VPN

First, we make the OpenVPN connection. I get the config and just run:

```
sudo openvpn config.ovpn
```

This gives me a new interface `tun1` on the subnet `10.8.0.102/24`.

I also add a new routing table for this VPN connection to the internet, but making sure first that there is a route to the server with higher precedence:

```
ip route add table 79 to 192.168.60.0/24 dev enp5s0f1 metric 10
ip route add table 79 to 10.8.0.0/24 dev tun1 metric 100
ip route add table 79 to default via 10.8.0.101 dev tun1 metric 100
```

### New IP

Next, I give the interface `enp5s0f1` the IP `192.168.60.1` and bring it up:

```
ip addr add 192.168.60.1/24 dev enp5s0f1
ip link set enp5s0f1 up
```

Then I route traffic from that subnet over my new OpenVPN connection, and add a SNAT rule for it:
```
ip rule add from 192.168.60.0/24 table 79 priority 80
iptables -A POSTROUTING -s 192.168.60.0/24 -o tun1 -j MASQUERADE -t nat
```

Bonus: I setup PiHole, so I will use the server as the DNS in upcoming DHCP...

### DHCP

```
apt install isc-dhcp-server
```

And then setup `dhcpd.conf` as:

```
default-lease-time 600;
max-lease-time 7200;

subnet 192.168.60.0 netmask 255.255.255.0 {
    range 192.168.60.100 192.168.60.200;
    option routers 192.168.60.1;
    option domain-nae-servers 192.168.60.1;
}
```

Since my server's IP on this subnet will be the gateway, as well as DNS (PiHole).

### Result

I setup my AP in "AP mode - Smart DHCP". When I plgged it into my new interface, it got an IP automatically! Then connecting my laptop to it, it also got an IP, and as its traffic routed over OpenVPN by default!
