# Psychrometer — Project History

Milestone log for the Psychrometer project.
Records key decisions, structural changes, and completed phases.

---

## 2026-06-29

### Project inception
- Revived a shelved concept: miniature low-power autonomous T/H sensors.
- Concept reviewed; architecture fixed as a star network of battery nodes
  reporting to one hub.

### Concept decisions
- **Node MCU:** MSP430G2553 (ultra-low power, deep sleep between cycles).
- **Sensor:** BME280 (temperature / humidity / pressure) — chosen over AM2320
  for accuracy, low sleep current, and bonus pressure data.
- **Radio:** nRF24L01+ via GT-24-Mini module (2.4 GHz, SPI, 3.3 V supply and
  logic). Wi-Fi rejected — too power-hungry for coin/AA battery life.
- **Topology:** star, up to 20 nodes (~10 planned + expansion).
- **Range:** apartment / small house — LoRa unnecessary.
- **Period:** 10 minutes for indoor and outdoor nodes.
- **Hub:** STM32F103C8T6 + LCD; collects and displays only, no network uplink.
- **Outdoor node:** powered from 5.28 V / 0.37 W solar panel.

### Documentation
- `README.md`, `requirements.md`, `HISTORY.md`, `CHANGELOG.md` created.
- Directory structure agreed: `inc/`, `src/`, `docs/`.
