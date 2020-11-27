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
