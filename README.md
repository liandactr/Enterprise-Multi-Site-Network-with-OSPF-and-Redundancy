# Enterprise Multi-Site Network with OSPF and Redundancy

## Deskripsi Proyek

Proyek ini merupakan simulasi jaringan perusahaan multi-site menggunakan Cisco Packet Tracer. Jaringan menghubungkan tiga lokasi, yaitu Head Office (HQ), Branch Office 1, dan Branch Office 2, menggunakan tiga router Cisco 2911 dengan protokol routing dinamis OSPF (Open Shortest Path First).

Topologi menggunakan bentuk segitiga yang menghubungkan setiap router. Desain ini menyediakan jalur alternatif ketika salah satu koneksi antar-router mengalami gangguan.

Proyek ini berfokus pada konfigurasi OSPF, pengalamatan IP, komunikasi antar-site, verifikasi routing table, serta pengujian redundansi jaringan melalui mekanisme failover.

## Tujuan Proyek

- Menghubungkan tiga lokasi perusahaan dalam satu jaringan.
- Mengimplementasikan OSPF menggunakan Area 0.
- Mengonfigurasi subnet LAN yang berbeda untuk setiap lokasi.
- Menggunakan subnet `/30` untuk koneksi point-to-point antar-router.
- Memverifikasi adjacency OSPF antar-router.
- Memastikan router mempelajari jaringan remote melalui routing dinamis.
- Menguji konektivitas antar-site menggunakan perintah `ping`.
- Menguji jalur alternatif ketika koneksi langsung antara HQ dan Branch Office 1 terputus.

## Tools dan Teknologi

- Cisco Packet Tracer
- Cisco 2911 Router
- Cisco Switch
- OSPFv2
- IPv4 Addressing
- Subnetting IPv4
- ICMP Ping
- Dynamic Routing
- Network Redundancy dan Failover

## Topologi Jaringan

Jaringan terdiri dari tiga lokasi yang terhubung melalui tiga router dalam topologi segitiga.

- **R1 — Head Office (HQ):** `192.168.10.0/24`
- **R2 — Branch Office 1:** `192.168.20.0/24`
- **R3 — Branch Office 2:** `192.168.30.0/24`

Setiap lokasi memiliki satu switch dan tiga PC. Ketiga router saling terhubung untuk menyediakan jalur komunikasi utama dan jalur alternatif.

## Perangkat yang Digunakan

| Perangkat | Jumlah | Fungsi |
|---|---:|---|
| Cisco 2911 Router | 3 | Routing antar-site dan implementasi OSPF |
| Switch | 3 | Menghubungkan perangkat dalam LAN |
| PC | 9 | End device untuk pengujian konektivitas |

## IP Addressing Plan

### 1. Head Office — R1

| Perangkat | IP Address | Subnet Mask | Default Gateway |
|---|---|---|---|
| R1 | `192.168.10.1` | `255.255.255.0` | - |
| HQ-PC1 | `192.168.10.10` | `255.255.255.0` | `192.168.10.1` |
| HQ-PC2 | `192.168.10.11` | `255.255.255.0` | `192.168.10.1` |
| HQ-PC3 | `192.168.10.12` | `255.255.255.0` | `192.168.10.1` |

### 2. Branch Office 1 — R2

| Perangkat | IP Address | Subnet Mask | Default Gateway |
|---|---|---|---|
| R2 | `192.168.20.1` | `255.255.255.0` | - |
| Branch1-PC1 | `192.168.20.10` | `255.255.255.0` | `192.168.20.1` |
| Branch1-PC2 | `192.168.20.11` | `255.255.255.0` | `192.168.20.1` |
| Branch1-PC3 | `192.168.20.12` | `255.255.255.0` | `192.168.20.1` |

### 3. Branch Office 2 — R3

| Perangkat | IP Address | Subnet Mask | Default Gateway |
|---|---|---|---|
| R3 | `192.168.30.1` | `255.255.255.0` | - |
| Branch2-PC1 | `192.168.30.10` | `255.255.255.0` | `192.168.30.1` |
| Branch2-PC2 | `192.168.30.11` | `255.255.255.0` | `192.168.30.1` |
| Branch2-PC3 | `192.168.30.12` | `255.255.255.0` | `192.168.30.1` |

### 4. Koneksi Antar-Router

Subnet mask untuk seluruh koneksi point-to-point adalah `255.255.255.252`.

| Koneksi | Interface | IP Address | Subnet |
|---|---|---|---|
| R1–R2 | R1 G0/1 | `10.0.12.1/30` | `10.0.12.0/30` |
| R1–R2 | R2 G0/1 | `10.0.12.2/30` | `10.0.12.0/30` |
| R1–R3 | R1 G0/2 | `10.0.13.1/30` | `10.0.13.0/30` |
| R1–R3 | R3 G0/1 | `10.0.13.2/30` | `10.0.13.0/30` |
| R2–R3 | R2 G0/2 | `10.0.23.1/30` | `10.0.23.0/30` |
| R2–R3 | R3 G0/2 | `10.0.23.2/30` | `10.0.23.0/30` |

## Konfigurasi OSPF

OSPF dikonfigurasi menggunakan process ID `1` dan Area `0`. Setiap router mengiklankan jaringan LAN lokal serta jaringan point-to-point yang terhubung langsung dengannya.

### R1 — Head Office

```cisco
router ospf 1
 network 10.0.12.0 0.0.0.3 area 0
 network 10.0.13.0 0.0.0.3 area 0
 network 192.168.10.0 0.0.0.255 area 0
```

### R2 — Branch Office 1

```cisco
router ospf 1
 network 10.0.12.0 0.0.0.3 area 0
 network 10.0.23.0 0.0.0.3 area 0
 network 192.168.20.0 0.0.0.255 area 0
```

### R3 — Branch Office 2

```cisco
router ospf 1
 network 10.0.13.0 0.0.0.3 area 0
 network 10.0.23.0 0.0.0.3 area 0
 network 192.168.30.0 0.0.0.255 area 0
```

Konfigurasi di atas merupakan bagian konfigurasi OSPF. Alamat IP interface, status interface, dan pengaturan default gateway PC harus sesuai dengan IP addressing plan.

## Verifikasi dan Pengujian

### 1. Verifikasi Neighbor OSPF

Perintah yang digunakan:

```cisco
show ip ospf neighbor
```

Hasil pengujian menunjukkan adjacency OSPF berhasil terbentuk dengan status `FULL`. Setelah koneksi R1–R2 diaktifkan kembali, R1 kembali membentuk adjacency dengan R2 dan R3.

### 2. Verifikasi Routing Table

Perintah yang digunakan:

```cisco
show ip route
```

Router mempelajari jaringan remote melalui OSPF. Rute OSPF ditandai dengan kode `O` pada routing table.

Contoh rute pada R1 ketika seluruh koneksi aktif:

```text
O 192.168.20.0/24 [110/2] via 10.0.12.2
O 192.168.30.0/24 [110/2] via 10.0.13.2
```

Hasil tersebut menunjukkan R1 dapat menjangkau LAN Branch Office 1 melalui R2 dan LAN Branch Office 2 melalui R3.

### 3. Pengujian Ping Antar-Site

Pengujian dilakukan dari HQ-PC1 dengan alamat IP `192.168.10.10` menuju:

- Branch1-PC1 — `192.168.20.10`
- Branch2-PC1 — `192.168.30.10`

Pada pengujian awal, paket pertama dapat mengalami timeout karena proses resolusi ARP. Setelah pengujian diulang, kedua tujuan memberikan hasil empat dari empat paket berhasil diterima dengan `0% packet loss`.

### 4. Pengujian Failover

Pengujian dilakukan dengan mematikan interface R1 G0/1 yang menghubungkan R1 dengan R2.

Perintah yang digunakan:

```cisco
configure terminal
interface gigabitEthernet 0/1
shutdown
```

Setelah koneksi langsung R1–R2 terputus, routing table R1 menunjukkan rute menuju LAN Branch Office 1 berpindah ke jalur alternatif melalui R3.

Contoh rute selama pengujian:

```text
O 192.168.20.0/24 [110/3] via 10.0.13.2
O 192.168.30.0/24 [110/2] via 10.0.13.2
```

Ping dari HQ-PC1 menuju Branch1-PC1 selama pengujian failover tetap berhasil dengan empat dari empat paket diterima dan `0% packet loss`.

Hasil tersebut menunjukkan bahwa jalur alternatif melalui R3 dapat mempertahankan konektivitas antara HQ dan Branch Office 1 ketika koneksi langsung R1–R2 terputus.

### 5. Pemulihan Koneksi

Setelah pengujian failover selesai, interface R1 G0/1 diaktifkan kembali.

```cisco
configure terminal
interface gigabitEthernet 0/1
no shutdown
```

Adjacency OSPF dengan R2 kembali mencapai status `FULL`, dan routing table kembali menunjukkan rute langsung menuju Branch Office 1 melalui R2.

## Ringkasan Hasil Pengujian

| Pengujian | Hasil | Keterangan |
|---|---|---|
| Adjacency OSPF | Berhasil | Neighbor OSPF mencapai status FULL |
| Routing antar-site | Berhasil | Jaringan remote dipelajari melalui OSPF |
| Ping HQ ke Branch Office 1 | Berhasil | Pengujian ulang menghasilkan 4/4 paket diterima |
| Ping HQ ke Branch Office 2 | Berhasil | Pengujian ulang menghasilkan 4/4 paket diterima |
| Failover R1–R2 melalui R3 | Berhasil | Rute ke LAN Branch Office 1 berpindah melalui R3 |
| Pemulihan koneksi R1–R2 | Berhasil | Neighbor OSPF kembali FULL dan rute langsung dipulihkan |

## Struktur Repository

```text
enterprise-multi-site-ospf/
│
├── README.md
├── Enterprise-Multi-Site-OSPF.pkt
│
└── screenshots/
    ├── topology.png
    ├── ospf-neighbors.png
    ├── routing-table.png
    └── failover-test.png
```

Keterangan file:

- `README.md` — dokumentasi proyek dan hasil pengujian.
- `Enterprise-Multi-Site-OSPF.pkt` — file simulasi Cisco Packet Tracer.
- `topology.png` — screenshot keseluruhan topologi jaringan.
- `ospf-neighbors.png` — screenshot hasil verifikasi neighbor OSPF.
- `routing-table.png` — screenshot routing table.
- `failover-test.png` — screenshot pengujian failover dan konektivitas.

## Konsep yang Dipelajari

Melalui proyek ini, konsep jaringan yang dipraktikkan meliputi:

- Perencanaan alamat IP dan subnetting IPv4.
- Implementasi routing dinamis menggunakan OSPF.
- Pembentukan adjacency antar-router.
- Advertise network menggunakan wildcard mask.
- Interpretasi routing table dan administrative distance.
- Pengujian konektivitas menggunakan ICMP.
- Redundansi jalur dan pemulihan routing setelah koneksi terputus.

## Kesimpulan

Proyek ini menunjukkan implementasi jaringan perusahaan multi-site menggunakan OSPF Area 0 dengan tiga router dan tiga LAN yang berbeda. Setiap router dapat mempelajari jaringan remote melalui routing dinamis, dan konektivitas dari HQ menuju kedua branch berhasil diuji.

Pengujian failover menunjukkan bahwa ketika koneksi langsung R1–R2 dimatikan, trafik menuju Branch Office 1 dapat melewati jalur alternatif melalui R3. Setelah koneksi dipulihkan, adjacency OSPF kembali terbentuk dan rute langsung kembali digunakan.

Proyek ini menjadi latihan dalam membangun jaringan enterprise yang mendukung dynamic routing, inter-site connectivity, dan redundancy menggunakan Cisco Packet Tracer.

---

**Project Category:** Network Engineering / Routing & Switching  
**Platform:** Cisco Packet Tracer  
**Routing Protocol:** OSPFv2  
**Topology:** Three-Router Triangle  
**Routing Area:** Area 0  
**Key Features:** Dynamic Routing, Inter-Site Connectivity, Failover Redundancy
