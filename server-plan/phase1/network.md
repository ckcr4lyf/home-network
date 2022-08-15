## Assigning IP

Currently my server has the following NICs:

```
2: enp6s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether RE:DA:CT:ED:XD:f5 brd ff:ff:ff:ff:ff:ff
    inet 192.168.128.3/24 metric 100 brd 192.168.128.255 scope global dynamic enp6s0
       valid_lft 70023sec preferred_lft 70023sec
3: enp5s0f0: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN group default qlen 1000
    link/ether RE:DA:CT:ED:XD:84 brd ff:ff:ff:ff:ff:ff
4: enp5s0f1: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN group default qlen 1000
    link/ether RE:DA:CT:ED:XD:85 brd ff:ff:ff:ff:ff:ff
```

The plan is to first setup `enp5s0f0` to `192.168.30.1` , with no additional routing.

Hotpatch command:

```
ip addr add 192.168.30.1/24 dev enp5s0f0
ip link set enp5s0f0 up
```

## Setting up router

I had to reset my Netgear R6120. Then via Wifi, I changed it to "AP Mode", with static config (since my server doesn't have DHCP - yet?)

I configured it as follows:

```
IP: 192.168.30.2
Subnet: 255.255.255.0
Gateway: 192.168.30.1
```

A bit rough when I first plugged it in, not sure why. But after a couple of minutes, I was able to ping it from my server!

I also connected my phone to the AP manually (static IP) , and was able to access my server's network. The total manual IP assigning seems to have worked!
