# Network Migration Plan

**Status**: 🔧 In Progress  
**Planned Date**: 21 February 2026  
**Estimated Duration**: 3–4 hours  
**Risk Level**: Medium (temporary loss of smart home control during migration)

---

## Pre-Migration Checklist

Complete these **before** starting the migration.

### Backups
- [ ] Export Deco configuration (Deco app → More → Backup)
- [ ] Create Home Assistant full snapshot (Settings → System → Backups → Create Backup)
- [ ] Screenshot/export current DHCP reservation list from Deco
- [ ] Note down any Shelly webhook URLs configured in HA
- [ ] Export Yealink config (web admin → Settings → Configuration → Export)
- [ ] Document current Hikvision network settings (web admin for each device)
- [ ] Save a copy of HA's `configuration.yaml` and any relevant automation files

### Confirm Before Starting
- [ ] **CONFIRM**: How are Shelly IPs currently assigned? (static on device / DHCP reservation on router)
- [ ] **CONFIRM**: Can the Deco gateway IP be changed to .1? (check Deco app/admin)
- [ ] **CONFIRM**: Can Deco mesh node IPs be manually reserved? (or does Deco manage internally)
- [ ] **CONFIRM**: Is HA accessible via Nabu Casa cloud? (useful as fallback during migration)
- [ ] All household members informed of potential WiFi/smart home disruption
- [ ] Phone charged (you'll need the Deco app)
- [ ] Have a wired ethernet connection available to the gateway if possible (fallback access)

---

## Migration Phases

### Phase 0 — Reduce DHCP Lease Time
**Risk**: None  
**Duration**: 5 minutes (then wait ~2 hours for leases to cycle)

> Shortening the lease time means devices will pick up new settings faster after changes.

- [ ] In Deco admin, reduce DHCP lease time to **5 minutes** (or shortest available)
- [ ] Note the original lease time: `________` (to restore later)
- [ ] Wait for existing leases to expire (ideally do this the evening before)

---

### Phase 1 — Gateway Migration
**Risk**: 🔴 High — brief network outage for all devices  
**Duration**: 10–15 minutes  
**Rollback**: Change gateway IP back to .254

> This is the most impactful single change. All devices currently know the gateway as .254.

- [ ] Change Deco X50-5G gateway IP from `.254` → `.1`
- [ ] Update DHCP server to advertise `.1` as gateway
- [ ] Verify the Deco app still connects
- [ ] Verify internet access from your phone
- [ ] Verify internet access from a computer
- [ ] Note any devices that lost connectivity (may need reboot to pick up new gateway)

**If Deco doesn't allow changing gateway IP to .1:**
- [ ] Use `.10` or another address in the infrastructure range
- [ ] Update the DHCP scheme documentation accordingly

**Post-step notes:**
```
Gateway moved to: .___
Any issues:
```

---

### Phase 2 — Mesh Node Reservations
**Risk**: 🟡 Medium — mesh nodes may need reboot  
**Duration**: 10 minutes

> Deco may manage mesh node IPs internally. If so, document their addresses and skip this step.

- [ ] Check if Deco allows DHCP reservations for mesh nodes
- [ ] If yes: reserve `.2` (Living Room), `.3` (Porch), `.4` (Office), `.5` (Study)
- [ ] If no: document the IPs Deco assigns and update the scheme
- [ ] Verify all mesh nodes show as connected in Deco app

**Post-step notes:**
```
Mesh nodes at: .___  .___  .___  .___
Deco manages internally? Y/N:
```

---

### Phase 3 — Home Assistant
**Risk**: 🔴 High — HA is central to everything  
**Duration**: 15–20 minutes

> HA's IP is referenced by many devices and integrations. Move this early so you can use HA to verify subsequent device moves.

- [ ] Change HA's IP from `.123` → `.20`
  - [ ] If HA uses DHCP reservation: update reservation on Deco to `.20`
  - [ ] If HA has static IP: change in HA network settings (Settings → System → Network)
- [ ] Reboot HA
- [ ] Verify HA web interface accessible at `http://192.168.1.20:8123`
- [ ] Verify Nabu Casa remote access still works (if applicable)
- [ ] Update any browser bookmarks to new IP

**HA integrations that may reference the old IP (.123):**
- [ ] Check Hikvision integration settings
- [ ] Check Shelly webhook callback URLs
- [ ] Check any MQTT broker configuration
- [ ] Check Sonos integration
- [ ] Check Samsung TV integration
- [ ] Check Tuya integration (likely cloud-based, may not need updating)
- [ ] Check Tapo integration
- [ ] Check Octopus Energy integration (likely cloud/API, may not need updating)

- [ ] Move HA Voice Preview: `.82` → `.21`
- [ ] Move Octopus Mini: `.65` → `.22`
- [ ] Verify HA Voice responds
- [ ] Verify Octopus energy data flowing in HA

**Post-step notes:**
```
HA accessible at .20? Y/N:
Nabu Casa working? Y/N:
Integrations needing update:
```

---

### Phase 4 — Security / Intercom (Hikvision)
**Risk**: 🟡 Medium — intercom system down during migration  
**Duration**: 15–20 minutes

> Hikvision devices talk to each other (door station ↔ indoor stations). Move them together.

- [ ] Move Door Station: `.41` → `.30`
  - [ ] Update DHCP reservation on Deco
  - [ ] Reboot door station (or power cycle PoE)
  - [ ] Verify web admin accessible at `http://192.168.1.30`
- [ ] Move Indoor Station (Lounge): `.42` → `.31`
  - [ ] Verify intercom link to door station works
- [ ] Move Indoor Station (Office): `.43` → `.32`
  - [ ] Verify intercom link to door station works
- [ ] Update Hikvision integration in HA with new IPs
- [ ] Test: press doorbell → verify both indoor stations ring
- [ ] Test: view live camera feed in HA

**Post-step notes:**
```
Doorbell working? Y/N:
Indoor stations linked? Y/N:
HA camera feed working? Y/N:
```

---

### Phase 5 — Communications & Office
**Risk**: 🟢 Low  
**Duration**: 10 minutes

- [ ] Move Yealink W70B: `.129` → `.40`
  - [ ] Update DHCP reservation
  - [ ] Reboot base station
  - [ ] Test: make/receive a call
- [ ] Move Brother Printer: `.34` → `.41`
  - [ ] Update DHCP reservation
  - [ ] **While here**: disable FTP (21), Telnet (23), SMTP (25) via `http://192.168.1.41`
  - [ ] Test: print a test page from a computer

**Post-step notes:**
```
VoIP working? Y/N:
Printer working? Y/N:
Printer unnecessary services disabled? Y/N:
```

---

### Phase 6 — AV / Entertainment
**Risk**: 🟢 Low  
**Duration**: 15 minutes

- [ ] Move Sony Bravia: `.72` → `.50`
  - [ ] Verify HA integration reconnects
- [ ] Move Sonos Play:5: `.74` → `.51`
  - [ ] Verify plays audio
- [ ] Move Samsung TV (Bedroom): `.76` → `.52`
  - [ ] Verify HA integration reconnects
- [ ] Samsung TV (Kitchen): 
  - [ ] Disable MAC randomisation on TV first
  - [ ] Then reserve `.53`
- [ ] Sky Puck and PS4: power on and assign `.54` / `.55` when convenient (not urgent)

**Post-step notes:**
```
Sony Bravia in HA? Y/N:
Sonos playing? Y/N:
Samsung Bedroom in HA? Y/N:
Samsung Kitchen MAC fixed? Y/N:
```

---

### Phase 7 — HVAC / Climate
**Risk**: 🟡 Medium — controls heating and hot water  
**Duration**: 15 minutes

> These are safety-critical. Verify each device is working before moving to the next.

- [ ] Move Sonoff Immersion Controller: `.52` → `.60`
  - [ ] Verify HA can control it
  - [ ] Test: toggle immersion on/off from HA
- [ ] Move Tuya UFH Controller: `.75` → `.62`
  - [ ] Verify connectivity
- [ ] Move MeacoDry Dehumidifier: `.67` → `.63`
  - [ ] Verify HA integration
- [ ] Move Shelly Heated Towel Rail: `.55` → `.64`
  - [ ] Verify HA control and power monitoring

**Post-step notes:**
```
Immersion controllable? Y/N:
UFH controller online? Y/N:
Dehumidifier in HA? Y/N:
Towel rail in HA? Y/N:
```

---

### Phase 8 — Lighting
**Risk**: 🟢 Low  
**Duration**: 15 minutes

- [ ] Move Shelly Cloakroom Light: `.61` → `.80`
- [ ] Move Shelly Landing Lights: `.62` → `.81`
- [ ] Move Shelly Loft Lights: `.63` → `.82`
- [ ] Move Shelly Porch Lights: `.64` → `.83`
  - [ ] **While here**: check if stable firmware available (currently beta 1.7.99)
- [ ] Move Tapo Lamp Plug: `.87` → `.84`
- [ ] For each: verify HA can toggle on/off

**Post-step notes:**
```
All lights controllable from HA? Y/N:
Porch Shelly firmware updated? Y/N:
```

---

### Phase 9 — Switching / Power
**Risk**: 🟡 Medium — some strips power critical infrastructure  
**Duration**: 10 minutes

> ⚠ Do NOT power-cycle Tapo strips that control the Deco mesh node (.111) or doorbell PoE (.113). Only change their DHCP reservations.

- [ ] Move Tapo P304M (AV): `.57` → `.110`
- [ ] Move Tapo P304M (Gaming): `.60` → `.111`
  - [ ] ⚠ Powers Deco mesh node — verify mesh node stays online
- [ ] Move Tapo P304M (Lounge Table): `.53` → `.112`
- [ ] Move Tapo Strip (Porch): `.70` → `.113`
  - [ ] ⚠ Powers doorbell PoE + Octopus Mini — verify both stay online
- [ ] **While here**: lock critical outlets to "always on" in Tapo app

**Post-step notes:**
```
All strips in HA? Y/N:
Critical outlets locked? Y/N:
Mesh node still online? Y/N:
Doorbell still online? Y/N:
```

---

### Phase 10 — Miscellaneous / Utility
**Risk**: 🟢 Low  
**Duration**: 5 minutes

- [ ] Move Shelly Clocktower Motor: `.56` → `.130`
- [ ] Move Shelly Christmas Tree Lights: `.79` → `.131`
- [ ] Verify HA control for both

---

### Phase 11 — Post-Migration
**Risk**: None  
**Duration**: 15 minutes

- [ ] Restore DHCP lease time to original value: `________`
- [ ] Run a new nmap scan to verify all devices on expected IPs
- [ ] Walk through each room and test key functions:
  - [ ] **Study**: HA dashboard loads, printer prints, Yealink rings
  - [ ] **Living Room**: TV turns on via HA, Sonos plays, lights toggle, doorbell indoor station works
  - [ ] **Kitchen**: HA Voice responds, Samsung TV in HA
  - [ ] **Office**: Indoor station rings, wife's PC can print
  - [ ] **Porch**: Porch lights toggle (interior + exterior), doorbell camera live
  - [ ] **Loft**: Landing lights, loft lights toggle, clock motor controllable
  - [ ] **Bathroom**: Towel rail toggles, power monitoring shows in HA
  - [ ] **Cloakroom**: Light toggles, motion sensor triggers
  - [ ] **Upstairs Landing**: Dehumidifier controllable
- [ ] Check HA automations are firing correctly (trigger a few manually)
- [ ] Verify Octopus energy data still flowing
- [ ] Verify Nabu Casa remote access
- [ ] Update inventory spreadsheet with new IPs
- [ ] Commit all changes to git repo

**Post-migration notes:**
```
All devices migrated? Y/N:
Any devices still on old IPs:
Any automations broken:
New nmap scan saved? Y/N:
```

---

## Rollback Plan

If things go seriously wrong at any phase:

1. **Restore Deco config** from the backup taken in pre-migration
2. **Restore HA snapshot** if HA configuration was corrupted
3. **Devices with static IPs** will need manual reversion — this is why we document the "Current IP" column
4. **DHCP-assigned devices** will pick up old settings automatically after Deco config restore

The most important thing is: **don't panic, don't rush**. Every change is reversible. The original IPs are documented in the DHCP reservation spreadsheet's "Current IP" column.

---

## Changelog

| Date | Phase | Notes |
|---|---|---|
| | | |
