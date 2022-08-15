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

The plan is to first setup `enp5s0f0` to `192.168.30.1` , with no additional routing