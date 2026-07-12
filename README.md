# AIOT-AQUA — Smart Aquaculture AIoT Platform

> Deployment #2 of BMS-Core AIoT Framework.
> Real-time water quality monitoring, AI-driven actuator control,
> and predictive analytics for aquaculture farms.

---

## Overview

AIOT-AQUA monitors water quality parameters (pH, dissolved oxygen,
temperature, turbidity) across fish/shrimp tanks using ESP32 sensor
nodes. An event-driven AI agent recommends actuator actions (pump,
aerator, feeder) based on real-time readings and historical patterns.

**Part of BMS-Core framework** — same architecture as Smart Campus,
different deployment configuration.

---

## Architecture

```
ESP32 Nodes (tanks)
    ↓ MQTT publish
backend_edge (PC/OPi — local)
├── Mosquitto broker
├── TimescaleDB (raw water quality)
└── Rule Engine (hard rules, no AI)
    ↓ Cloudflare tunnel
backend_cloud (i7 + RTX 3050)
├── FastAPI + WebSocket
├── AI Agent (Qwen tool calling)
├── LLM summarizer (water quality)
├── Nomic embed + pgvector
└── Gemini chatbot
    ↓ WebSocket
frontend (browser)
└── Tank map + realtime charts
    Water quality dashboard
    AI recommendation panel
```

---

## Tank States (FSM)

| State | Trigger | Actuator |
|-------|---------|----------|
| NORMAL | All params in range | — |
| PH_ALERT | pH < 7.5 or > 8.5 | Suggest pH adjustment |
| DO_ALERT | DO < 5.0 mg/L | Activate aerator |
| TEMP_ALERT | Temp out of range | Adjust heater/cooler |
| FEEDING_TIME | Schedule trigger | Activate feeder |
| EMERGENCY | 2+ params critical | Alert farmer + all actuators |

---

## Hardware

| Component | Role | Note |
|-----------|------|------|
| ESP32 | Main MCU | Per tank node |
| pH sensor (analog) | pH monitoring | Calibrate before use |
| DO sensor | Dissolved oxygen | SEN0237 or similar |
| DS18B20 | Water temperature | Waterproof |
| Turbidity sensor | Water clarity | SEN0189 |
| Relay module | Pump/aerator/feeder control | 5V |
| OLED 0.96" | Local status display | I2C |
| Buzzer | Local alert | Active buzzer |

---

## MQTT Topics

```
Publish (ESP32 → Broker):
campus/telemetry/{tank_id}       ← sensor readings
campus/device/{tank_id}/status   ← heartbeat + LWT
campus/events/emergency          ← critical alert

Subscribe (Broker → ESP32):
campus/command/{tank_id}         ← actuator commands
```

---

## Sensor Thresholds (default)

```yaml
# Per species — adjust in config.yaml
shrimp:
  ph:          [7.5, 8.5]
  do:          [5.0, 8.0]   # mg/L
  temp:        [25.0, 30.0] # °C
  turbidity:   [0, 50]      # NTU

tilapia:
  ph:          [6.5, 8.5]
  do:          [4.0, 8.0]
  temp:        [25.0, 32.0]
  turbidity:   [0, 100]
```

---

## Stack

| Layer | Technology |
|-------|-----------|
| Firmware | ESP32 Arduino framework |
| Protocol | MQTT (Mosquitto) |
| Edge DB | TimescaleDB |
| Tunnel | Cloudflare Tunnel |
| Backend | FastAPI + asyncio |
| AI Agent | Qwen 1.5B Q4 (Ollama) |
| Embedding | Nomic embed (self-hosted) |
| Vector store | pgvector |
| Chatbot | Gemini API |
| Frontend | HTML + Chart.js + WebSocket |

---

## Team

| Member | Role |
|--------|------|
| Lead | Architecture + AI pipeline + Review |
| Member 1 | Firmware ESP32 + sensors |
| Member 2 | Frontend dashboard |
| Member 3 | Docs + slide + demo script |

---

## Getting Started

```bash
# Clone
git clone https://github.com/you/aiot-aqua

# Backend cloud
cd backend_cloud
pip install -r requirements.txt
cp .env.example .env   # fill in your keys
python main.py

# Backend edge
cd backend_edge
pip install -r requirements.txt
python main.py

# Frontend
cd frontend
open index.html   # or serve with live-server
```

---

## Future

- Predictive water quality (EWMA → Linear Regression)
- Camera + CV fish behavior detection
- Auto feeding schedule optimization
- Multi-farm management
- Mobile app alerts
