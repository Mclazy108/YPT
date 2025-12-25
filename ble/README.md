# YPT BLE Contract

This directory defines the BLE "contract" between:
- ESP32 firmware (BLE peripheral, ESP-IDF C)
- Flutter iOS app (BLE central)

Do not hardcode UUIDs or packet formats anywhere else.
Instead, copy the generated files from `ble/generated/` into:
- firmware: include the `.h` files
- flutter: include the `.dart` files

## Service

**Name (advertised):** YPT-Detector
**Service UUID:** fb99838c-9ab5-4e2d-b791-db715296b50b

## Characteristics

### 1) Punch Event (Notify)
UUID: 7e30f69a-dcda-4a31-ac48-3d40c55e0f56
Properties: Notify  
Payload: 11 bytes, little-endian

- uint32 seq
- uint32 t_ms
- uint16 force
- uint8  flags

Flags:
- bit0: validPunch (1 = punch event)
- bit1: clipped (1 = sensor saturated/clipped)
- bit2: calibrated (1 = force is calibrated to a known scale)
- bits3-7: reserved (0)

### 2) Session Summary (Read + Notify)
UUID: a088aa46-a842-4bc3-b5ba-299481db4327
Properties: Read, Notify  
Payload: 10 bytes, little-endian

- uint32 session_ms
- uint32 punch_count
- uint16 last_force

### 3) Control (Write)
UUID: 810f79ac-3e1b-4a0a-acee-b5c215abf231
Properties: Write  
Payload: 1+ bytes. Currently uses only the first byte.

Commands:
- 0x01 start session (resets counters + starts timer)
- 0x02 stop session (freezes timer)
- 0x03 reset counters (if running, resets timer)

