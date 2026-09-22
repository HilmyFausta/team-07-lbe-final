# Laporan Final Project LBE NCC Kelompok 7

|    NRP     |           Nama             |
| :--------: |       :------------:       |
| 5025251052 | Hilmy Fausta Pratama       |
| 5025251041 | Muhammad Adinata Parikesit      |
| 5025251021 | Yahdilil Haq Sarifuddin|
| 5025251036 | Darwisy Ahmad Alfayyadl|

Tiap folder berisi source portofolio masing-masing + `Dockerfile` sendiri-sendiri (struktur & isi beda, karena tiap orang deploy image sendiri ke Docker Hub-nya masing-masing). Lihat `README.md` di dalam tiap folder untuk cara build & run lokal.

## Arsitektur

```mermaid
flowchart TB
    Internet(["Internet client<br/>(curl loop / browser)"])

    subgraph RG["Resource Group: LBE-NCC-07 (indiasouthcentral)"]
        PIP["Public IP: pip-team07-fp-lb<br/>172.198.228.9<br/>kelompok07.indiasouthcentral.cloudapp.azure.com"]

        subgraph LB["Standard Load Balancer: lb-team07-fb"]
            Rule["LB Rule: rule-team07-fp<br/>Frontend :80 → Backend :8080<br/>Session persistence: None"]
            Probe["Health Probe: hp-team07-fp<br/>TCP :8080, every 5s"]
        end

        subgraph VNet["VNet: vnet-07 (10.0.0.0/16)"]
            subgraph Subnet["Subnet: default (10.0.0.0/24)"]
                VM1["vm-hilmy<br/>10.0.0.6<br/>Docker container :8080"]
                VM2["vm-adi<br/>10.0.0.5<br/>Docker container :8080"]
                VM3["vm-dilil<br/>10.0.0.4<br/>Docker container :8080"]
                VM4["vm-wisy<br/>10.0.0.7<br/>Docker container :8080"]
            end
        end
    end

    Internet -->|"HTTP :80"| PIP --> Rule
    Rule -.->|"health check :8080"| Probe
    Rule --> VM1 & VM2 & VM3 & VM4
    Probe -.-> VM1 & VM2 & VM3 & VM4
```
## Komponen
| Resource | Nama | Detail |
|---|---|---|
| Resource Group | `LBE-NCC-07` | region `indiasouthcentral` |
| Virtual Network | `vnet-07` | `10.0.0.0/16`, subnet `default` (`10.0.0.0/24`) |
| Load Balancer | `lb-team07-fb` | SKU **Standard**, backend pool `bp-team07-fp` |
| Public IP | `pip-team07-fp-lb` | Static, `172.198.228.9` |
| LB Rule | `rule-team07-fp` | Frontend port `80` → backend port `8080`, protokol TCP, load distribution `Default` (= tanpa session persistence) |
| Health Probe | `hp-team07-fp` | TCP port `8080`, interval 5s, threshold 1 |


## Virtual Machines

Semua `Standard_B1s` dengan 1GB ram dan 1vCPU, Ubuntu 24.04 LTS, masing-masing menjalankan satu portofolio dalam container Docker yang listen di port internal **8080** (fixed, dipakai health probe & LB rule).

| VM | Private IP | Anggota | SSH via LB (Inbound NAT rule) |
|---|---|---|---|
| vm-hilmy | 10.0.0.6 | Hilmy | port `5001` → VM port 22 |
| vm-wisy  | 10.0.0.7 | Wisy  | port `5002` → VM port 22 |
| vm-dilil | 10.0.0.4 | Dilil | port `5003` → VM port 22 |
| vm-adi   | 10.0.0.5 | Adi   | port `5004` → VM port 22 |

Setiap VM punya NSG sendiri, dengan rule inbound yang mengizinkan:
- `22` (SSH)
- `80` (HTTP)
- `443` (HTTPS)
- `8080` (app port — dipakai health probe & LB rule)