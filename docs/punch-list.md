# Post-Migration Punch List

**Created**: 21 February 2026 (end of Day 1)  
**Status**: 🔧 Pending — to complete in Day 2 session  
**Last nmap**: 16:43 UTC, 21 Feb 2026

---

## Summary

Day 1 successfully migrated 28 devices to their target IPs across all functional bands. The core network structure is in place. This punch list covers remaining verification, fixes, and deferred items.

---

## Priority 1 — Fix Broken Integrations

### 1.1 MeacoDry Dehumidifier (.63) — Local Tuya integration offline
- **Problem**: Local Tuya integration in HA had hardcoded IP .67 (old address). Updated to .63 but device still showing offline.
- **Action**: 
  - [ ] Power cycle the dehumidifier
  - [ ] Confirm .63 is pingable after power cycle
  - [ ] Check Tuya/Smart Life app — is device online there?
  - [ ] In HA: Settings → Devices & Services → Local Tuya → reconfigure with IP .63
  - [ ] If Local Tuya won't reconnect, try deleting and re-adding the integration
  - [ ] Verify HA can control the dehumidifier

### 1.2 Hikvision camera feed in HA
- **Problem**: Door station moved from .41 → .30. HA Hikvision integration likely still points to old IP.
- **Action**:
  - [ ] In HA: Settings → Devices & Services → Hikvision → reconfigure with IP .30
  - [ ] Verify live camera feed works in HA
  - [ ] Test doorbell press → notification in HA

---

## Priority 2 — Verify All HA Integrations

Walk through each integration in HA (Settings → Devices & Services) and confirm devices are online.

### 2.1 Shelly devices (6× 1PM + 1× 2PM)
- [ ] Cloakroom Light (.80) — toggle on/off from HA
- [ ] Landing Lights (.81) — toggle on/off from HA
- [ ] Loft Lights (.82) — toggle on/off from HA
- [ ] Porch Lights Int/Ext (.83) — toggle both channels from HA
- [ ] Heated Towel Rail (.64) — toggle + verify power monitoring in HA
- [ ] Clocktower Motor (.130) — toggle from HA
- [ ] Christmas Tree (.131) — toggle from HA (seasonal, may not be connected)

### 2.2 Tapo devices (3× P304M + 1× strip + 1× plug)
- [ ] AV strip (.110) — check HA control
- [ ] Gaming strip (.111) — check HA control (⚠ powers Deco mesh node)
- [ ] Lounge Table strip (.112) — check HA control
- [ ] Porch strip (.113) — check HA control (⚠ powers doorbell PoE + Octopus Mini)
- [ ] Lamp plug (.84) — check HA control

### 2.3 Sonoff Immersion Controller (.60)
- [ ] Verify HA can see it (eWeLink integration)
- [ ] Test: toggle immersion on/off from HA
- [ ] Confirm power monitoring working

### 2.4 Tuya UFH Controller (.62)
- [ ] Verify HA connectivity
- [ ] Check if this uses Local Tuya or cloud Tuya — may need IP update like dehumidifier

### 2.5 Sony Bravia (.50)
- [ ] Verify HA integration reconnected
- [ ] Test: turn TV on/off from HA

### 2.6 Sonos Play:5 (.51)
- [ ] Verify plays audio
- [ ] Check Sonos app recognises speaker

### 2.7 Octopus Energy
- [ ] Verify energy data still flowing in HA (API/cloud — should be fine)

### 2.8 Nabu Casa remote access
- [ ] Verify remote access works from phone (off WiFi)

---

## Priority 3 — Deferred Device Migrations

These devices were offline during Day 1 and need reservations + setup when available.

### 3.1 Samsung Bedroom TV → .52
- **MAC**: 74:6D:FA:75:41:B0
- [ ] Power on TV
- [ ] Reservation already created — verify it picks up .52
- [ ] Verify HA Samsung integration reconnects

### 3.2 Samsung Kitchen TV → .53
- **MAC**: Currently randomised (BD:DF:58:3B:B3:3B seen previously)
- [ ] Disable MAC randomisation on TV: Settings → Network → disable Private WiFi / random MAC
- [ ] Note the real MAC address
- [ ] Create DHCP reservation for .53
- [ ] Verify HA integration

### 3.3 Android Tablet (HA Lounge Panel) 
- **Current IP**: .27 (floating in DHCP)
- [ ] Disable MAC randomisation on tablet
- [ ] Note real MAC address
- [ ] Decide target IP — suggest .23 (Smart Home Core range, as it's an HA panel)
- [ ] Create DHCP reservation
- [ ] Verify HA dashboard loads on tablet

### 3.4 Sky Puck → .54
- [ ] Power on when convenient
- [ ] Note MAC address
- [ ] Create DHCP reservation for .54

### 3.5 PlayStation 4 → .55
- [ ] Power on when convenient
- [ ] Note MAC address
- [ ] Create DHCP reservation for .55

---

## Priority 4 — Security Hardening

### 4.1 Brother Printer (.41) — disable unnecessary services
- [ ] Browse to `http://192.168.1.41`
- [ ] Disable FTP (port 21)
- [ ] Disable Telnet (port 23)
- [ ] Disable SMTP (port 25)
- [ ] Verify printing still works after changes

### 4.2 Tapo critical outlet locking
- [ ] In Tapo app: lock the outlet powering Deco mesh node on Gaming strip (.111) to "always on"
- [ ] In Tapo app: lock the outlets powering doorbell PoE and Octopus Mini on Porch strip (.113) to "always on"

### 4.3 Hikvision indoor station gateway verification
- [ ] Confirm Lounge station (.31) gateway is set to .1 (was .200)
- [ ] Confirm Office station (.32) gateway is set to .1 (was .200)
- [ ] Test intercom: press doorbell → both stations ring
- [ ] Check Hikvision firmware versions — update if available

---

## Priority 5 — Firmware & Maintenance

### 5.1 Shelly Porch Lights (.83) firmware
- [ ] Currently on beta firmware 1.7.99
- [ ] Check if stable release available via Shelly web UI at `http://192.168.1.83`
- [ ] Update if stable version available

### 5.2 Deco firmware
- [ ] Currently on 1.1.0, update 1.1.1 available
- [ ] Apply after migration is fully verified and stable
- [ ] Re-backup Deco config after firmware update

---

## Priority 6 — Housekeeping & Documentation

### 6.1 Mystery devices to identify
- **58:BF:25:46:D6:52** (Espressif) — parked at .150. Possibly backup Sonoff immersion controller?
  - [ ] Check Shelly/Sonoff web UI at `http://192.168.1.150`
  - [ ] Identify and assign to correct functional band or decommission
- **4C:75:25:20:2C:21** — reserved in Deco, unknown origin
  - [ ] Identify or remove reservation
- **ShellyHTG3 54:32:04:57:B1:D0** and **54:32:04:57:9B:B4** — spotted by IP Scanner
  - [ ] Identify — temperature/humidity sensors? New purchases?
  - [ ] Assign to appropriate functional band

### 6.2 Backfill placeholder reservations
- [ ] After all devices confirmed, create placeholder reservations across functional ranges to prevent DHCP squatting
- [ ] This ensures new devices (phones, laptops, guests) always land in the .170-.220 DHCP pool
- [ ] Suggested approach: reserve unused IPs in .30-.139 range with dummy entries

### 6.3 Update documentation
- [ ] Update dhcp-scheme.md with actual mesh node IPs (.247-.250, Deco-managed)
- [ ] Update dhcp-scheme.md to note indoor stations are static (not DHCP reservations)
- [ ] Update network-overview.md with gateway change (.254 → .1)
- [ ] Update inventory spreadsheet with all new IPs
- [ ] Run final nmap scan and save to backups/
- [ ] Note Deco DHCP pool range change (50-250 → 20-250)

### 6.4 Yealink web admin access
- [ ] Try default credentials again (admin/admin) after lockout period
- [ ] If accessible, change default password
- [ ] Export config as backup

---

## Final Verification — Room Walkthrough

Once all punch list items are complete, do the Phase 11 room walkthrough:

- [ ] **Study**: HA dashboard loads, printer prints, Yealink rings
- [ ] **Living Room**: TV turns on via HA, Sonos plays, lights toggle, doorbell indoor station works
- [ ] **Kitchen**: HA Voice responds, Samsung TV in HA
- [ ] **Office**: Indoor station rings, wife's PC can print
- [ ] **Porch**: Porch lights toggle (interior + exterior), doorbell camera live
- [ ] **Loft**: Landing lights, loft lights toggle, clock motor controllable
- [ ] **Bathroom**: Towel rail toggles, power monitoring shows in HA
- [ ] **Cloakroom**: Light toggles
- [ ] **Upstairs Landing**: Dehumidifier controllable

---

## Current Network State (as of 16:43 UTC, 21 Feb 2026)

### Devices at target IPs ✅
| IP | Device | Status |
|---|---|---|
| .1 | Deco X50-5G Gateway | ✅ Confirmed |
| .20 | Home Assistant | ✅ Confirmed |
| .21 | HA Voice Preview | ✅ Confirmed |
| .22 | Octopus Energy Mini | ✅ Confirmed |
| .30 | Hikvision Door Station | ✅ Confirmed |
| .31 | Hikvision Indoor (Lounge) | ✅ Confirmed (static) |
| .32 | Hikvision Indoor (Office) | ✅ Confirmed (static) |
| .40 | Yealink W70B | ✅ Confirmed |
| .41 | Brother Printer | ✅ Confirmed |
| .50 | Sony Bravia | ✅ Confirmed |
| .51 | Sonos Play:5 | ✅ Confirmed |
| .60 | Sonoff Immersion | ✅ Confirmed |
| .62 | Tuya UFH Controller | ✅ Confirmed |
| .63 | MeacoDry Dehumidifier | ✅ On network, HA integration broken |
| .64 | Shelly Towel Rail | ✅ Confirmed |
| .80 | Shelly Cloakroom Light | ✅ Confirmed |
| .81 | Shelly Landing Lights | ✅ Confirmed |
| .82 | Shelly Loft Lights | ✅ Confirmed |
| .83 | Shelly Porch Lights | ✅ Confirmed |
| .84 | Tapo Lamp Plug | ✅ Confirmed |
| .110 | Tapo AV Strip | ✅ Confirmed |
| .111 | Tapo Gaming Strip | ✅ Confirmed |
| .112 | Tapo Lounge Table Strip | ✅ Confirmed |
| .113 | Tapo Porch Strip | ✅ Confirmed |
| .130 | Shelly Clocktower Motor | ✅ Confirmed |
| .131 | Shelly Christmas Tree | ✅ Confirmed |

### Pending placement
| IP | Device | Notes |
|---|---|---|
| .52 | Samsung TV (Bedroom) | Offline — reserve when powered on |
| .53 | Samsung TV (Kitchen) | Needs MAC randomisation disabled first |
| .54 | Sky Puck | Offline — reserve when powered on |
| .55 | PS4 | Offline — reserve when powered on |
| .150 | Unknown Espressif | Quarantined — identify |

### Mesh nodes (Deco-managed)
| IP | Location |
|---|---|
| .247 | Porch |
| .248 | Study/Living Room |
| .249 | Office/Porch |
| .250 | Living Room/Office |

*Note: Mesh node IPs and location associations may shuffle between reboots.*

---

## Key Lessons Learned (Day 1)

1. **Deco DHCP reservations require router reboot** to take effect — not immediate
2. **Batch approach works well**: create multiple reservations → single router reboot
3. **Quarantine strategy**: park squatters at .150+ before moving devices to target IPs
4. **Hikvision indoor stations are static-only** — no DHCP option available
5. **DHCP pool range matters**: expanding to 20-250 caused devices to squat on reserved IPs initially
6. **Backfill placeholders** needed post-migration to prevent future squatting
7. **HA restart ≠ reboot**: restart is app-level, reboot cycles the OS (needed for DHCP renewal)
8. **Local Tuya uses hardcoded IPs**: must update in HA integration config after IP changes
