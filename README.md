# AIOT-AQUA — Intelligent Aquarium AIoT Platform

> Deployment #2 of **BMS-Core AIoT Framework**
> Real-time aquarium monitoring, intelligent automation, and AI-assisted water quality management.

---

# Overview

AIOT-AQUA is an AIoT platform for monitoring and managing planted aquariums and ornamental shrimp tanks

The platform continuously collects environmental data from distributed ESP32 sensor nodes, processes telemetry at the edge, synchronizes data to the cloud, and provides intelligent recommendations through an AI assistant.

Although demonstrated on a desktop aquarium, the architecture is designed to scale directly to commercial aquaculture deployments with minimal modification.


# Objectives

* Continuous water quality monitoring
* Intelligent aquarium automation
* Edge-first architecture
* AI-assisted decision support
* Reusable AIoT platform for multiple deployments

---

# Architecture

```
                   +----------------------+
                   |      Frontend        |
                   |----------------------|
                   | Dashboard            |
                   | Water Quality        |
                   | Tank Visualization   |
                   | AI Assistant         |
                   +----------▲-----------+
                              │ WebSocket
                              │
                    +---------┴----------+
                    |   Backend Cloud    |
                    |--------------------|
                    | FastAPI            |
                    | AI Agent           |
                    | LLM Summarizer     |
                    | pgvector           |
                    | Gemini Chatbot     |
                    +---------▲----------+
                              │
                     Cloudflare Tunnel
                              │
                    +---------┴----------+
                    |    Backend Edge    |
                    |--------------------|
                    | Mosquitto          |
                    | Rule Engine        |
                    | TimescaleDB        |
                    | Edge Cache         |
                    | Sync Queue         |
                    +---------▲----------+
                              │ MQTT
                              │
                +-------------┴-------------+
                |       ESP32 Tank Node     |
                |---------------------------|
                | pH                        |
                | Dissolved Oxygen          |
                | Temperature               |
                | Water Level               |
                | Relay                     |
                | OLED                      |
                +---------------------------+
```

---

# Platform Features

## Real-time Monitoring

* pH
* Dissolved Oxygen (DO)
* Water Temperature
* Water Level
* Device Status
* Historical Trends

---

## Intelligent Automation

Automatic control of

* Aerator
* Water Pump
* Lighting
* Auto Water Refill

---

## AI Assistant

The AI assistant analyzes sensor history and explains system behavior.

Example:

> Dissolved oxygen has gradually decreased during the last three hours.
> The aerator has been activated automatically.
> Possible causes include insufficient circulation, excessive organic waste, or increased plant respiration during nighttime.

---

# Aquarium States (Finite State Machine)

| State       | Trigger                      | Action                    |
| ----------- | ---------------------------- | ------------------------- |
| NORMAL      | All parameters normal        | Monitoring                |
| PH_ALERT    | pH outside configured range  | Recommendation            |
| DO_ALERT    | DO below threshold           | Start aerator             |
| TEMP_ALERT  | Temperature abnormal         | Notify user               |
| LOW_WATER   | Water level low              | Refill pump               |
| MAINTENANCE | Manual maintenance mode      | Suspend automation        |
| EMERGENCY   | Multiple critical parameters | Alert + emergency actions |

---

# Hardware

| Component                         | Purpose                |
| --------------------------------- | ---------------------- |
| ESP32 DevKit                      | Controller             |
| Analog pH Sensor                  | Water acidity          |
| DO Sensor (SEN0237 or equivalent) | Dissolved oxygen       |
| DS18B20                           | Water temperature      |
| Water Level Sensor                | Water level            |
| Relay Module                      | Pump / Light / Aerator |
| OLED Display                      | Local status           |
| Active Buzzer                     | Alarm                  |

---

# MQTT Topics

```
Publish

aqua/telemetry/{tank_id}
aqua/device/{tank_id}/status
aqua/events/alert

Subscribe

aqua/command/{tank_id}
```

---

# Water Quality Thresholds

## Planted Aquarium

```yaml
planted_aquarium:
  ph:
    min: 6.5
    max: 7.5

  do:
    min: 5.0
    max: 8.5

  temperature:
    min: 22
    max: 28

  water_level:
    min: 80
```

---

## Neocaridina Shrimp Tank

```yaml
shrimp_tank:
  ph:
    min: 6.5
    max: 7.5

  do:
    min: 6.0
    max: 8.5

  temperature:
    min: 20
    max: 25

  water_level:
    min: 80
```

---

# Technology Stack

| Layer         | Technology                  |
| ------------- | --------------------------- |
| Firmware      | ESP32 Arduino Framework     |
| Protocol      | MQTT                        |
| Broker        | Mosquitto                   |
| Edge Database | TimescaleDB                 |
| Backend       | FastAPI                     |
| Tunnel        | Cloudflare Tunnel           |
| AI Agent      | Qwen 1.5B (Ollama)          |
| Embedding     | Nomic Embed                 |
| Vector Store  | pgvector                    |
| Chatbot       | Gemini API                  |
| Frontend      | HTML + Chart.js + WebSocket |

---

# Dashboard

The web dashboard provides

* Live sensor values
* Historical charts
* Device health
* Water Quality Score
* Tank status
* AI recommendations
* Alert history

---

# Water Quality Score

The platform computes an overall health score.
The score combines

* pH
* DO
* Temperature
* Water Level

to provide a quick overview of aquarium health.

---

# Project Structure

```
backend_cloud/
backend_edge/
firmware/
frontend/
docs/
```

---

# Getting Started

```bash
git clone https://github.com/yourname/aiot-aqua

cd backend_edge
pip install -r requirements.txt
python main.py

cd ../backend_cloud
pip install -r requirements.txt
python main.py

cd ../frontend
```

Open

```
index.html
```

or serve using Live Server.

---

# Future Roadmap

* OTA firmware updates
* Device Registry
* Camera-based aquarium observation
* Fish and shrimp behavior analysis
* Predictive water quality forecasting
* Edge AI anomaly detection
* Digital Twin visualization
* Multi-aquarium management
* Mobile notifications

---
