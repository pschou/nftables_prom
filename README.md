# nftables_prom
Super simple promethues exporter for nftables using nft

To build:
```
make
```

To run:
```
nftables_prom
```

Program flags:
```
# ./nftables_prom  -h
Usage of ./nftables_prom:
  -listen string
        ip and port to listen on (default ":9732")
  -sizebins string
        transmit unit size bins in bytes (default "75,150,225,300,375,450,525,600,675,750,825,900,975,1050,1125,1200,1275,1350,1425,1500,4500,9000,inf")
  -sizesuffix string
        nftables chain suffix to bin (default "_SIZE")
```

Setup nftables to bin the packets for inspection:
```
#!/bin/bash

# create a hash list for later use in iptable selection rules
ipset -N local_ips hash:net
ipset -A local_ips 10.0.0.0/8
ipset -A local_ips 192.168.0.0/16
ipset -A local_ips 172.16.0.0/12

# Count packets sourced from the internet
iptables -t mangle -N INTERNET_SIZE
iptables -t mangle -F INTERNET_SIZE
iptables -t mangle -A FORWARD -m set \! --match-set local_ips src -m set --match-set local_ips dst -j INTERNET_SIZE
for i in {1..20}; do
  iptables -t mangle -A INTERNET_SIZE -m length --length $((i * 75 - 74)):$((i*75)) -j RETURN
done
iptables -t mangle -A INTERNET_SIZE -m length --length 1501:4500 -j RETURN
iptables -t mangle -A INTERNET_SIZE -m length --length 4501:9000 -j RETURN

# Count packets destined for the world / default gateway
iptables -t mangle -N WORLD_SIZE
iptables -t mangle -F WORLD_SIZE
iptables -t mangle -A FORWARD -m set --match-set local_ips src -m set \! --match-set local_ips dst -j WORLD_SIZE
for i in {1..20}; do
  iptables -t mangle -A WORLD_SIZE -m length --length $((i * 75 - 74)):$((i*75)) -j RETURN
done
iptables -t mangle -A WORLD_SIZE -m length --length 1501:4500 -j RETURN
iptables -t mangle -A WORLD_SIZE -m length --length 4501:9000 -j RETURN
```
