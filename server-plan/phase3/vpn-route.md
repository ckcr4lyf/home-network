Ok, so I skipped phase 2.

I wanted to route all traffic from the `192.168.30.0/24` network over OpenVPN to the internet, so anyone who connects to it is auto-VPNd

If you recall (actually not in this repo. TODO: add it), I already have a table for oruting over OpenVPN (which has local IP 10.16.0.7):

```
# ip route show table 69
default via 10.16.0.1 dev tun0
10.16.0.0/24 dev tun0 scope link
```

Currently I have a manual rule to route traffic over it for packets originating on `10.16.0.7` for my Torrent Client.

The next step is to do this for the `192.168.30.0/24` subnet. For this, I first added the rule:

```
ip rule add from 192.168.30.0/24 table 69 priority 70
```

However this isn't enough, since we need to also SNAT the original subnet over the VPN client IP (`10.16.0.7`) . For this, I add the iptables rule:

```
iptables -A POSTROUTING -s 192.168.30.0/24 -o tun0 -j MASQUERADE -t nat
```

After this, it worked!