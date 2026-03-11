# Home Assistant Blueprints

A collection of Home Assistant blueprints by **kramttocs**.

## Available Blueprints

<details>
<summary><strong>UPS Monitor via NUT</strong></summary>

Monitor a **Network UPS Tools (NUT)** device in Home Assistant and receive smart, debounced notifications when important UPS events occur.

This blueprint reduces noisy alerts caused by temporary sensor flapping while still notifying you of real issues like:

- UPS switching to **On Battery**
- **Low Battery**
- Battery dropping by configurable percentages
- **Self‑test failures**
- **High UPS load**

It also supports **multiple notification devices** and **cross‑platform priority notifications** for both Android and iOS.

---

# Install Blueprint

Click the button below to import the blueprint into Home Assistant.

[![Import Blueprint](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https://raw.githubusercontent.com/kramttocs/ha-blueprints/main/Automations/ups-monitor-nut.yaml)

---

# Features

## Smart Notification Filtering

Reduces false alerts caused by:

- `unknown`
- `unavailable`
- temporary NUT reconnects

## Battery Monitoring

Notify when battery drops by configurable increments.

Example:

Battery at 80%  
Battery at 70%  
Battery at 60%

## UPS Status Monitoring

Detect changes such as:

- Online
- On Battery
- Low Battery

Optional suppression of noisy **Online** notifications after reconnects.

## Self‑Test Monitoring (Optional)

Alerts if UPS self-tests report:

- Failed
- Warning
- Error
- Aborted

## Load Monitoring (Optional)

Alert if UPS load stays above a configured percentage for a defined time.

## Cross‑Platform Priority Notifications

Supports:

| Mode | Behavior |
|-----|-----|
| Normal | Standard notification |
| Android High Priority | Immediate delivery |
| iOS Time Sensitive | Breaks through Focus modes |
| Cross Platform High | Sends both Android + iOS priority flags |

## Multiple Devices

Send alerts to **any number of mobile devices**.

Example targets:

notify.mobile_app_pixel_9_pro  
notify.mobile_app_scotts_iphone

---

# Requirements

- Home Assistant
- **NUT Integration**
- **Home Assistant Mobile App** (for push notifications)

---

# Example Notifications

UPS Monitor: UPS status changed to: On Battery

UPS Monitor: Battery at 70%. Voltage: 27.2 V

UPS Monitor: UPS load high: 68% (threshold: 50%)

---

# Recommended Settings

For most UPS setups:

| Setting | Recommended |
|------|------|
| Status Debounce | 30 seconds |
| Ignore Unavailable | Enabled |
| Online Notifications | Disabled |
| Battery Interval | 10% |

---

# Credit

Original idea/starting point credit goes to: **Hacky1**.

---
</details>
