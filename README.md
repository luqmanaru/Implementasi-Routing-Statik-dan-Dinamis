# Implementasi-Routing-Statik-dan-Dinamis

Proyek infrastruktur jaringan hybrid dengan routing statik dan OSPF

![Cisco](https://img.shields.io/badge/Cisco-Routing-blue?style=for-the-badge&logo=cisco&logoColor=white)
![Network](https://img.shields.io/badge/Network-Hybrid-green?style=for-the-badge)
![OSPF](https://img.shields.io/badge/OSPF-Protocol-orange?style=for-the-badge)

---

## 📝 Deskripsi
Proyek ini mengimplementasikan **infrastruktur jaringan hybrid** dengan kombinasi routing statik dan OSPF. Implementasi mencakup:
- Konfigurasi 3 router dengan routing statik dan dinamis
- Implementasi DHCP server untuk alokasi IP otomatis
- Verifikasi konektivitas end-to-end antar segmen jaringan
- Analisis tabel routing dan pengujian failover

---

## 🏗️ Topologi Jaringan
| Perangkat | Interface | IP Address | Subnet | Koneksi |
|-----------|-----------|------------|--------|---------|
| **R1** | Gi0/0 | 10.10.10.1 | /30 | Ke R2 |
| | Gi0/1 | 192.168.1.1 | /24 | Ke PC1, PC2 |
| **R2** | Gi0/0 | 10.10.10.2 | /30 | Ke R1 |
| | Gi0/1 | 20.20.20.1 | /30 | Ke R3 |
| | Gi0/2 | 192.168.2.1 | /24 | Ke PC3, PC4 |
| **R3** | Gi0/0 | 20.20.20.2 | /30 | Ke R2 |
| | Gi0/1 | 192.168.3.1 | /24 | Ke PC5, PC6 |
| **PC1** | - | DHCP | /24 | Ke R1 |
| **PC2** | - | DHCP | /24 | Ke R1 |
| **PC3** | - | DHCP | /24 | Ke R2 |
| **PC4** | - | DHCP | /24 | Ke R2 |
| **PC5** | - | DHCP | /24 | Ke R3 |
| **PC6** | - | DHCP | /24 | Ke R3 |

---

## 🖼️ Gambar Topologi
```
    [PC1] [PC2]         [PC3] [PC4]         [PC5] [PC6]
        |   |               |   |               |   |
        +---+---+           +---+---+           +---+---+
            |                   |                   |
          [R1]---------------[R2]---------------[R3]
            |                   |                   |
        10.10.10.0/30       20.20.20.0/30
```
<img width="921" height="392" alt="image" src="https://github.com/user-attachments/assets/0e8ca788-250e-40a1-a2af-fe6feaa0b163" />

---

## 📊 Output Verifikasi
### Tabel Routing R1
```
R1# show ip route

Codes: C - connected, S - static, I - IGRP, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2, E - EGP
       i - IS-IS, L1 - IS-IS level-1, L2 - IS-IS level-2, ia - IS-IS inter area
       * - candidate default, U - per-user static route, o - ODR
       P - periodic downloaded static route

Gateway of last resort is not set

     10.0.0.0/30 is subnetted, 1 subnets
C       10.10.10.0 is directly connected, GigabitEthernet0/0
     20.0.0.0/30 is subnetted, 1 subnets
S       20.20.20.0 [1/0] via 10.10.10.2
     192.168.1.0/24 is subnetted, 1 subnets
C       192.168.1.0 is directly connected, GigabitEthernet0/1
     192.168.2.0/24 is subnetted, 1 subnets
S       192.168.2.0 [1/0] via 10.10.10.2
     192.168.3.0/24 is subnetted, 1 subnets
S       192.168.3.0 [1/0] via 10.10.10.2
```

### Tabel Routing R3
```
R3# show ip route

Codes: C - connected, S - static, I - IGRP, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2, E - EGP
       i - IS-IS, L1 - IS-IS level-1, L2 - IS-IS level-2, ia - IS-IS inter area
       * - candidate default, U - per-user static route, o - ODR
       P - periodic downloaded static route

Gateway of last resort is 20.20.20.1 to network 0.0.0.0

     10.0.0.0/30 is subnetted, 1 subnets
S       10.10.10.0 [1/0] via 20.20.20.1
     20.0.0.0/30 is subnetted, 1 subnets
C       20.20.20.0 is directly connected, GigabitEthernet0/0
     192.168.1.0/24 is subnetted, 1 subnets
S       192.168.1.0 [1/0] via 20.20.20.1
     192.168.2.0/24 is subnetted, 1 subnets
S       192.168.2.0 [1/0] via 20.20.20.1
     192.168.3.0/24 is subnetted, 1 subnets
C       192.168.3.0 is directly connected, GigabitEthernet0/1
S*   0.0.0.0/0 [1/0] via 20.20.20.1
```

### Konektivitas PC1 ke PC5
```
PC1> ping 192.168.3.1

Pinging 192.168.3.1 with 32 bytes of data:
Reply from 192.168.3.1: bytes=32 time=15ms TTL=254
Reply from 192.168.3.1: bytes=32 time=10ms TTL=254
Reply from 192.168.3.1: bytes=32 time=12ms TTL=254
Reply from 192.168.3.1: bytes=32 time=11ms TTL=254

Ping statistics for 192.168.3.1:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 10ms, Maximum = 15ms, Average = 12ms
```

### Verifikasi DHCP R2
```
R2# show ip dhcp binding

Bindings from all pools not associated with VRF:
IP address      Client-ID/              Lease expiration        Type
                Hardware address
192.168.2.2     0100.5079.6668.01       --                     Automatic
192.168.2.3     0100.5079.6668.02       --                     Automatic
```

---

**luqmanaru**  
