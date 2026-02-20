# Home Network

Private repository for home network documentation, configuration backups, and maintenance plans.

## Quick Reference

| Item | Detail |
|---|---|
| **Network** | 192.168.1.0/24 |
| **Gateway** | TP-Link Deco X50-5G (FTTP + 5G failover) |
| **Mesh** | 4× TP-Link Deco PX50 (WiFi + Powerline backhaul) |
| **Smart Home Hub** | Home Assistant (Nabu Casa) |
| **Total Devices** | ~39 (35 online at last scan) |
| **Last Scan** | 20 Feb 2026 (nmap 7.98) |

## IP Scheme (Functional Banding)

| Range | Function |
|---|---|
| .1–.19 | Infrastructure |
| .20–.29 | Smart Home Core |
| .30–.39 | Security / Intercom |
| .40–.49 | Communications & Office |
| .50–.59 | AV / Entertainment |
| .60–.79 | HVAC / Climate |
| .80–.109 | Lighting |
| .110–.129 | Switching / Power |
| .130–.139 | Miscellaneous / Utility |
| .140–.169 | Unallocated (future) |
| .170–.220 | DHCP Pool (roaming/guests) |
| .221–.254 | System Reserved |

See [docs/dhcp-scheme.md](docs/dhcp-scheme.md) for full details.

## Repository Structure

```
home-network/
├── README.md                       # This file
├── docs/
│   ├── migration-plan.md           # Living migration checklist
│   ├── network-overview.md         # Architecture & design decisions
│   └── dhcp-scheme.md              # Functional banding scheme
├── inventory/
│   ├── network_inventory_final.xlsx # nmap scan results (20 Feb 2026)
│   └── dhcp_reservation_plan.xlsx   # DHCP reservation plan
├── backups/
│   ├── deco/                       # Router config exports
│   ├── home-assistant/             # HA snapshot references
│   ├── shelly/                     # Device configs
│   ├── hikvision/                  # Camera configs
│   └── yealink/                    # DECT config
└── scripts/
    └── (future: scan scripts, backup automation)
```

## Current Status

🔧 **Migration in progress** — see [docs/migration-plan.md](docs/migration-plan.md)
