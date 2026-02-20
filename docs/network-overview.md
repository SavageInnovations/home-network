# Network Overview

## Architecture

### Internet Connection
- **Primary**: FTTP (fibre to the premises) via Deco X50-5G gateway
- **Failover**: 5G cellular built into the Deco X50-5G

### Mesh Network
- **Router/Gateway**: TP-Link Deco X50-5G (Study)
- **Mesh Nodes**: 4× TP-Link Deco PX50
  - Living Room, Porch, Office, Study
  - Backhaul: WiFi + Powerline (hybrid)

### Network Topology
- **Flat network**: Single subnet 192.168.1.0/24
- **No VLANs**: Deco system has limited VLAN support; IoT network feature too blunt for HA-integrated devices
- **Segmentation strategy**: Functional IP banding with DHCP reservations (see [dhcp-scheme.md](dhcp-scheme.md))

## Design Decisions

### Why Flat Network (Not VLANs)
Almost every IoT device on the network is integrated with Home Assistant, which requires local LAN access (HTTP, RTSP, UPnP, etc.). The Deco's built-in IoT network isolates devices from the main LAN with no granular firewall rules, which would break HA connectivity. Proper VLAN segmentation would require replacing the Deco as router with a dedicated firewall appliance (e.g., OPNsense) plus managed switching — a significant project not justified for the current threat model.

### Why Functional Banding (Not Manufacturer Banding)
Devices are grouped by **what they do**, not who made them. A Shelly controlling a towel rail sits in HVAC (.60–.79), not a Shelly range. A Tapo plug on a lamp sits in Lighting (.80–.109), not a Tapo range. This makes troubleshooting intuitive: "heating not working → check .60–.79."

### Devices Not Segmented
Samsung TVs, Sky Puck, and PS4 could theoretically go on the Deco IoT SSID, but the Samsung TVs are HA-integrated, and the remaining devices (Sky Puck, PS4) are too few to justify a separate SSID.

## Smart Home Ecosystem

| Platform | Devices | Integration |
|---|---|---|
| Home Assistant | Hub + Voice satellite | Core controller |
| Shelly Gen4 | 6× 1PM, 1× 2PM | Local HTTP, BLE gateways |
| Tapo | 3× P304M strips, 1× strip, 1× plug | Local via HA integration |
| Sonoff | 1× immersion controller (+1 backup) | eWeLink / HA |
| Tuya | Dehumidifier, UFH controller | Tuya cloud / HA |
| Hikvision | Door station + 2× indoor stations | RTSP, port 8000 |
| Sonos | Play:5 | UPnP / RTSP |

## Security Posture

### Mitigations in Place
- NAT (no inbound port forwarding)
- HA remote access via Nabu Casa cloud relay (no direct exposure)
- DHCP reservations for all fixed devices

### Known Issues (see migration plan)
- Brother printer: FTP, Telnet, SMTP open
- Windows PC: RDP and SMB exposed
- Hikvision: historical CVEs, firmware currency TBC
- Shelly devices: no authentication (acceptable on home LAN)
- Tapo strips powering critical infrastructure (mesh node, doorbell PoE)

## Future Considerations
- **Local DNS**: Pi-hole or AdGuard Home for ad-blocking and local name resolution
- **VLAN migration**: Current IP scheme maps cleanly to VLANs if needed later
- **Network diagram**: Physical/logical topology diagram (planned)
