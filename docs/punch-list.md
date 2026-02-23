# Post-Migration Punch List

**Created**: 21 February 2026 (end of Day 1)  
**Updated**: 23 February 2026 (Day 3 session)  
**Status**: 🟡 Near complete — minor items remain  
**Last nmap**: 18:17 UTC, 22 Feb 2026

---

## Summary

Day 1 migrated 28 devices to target IPs. Day 2 completed HA integration verification, deferred device migrations, Hikvision ghost IP fix, infrastructure additions (NAS, managed switch), and firmware updates. The network is now fully functional with a small number of housekeeping items remaining.

---

## Completed — Day 2

### Priority 1 — Fix Broken Integrations
- [x] **1.2 Hikvision camera feed** — motion detection working ✅
- [x] **Sony Bravia** — reconfigured with new IP .50, PSK re-validated ✅
- [x] **Sonos Play:5** — rediscovered after HA restart ✅

### Priority 2 — Verify All HA Integrations
- [x] 2.1 Shelly devices (all 7) — all toggled and verified ✅
- [x] 2.2 Tapo devices (all 5) — all toggled and verified ✅
- [x] 2.3 Sonoff Immersion (.60) — toggle + power monitoring ✅
- [x] 2.5 Sony Bravia (.50) — reconfigured, HA control working ✅
- [x] 2.6 Sonos Play:5 (.51) — needed HA restart to rediscover ✅
- [x] 2.7 Octopus Energy — cloud API, all good ✅
- [x] 2.8 Nabu Casa remote access — verified ✅

### Priority 3 — Deferred Device Migrations
- [x] 3.1 Samsung Bedroom TV → .52 (MAC 74:6D:FA:75:41:B0) ✅
- [x] 3.2 Samsung Kitchen TV → .53 (MAC 00:7D:3B:E7:F6:42) ✅
- [x] 3.3 Android Tablet → .23 (MAC C8:05:A4:09:9A:9A) ✅
- [x] 3.4 Sky Puck → .54 (MAC 04:B8:6A:9D:94:BD) ✅
- [x] 3.5 PS4 → .55 (MAC 0C:FE:45:68:7B:BB) ✅

### Priority 4 — Security Hardening
- [x] 4.1 Printer protocols — already secure, FTP/Telnet/SMTP were off ✅
- [x] 4.2 Tapo critical outlet locking — removed (low risk on home LAN)
- [x] 4.3 Hikvision gateway verification — all set to .1 ✅
- [x] Hikvision ghost IPs — fixed via SADP tool ✅
- [x] Hikvision firmware updated to V2.2.92 ✅

### Priority 5 — Firmware
- [x] 5.1 Shelly Porch Lights — updated to 1.7.4 stable ✅
- [x] 5.2 Deco firmware — updated to 1.1.1 ✅
- [x] Cisco SLM2008 — already on 2.0.0.10 (latest/final) ✅

### Additional Day 2 Completions
- [x] Door station reservation recreated (.30) — had been lost ✅
- [x] NAS deployed → .42 (ASUSTOR AS1102T) ✅
- [x] Cisco SLM2008 switch deployed → .6 (static) ✅
- [x] Printer moved to ethernet (new MAC 30:05:5C:EB:F2:3C) ✅
- [x] Yealink web admin access restored, password changed, config backed up ✅

---

## Remaining Items

### Dehumidifier — Local Tuya / Smart Life
- **Problem**: Device is on Smart Life app, not Tuya Smart. Local Tuya integration in HA only sees the thermostat (UFH controller on Tuya Smart account).
- **Options**:
  - [ ] Link Smart Life account to Local Tuya integration
  - [ ] Or move dehumidifier to Tuya Smart account
  - [ ] Or use Smart Life integration in HA instead
  - [ ] Verify HA can control dehumidifier after fix

### Tuya UFH Controller (.62)
- [ ] Review configuration — may need Local Tuya IP update
- [ ] Defer to underfloor heating project (planned 2026)

### Loft Lights Reservation (.82)
- **Problem**: Shelly Loft Lights (E4:B0:63:7E:59:24) appearing at .24 instead of .82
- [ ] Check Deco reservation is intact for this MAC → .82
- [ ] Power cycle the Shelly to force DHCP renewal

### Printer Deco Reservation — COMPLETED 23 Feb 2026
- [x] Updated .41 reservation to wired MAC (30:05:5C:EB:F2:3C) ✅

### Mystery Devices
- **58:BF:25:46:D6:52** (Espressif/ESP32) at .150 — no web UI on port 80, very sleepy (32% packet loss, high latency variance). Behaviour consistent with battery-powered sensor.
  - [ ] Physical hunt to locate device
  - [ ] Run `nmap -Pn -sV -p 1-1000 192.168.1.150` when device is awake
  - [ ] Identify and assign to correct band or decommission
- **4C:75:25:20:2C:21** — reserved in Deco, unknown
  - [ ] Identify or remove reservation

### Shelly HTG3 H&T Sensors (×5) — IDENTIFIED 23 Feb 2026
All five humidity/temperature sensors now identified by MAC and location:
| MAC | Location |
|---|---|
| B0:81:84:EE:8D:DC | Bedroom |
| 54:32:04:57:9B:B4 | Living Room |
| B0:81:84:EE:81:88 | Office |
| 54:32:04:57:B1:D0 | Study |
| 80:B5:4E:34:3D:D4 | Porch |
- [ ] Decision pending: assign DHCP reservations in HVAC range (.65-.69) or leave in DHCP pool (sensors sleep between updates)

### NAS Home Assistant Integration (.42)
- [ ] Enable WOL in ASUSTOR ADM (Power > Wake-on-LAN)
- [ ] Set up HA `wake_on_lan` integration for power-on (MAC 78:72:64:41:69:25)
- [ ] Set up SSH `shell_command` for graceful shutdown
- [ ] Add `binary_sensor.ping` for online state detection
- [ ] Create combined switch entity (WOL on / SSH off / ping state)

### Documentation
- [ ] Update inventory spreadsheet with all final IPs, new devices, and H&T sensors
- [ ] Consider Hikvision firmware update to latest (V2.2.100 or V2.2.108) when stable

### DHCP Scheme Revision — PLANNED
Restructure to separate open DHCP pool from reservations:
- **.1–.19** — Infrastructure (stays as-is)
- **.20–.49** — Open DHCP pool (new devices land here automatically)
- **.50–.250** — Reservations only (functional banding)

Devices to move out of .20-.49 range:
| Current IP | Device | Notes |
|---|---|---|
| .20 | Home Assistant | → .50+ |
| .23 | Android Tablet | → .50+ |
| .30 | Hikvision Door Station | → .50+ |
| .31 | Hikvision Indoor Lounge | → .50+ (static via SADP) |
| .32 | Hikvision Indoor Office | → .50+ (static via SADP) |
| .40 | Yealink W70B | → .50+ |
| .41 | Brother Printer | → .50+ |
| .42 | ASUSTOR NAS | → .50+ |

This is a dedicated session — requires same careful approach as initial migration.

---

## Final Network State (as of 18:17 UTC, 22 Feb 2026)

### Infrastructure
| IP | Device | MAC | Type | Status |
|---|---|---|---|---|
| .1 | Deco X50-5G Gateway | 8C:86:DD:12:69:30 | DHCP Server | ✅ FW 1.1.1 |
| .6 | Cisco SLM2008 Switch | A4:0C:C3:65:AF:B9 | Static | ✅ FW 2.0.0.10 |
| .247-.250 | Deco PX50 Mesh Nodes (×4) | Various | Deco-managed | ✅ |

### Smart Home Core
| IP | Device | MAC | Status |
|---|---|---|---|
| .20 | Home Assistant | 20:F8:3B:02:16:21 | ✅ |
| .21 | HA Voice Preview | 20:F8:3B:0A:3E:CC | ✅ |
| .22 | Octopus Energy Mini | 78:1C:3C:33:C0:4C | ✅ |
| .23 | Android Tablet (HA Panel) | C8:05:A4:09:9A:9A | ✅ |

### Security
| IP | Device | MAC | Status |
|---|---|---|---|
| .30 | Hikvision Door Station | BC:5E:33:58:B0:07 | ✅ DHCP |
| .31 | Hikvision Indoor Lounge | 14:F5:F9:B9:BA:CC | ✅ Static via SADP |
| .32 | Hikvision Indoor Office | 14:F5:F9:E8:D7:11 | ✅ Static via SADP |

### Communications & Office
| IP | Device | MAC | Status |
|---|---|---|---|
| .40 | Yealink W70B | 24:9A:D8:B4:10:56 | ✅ Config backed up |
| .41 | Brother DCP-9020CDW | 30:05:5C:EB:F2:3C | ✅ Wired ethernet |
| .42 | ASUSTOR AS1102T NAS | 78:72:64:41:69:25 | ✅ NEW |

### AV / Entertainment
| IP | Device | MAC | Status |
|---|---|---|---|
| .50 | Sony Bravia | 88:C9:E8:77:8D:1F | ✅ HA reconfigured |
| .51 | Sonos Play:5 | 5C:AA:FD:08:B2:6C | ✅ |
| .52 | Samsung TV (Bedroom) | 74:6D:FA:75:41:B0 | ✅ |
| .53 | Samsung TV (Kitchen) | 00:7D:3B:E7:F6:42 | ✅ |
| .54 | Sky Puck | 04:B8:6A:9D:94:BD | ✅ Reserved |
| .55 | PlayStation 4 | 0C:FE:45:68:7B:BB | ✅ |

### HVAC / Climate
| IP | Device | MAC | Status |
|---|---|---|---|
| .60 | Sonoff Immersion | E0:5A:1B:6A:9F:40 | ✅ |
| .62 | Tuya UFH Controller | FC:3C:D7:A1:99:1D | ⚠ Config TBC |
| .63 | MeacoDry Dehumidifier | 38:A5:C9:A3:E5:AD | ⚠ HA offline |
| .64 | Shelly Towel Rail | CC:BA:97:EC:0F:A8 | ✅ |

### Lighting
| IP | Device | MAC | Status |
|---|---|---|---|
| .80 | Shelly Cloakroom Light | CC:BA:97:EB:35:04 | ✅ |
| .81 | Shelly Landing Lights | E4:B0:63:78:DD:C8 | ✅ |
| .82 | Shelly Loft Lights | E4:B0:63:7E:59:24 | ⚠ At .24, check reservation |
| .83 | Shelly Porch Lights | E4:B0:63:61:C9:EC | ✅ FW 1.7.4 |
| .84 | Tapo Lamp Plug | 0C:EF:15:00:18:3B | ✅ |

### Switching / Power
| IP | Device | MAC | Status |
|---|---|---|---|
| .110 | Tapo AV Strip | 0C:EF:15:CA:A6:9E | ✅ |
| .111 | Tapo Gaming Strip | 0C:EF:15:CA:A6:D4 | ✅ |
| .112 | Tapo Lounge Table | 0C:EF:15:CA:AE:76 | ✅ |
| .113 | Tapo Porch Strip | 98:BA:5F:84:2D:41 | ✅ |

### Miscellaneous
| IP | Device | MAC | Status |
|---|---|---|---|
| .130 | Shelly Clocktower Motor | E4:B0:63:7C:AE:10 | ✅ |
| .131 | Shelly Christmas Tree | A0:85:E3:BC:8B:50 | ✅ |
| .150 | Unknown Espressif | 58:BF:25:46:D6:52 | ❓ To identify |

---

## Scorecard

| Metric | Count |
|---|---|
| Devices at target IPs | 33 |
| New devices added (Day 2) | 3 (NAS, Switch, Tablet) |
| H&T sensors identified (Day 3) | 5 (Bedroom, Living Room, Office, Study, Porch) |
| HA integrations verified | All except dehumidifier + UFH |
| Firmware updates completed | 4 (Deco, Porch Shelly, 2× Hikvision indoor) |
| Mystery devices remaining | 2 (ESP32 at .150, unknown Deco reservation) |
| Remaining items | 6 (dehumidifier, loft lights, mystery devices, H&T reservations, scheme revision, inventory) |

---

## Key Lessons Learned (Day 2)

1. **Hikvision indoor stations** have a dual-IP bug — WiFi interface grabs DHCP alongside static config. Fixed via SADP tool setting IPs at network interface level.
2. **SADP tool** is essential for Hikvision device management — iVMS-4200 doesn't expose all network settings.
3. **NAS IP conflicts** can masquerade as router issues — check for static IP collisions when adding new devices.
4. **Printer WiFi→Ethernet migration** changes the MAC — update reservations accordingly.
5. **Cisco SLM2008** is end-of-life at firmware 2.0.0.10 — no further updates available.
6. **Deco firmware updates** cause full network restart — warn household first.
7. **Smart Life vs Tuya Smart** are separate ecosystems — Local Tuya integration only sees devices on the linked account.
