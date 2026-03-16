# Torrent VPS

I use a VPS as my gateway for torrenting. This is done via a Wireguard connection, so my torrent client running on my home-server has it's traffic routed over Wireguard, and the remote server forwards traffic on a certain port to my home server so it is connectable.

## Remote iptables setup

iptables -A FORWARD -d 10.69.0.2/32 -p tcp -m tcp --dport 31416 -j ACCEPT
iptables -A PREROUTING -d REAL_PUBLIC_IP/32 -p tcp -m tcp --dport 31416 -j DNAT --to-destination 10.69.0.2:31416 -t nat
iptables -A FORWARD -d 10.69.0.2/32 -p udp -m udp --dport 31416 -j ACCEPT
iptables -A PREROUTING -d REAL_PUBLIC_IP/32 -p udp -m udp --dport 31416 -j DNAT --to-destination 10.69.0.2:31416 -t nat
iptables -A POSTROUTING -o ens3 -j MASQUERADE -t nat

### Explanation

* The `PREROUTING` rule (on the NAT table) rewrites incoming packets from the internet on my VPS's port 31416 to `10.69.0.2` (my home server over wireguard).
* The `FORWARD` rule allows these packets to be forwarded to said address (required if default policy is drop)
* The `POSTROUTING` rule takes the replies from my homeserver and rewrites the source IP from `10.69.0.2` to the VPS's public IP

So, to any third party connecting, they have no idea about the behind the scenes routing.
