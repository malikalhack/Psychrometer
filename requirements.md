# Psychrometer — Requirements & Task Backlog

**Project:** Psychrometer
**Author:** Anton Chernov
**Date:** 06/29/2026
**Version:** 0.1.0

---

## 1. Requirements

### REQ-001 — Ultra-low Power Sensor Node

A sensor node shall spend almost all of its time in a deep low-power mode and
wake only to sample and transmit.

**Acceptance criteria:**
- The MSP430G2553 stays in LPM3/LPM4 between transmit cycles.
- The BME280 is held in forced/sleep mode and sampled on demand only.
- The nRF24L01+ is powered down between transmissions.
- Sensor power may be gated (high-side switch) to remove residual current.
- Sleep current of the node (MCU + sensor + radio) is in the microamp range.

---

### REQ-002 — Battery and Solar Operation

Indoor nodes shall run from a coin or AA battery; the outdoor node shall be
self-sustaining from a small solar panel.

**Acceptance criteria:**
- Indoor node life from 2xAA Li ≥ 1 year at the default 10-minute period.
- Outdoor node: 5.28 V / 0.37 W panel + LiFePO4/supercap, no battery swaps.
- A documented current/energy budget per transmit cycle.

---

### REQ-003 — Sensor: BME280

Nodes shall measure temperature, humidity and pressure with a Bosch BME280
over I2C.

**Acceptance criteria:**
- Calibration coefficients read once and applied per the datasheet.
- One forced measurement per transmit cycle; oversampling tuned for power.
- Driver isolated so a future sensor can replace it without app changes.

---

### REQ-004 — Radio: nRF24L01+ Star Network

The network shall be a star: up to 20 nodes report to one hub over
nRF24L01+ (GT-24-Mini module, 3.3 V supply and logic).

**Acceptance criteria:**
- Hub is PRX; nodes are PTX. Each node has a unique pipe address.
- Auto-ACK with retransmit; node confirms delivery before sleeping.
- Up to 20 nodes (~10 planned plus expansion).

---

### REQ-005 — Transmit Period

Default transmit period is 10 minutes for indoor and outdoor nodes.

**Acceptance criteria:**
- Period is a compile-time constant, tunable per node.
- Timing derived from the VLO/ACLK low-power timer.

---

### REQ-006 — Hub with Display

The hub shall receive readings from all nodes and show them on an LCD; it does
not forward to any network.

**Acceptance criteria:**
- Hub MCU STM32F103C8T6 + nRF24L01+ + LCD.
- Displays per-node temperature, humidity, pressure and last-seen time.
- Marks a node stale if no packet within N periods.

---

### REQ-007 — Packet Format

A compact fixed payload (≤ 32 bytes) shall carry node id, sensor data and a
status/battery field.

**Acceptance criteria:**
- Fields: node id, T, H, P, battery/status; documented byte layout.
- Endianness defined; same struct on node and hub.

---

### REQ-008 — Multi-platform Structure

Common code shall be MCU-independent; node (MSP430) and hub (STM32) specifics
live in the port layer.

**Acceptance criteria:**
- `inc/` and `src/` contain no MCU-specific registers.
- Node and hub builds select code via the port directory only.

---

### REQ-009 — No Dynamic Memory

Static allocation only; no `malloc`/`free`.

---

### REQ-010 — Doxygen Documentation

All public headers, types and functions documented with Doxygen tags;
`docs/` holds the Doxyfile and diagrams.

---

## 2. Task Backlog

### Phase 0 — Foundation
- [ ] T001 — Repository structure (`inc/ src/ docs/`)
- [ ] T002 — Requirements, HISTORY, CHANGELOG
- [ ] T003 — Toolchain decision (CCS for node, CMSIS-Toolbox for hub)

### Phase 1 — Node bring-up
- [ ] T010 — MSP430G2553 clock + LPM3 + low-power timer
- [ ] T011 — I2C + BME280 driver, forced read
- [ ] T012 — SPI + nRF24L01+ driver, PTX
- [ ] T013 — Packet struct + battery sense
- [ ] T014 — Sleep/wake cycle, current budget

### Phase 2 — Hub
- [ ] T020 — STM32F103 + nRF24L01+ PRX, multi-pipe
- [ ] T021 — LCD render, per-node table + stale flag

### Phase 3 — Outdoor variant
- [ ] T030 — Solar + LiFePO4/supercap power path
- [ ] T031 — Field power validation

### Phase 4 — Documentation
- [ ] T040 — Doxygen, schematics, budget tables
