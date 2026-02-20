# DHCP Reservation Scheme — Functional Banding

All addresses are `192.168.1.x` — shorthand `.x` used for readability.

## IP Ranges

| Range | Function | Slots | Assigned | Available |
|---|---|---|---|---|
| .1–.19 | Infrastructure | 19 | 5 | 14 |
| .20–.29 | Smart Home Core | 10 | 3 | 7 |
| .30–.39 | Security / Intercom | 10 | 3 | 7 |
| .40–.49 | Communications & Office | 10 | 2 | 8 |
| .50–.59 | AV / Entertainment | 10 | 6 | 4 |
| .60–.79 | HVAC / Climate | 20 | 5 | 15 |
| .80–.109 | Lighting | 30 | 5 | 25 |
| .110–.129 | Switching / Power | 20 | 4 | 16 |
| .130–.139 | Miscellaneous / Utility | 10 | 2 | 8 |
| .140–.169 | Unallocated (future) | 30 | 0 | 30 |
| .170–.220 | DHCP Pool | 51 | — | 51 |
| .221–.254 | System Reserved | 34 | — | 34 |

## Conventions

1. **Grouping principle**: Devices grouped by FUNCTION, not manufacturer. A Shelly controlling a towel rail sits in HVAC, not Lighting. A Tapo plug on a lamp sits in Lighting, not Switching.

2. **Assignment order**: Within each range, assign from the lowest available IP upward.

3. **New device — known category**: Assign next available IP in the appropriate functional range.

4. **New device — unknown category**: Assign from Unallocated (.140–.169). If that category grows, consider formalising its own range.

5. **Decommissioned devices**: Leave IP unassigned for at least 3 months to avoid stale references in HA automations.

6. **Roaming devices**: Phones, tablets, laptops, and guest devices stay on the DHCP pool (.170–.220). No reservations needed.

7. **MAC randomisation**: Apple devices use Private WiFi Addresses by default — fine for DHCP pool. For any device needing a reservation, disable MAC randomisation first.

8. **Documentation**: Update this file, the spreadsheet, AND the HA device description whenever a reservation is added or changed.

9. **Critical devices**: HA (.20), Immersion (.60), Doorbell (.30), and Gateway (.1) should be verified working after any network changes.

10. **HVAC priority**: HVAC/Climate devices are safety-critical. Test thoroughly after any IP changes.

11. **Future VLAN mapping**: Each functional range could map to a VLAN if segmentation is needed later.

## Device Assignments

### Infrastructure (.1–.19)

| IP | Device | MAC | Location |
|---|---|---|---|
| .1 | Deco X50-5G Gateway | 8C:86:DD:12:69:30 | Study |
| .2 | Deco PX50 Mesh Node | 9C:53:22:83:F5:B0 | Living Room |
| .3 | Deco PX50 Mesh Node | F0:A7:31:1F:69:93 | Porch |
| .4 | Deco PX50 Mesh Node | F0:A7:31:1F:68:9E | Office |
| .5 | Deco PX50 Mesh Node | 9C:53:22:84:03:57 | Study |
| .6–.19 | *(Future: managed switch, PoE switch, DNS/Pi-hole, firewall)* | | |

### Smart Home Core (.20–.29)

| IP | Device | MAC | Location |
|---|---|---|---|
| .20 | Home Assistant | 20:F8:3B:02:16:21 | Study |
| .21 | HA Voice Preview | 20:F8:3B:0A:3E:CC | Kitchen |
| .22 | Octopus Energy Mini (CAD) | 78:1C:3C:33:C0:4C | Porch |
| .23–.29 | *(Future: Zigbee coordinator, Z-Wave, voice satellites)* | | |

### Security / Intercom (.30–.39)

| IP | Device | MAC | Location |
|---|---|---|---|
| .30 | Hikvision Door Station | BC:5E:33:58:B0:07 | Front Door |
| .31 | Hikvision Indoor Station (Lounge) | 14:F5:F9:B9:BA:CC | Living Room |
| .32 | Hikvision Indoor Station (Office) | 14:F5:F9:E8:D7:11 | Office |
| .33–.39 | *(Future: cameras, NVR, motion sensors)* | | |

### Communications & Office (.40–.49)

| IP | Device | MAC | Location |
|---|---|---|---|
| .40 | Yealink W70B DECT Base | 24:9A:D8:B4:10:56 | Study |
| .41 | Brother Printer | 28:56:5A:74:D9:26 | Study |
| .42–.49 | *(Future: NAS, scanner, DECT handsets)* | | |

### AV / Entertainment (.50–.59)

| IP | Device | MAC | Location |
|---|---|---|---|
| .50 | Sony Bravia Smart TV | 88:C9:E8:77:8D:1F | Living Room |
| .51 | Sonos Play:5 | 5C:AA:FD:08:B2:6C | Living Room |
| .52 | Samsung Smart TV (Bedroom) | 74:6D:FA:75:41:B0 | Bedroom |
| .53 | Samsung Smart TV (Kitchen) | *Disable MAC randomisation first* | Kitchen |
| .54 | Sky Puck | *Assign when online* | Living Room |
| .55 | PlayStation 4 | *Assign when online* | Living Room |
| .56–.59 | *(Future: Sonos speakers, streaming devices)* | | |

### HVAC / Climate (.60–.79)

| IP | Device | MAC | Location |
|---|---|---|---|
| .60 | Sonoff Immersion Heater Controller | E0:5A:1B:6A:9F:40 | TBC |
| .61 | Sonoff Backup Immersion Controller | *Assign when online* | TBC |
| .62 | Tuya UFH Controller | FC:3C:D7:A1:99:1D | TBC |
| .63 | MeacoDry Dehumidifier | 38:A5:C9:A3:E5:AD | Upstairs Landing |
| .64 | Shelly 1PM Gen4 — Heated Towel Rail | CC:BA:97:EC:0F:A8 | Bathroom |
| .65–.79 | *(Future: UFH zones, TRVs, heat pump, radiator valves)* | | |

### Lighting (.80–.109)

| IP | Device | MAC | Location |
|---|---|---|---|
| .80 | Shelly 1PM Gen4 — Cloakroom Light | CC:BA:97:EB:35:04 | Cloakroom |
| .81 | Shelly 1PM Gen4 — Upstairs Landing Lights | E4:B0:63:78:DD:C8 | Loft |
| .82 | Shelly 1PM Gen4 — Loft Lights | E4:B0:63:7E:59:24 | Loft |
| .83 | Shelly 2PM Gen4 — Porch Lights (Int/Ext) | E4:B0:63:61:C9:EC | Porch |
| .84 | Tapo Smart Plug — Lounge Small Table Lamp | 0C:EF:15:00:18:3B | Living Room |
| .85–.109 | *(Future: Shelly switches, dimmers, lamp plugs)* | | |

### Switching / Power (.110–.129)

| IP | Device | MAC | Location |
|---|---|---|---|
| .110 | Tapo P304M — Behind Lounge TV (AV) | 0C:EF:15:CA:A6:9E | Living Room |
| .111 | Tapo P304M — Behind TV (Gaming) | 0C:EF:15:CA:A6:D4 | Living Room |
| .112 | Tapo P304M — Lounge Table | 0C:EF:15:CA:AE:76 | Living Room |
| .113 | Tapo Smart Power Strip — Porch | 98:BA:5F:84:2D:41 | Porch |
| .114–.129 | *(Future: additional power strips, smart sockets)* | | |

### Miscellaneous / Utility (.130–.139)

| IP | Device | MAC | Location |
|---|---|---|---|
| .130 | Shelly 1PM Gen4 — Clocktower Motor | E4:B0:63:7C:AE:10 | Loft |
| .131 | Shelly 1PM Gen4 — Christmas Tree Lights | A0:85:E3:BC:8B:50 | Living Room |
| .132–.139 | *(Future: seasonal devices, utility controllers)* | | |
