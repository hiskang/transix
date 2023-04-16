# transix
DS-Lite (RFC6333) linux config

# Ubuntu /etc/network/if-up.d/transix

````
#!/bin/sh

[ "$IFACE" = "ens32" -a "$ADDRFAM" = "inet6" ] || exit 0

sysctl -w net.ipv4.ip_forward=1
ip a add 2409:10:c2e0:4500::feed/64 dev ens32
ip -6 tunnel add ip6tnl1 mode ip4ip6 remote 2404:8e00::feed:100 local 2409:10:c2e0:4500::feed dev ens32
ip link set dev ip6tnl1 up
curl 'http://update.transix.jp/request?username=u1111111111&password=efefefef'

ip r delete default via 192.168.1.1 dev ens32
ip r add default dev ip6tnl1
iptables -t nat -A POSTROUTING -o ip6tnl1 -j MASQUERADE
iptables -t mangle -A POSTROUTING -o ip6tnl1 -p tcp -m tcp --tcp-flags SYN,RST SYN -j TCPMSS --clamp-mss-to-pmtu

````
