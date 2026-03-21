# Home Assistant Blueprints

A collection of Home Assistant blueprints by **kramttocs**.

---

## Available Blueprints

<details>
<summary><strong>UPS Monitor via NUT</strong></summary>

Monitor a **Network UPS Tools (NUT)** device in Home Assistant and receive smart, debounced notifications when important UPS events occur.

This blueprint reduces noisy alerts caused by temporary sensor flapping while still notifying you of real issues like:

- UPS switching to **On Battery**
- **Low Battery**
- Battery dropping by configurable percentages
- **Self-test failures**
- **High UPS load**

It also supports **multiple notification devices** and **cross-platform priority notifications** for both Android and iOS.

---

### Install Blueprint

Click the button below to import the blueprint into Home Assistant.

[![Import Blueprint](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https://raw.githubusercontent.com/kramttocs/ha-blueprints/main/Automations/ups-monitor-nut.yaml)

---

### Features

#### Smart Notification Filtering

Reduces false alerts caused by:

- `unknown`
- `unavailable`
- temporary NUT reconnects

#### Battery Monitoring

Notify when battery drops by configurable increments.

Example:

- Battery at 80%
- Battery at 70%
- Battery at 60%

#### UPS Status Monitoring

Detect changes such as:

- Online
- On Battery
- Low Battery

Optional suppression of noisy **Online** notifications after reconnects.

#### Self-Test Monitoring (Optional)

Alerts if UPS self-tests report:

- Failed
- Warning
- Error
- Aborted

#### Load Monitoring (Optional)

Alert if UPS load stays above a configured percentage for a defined time.

#### Cross-Platform Priority Notifications

Supports:

| Mode | Behavior |
|------|----------|
| Normal | Standard notification |
| Android High Priority | Immediate delivery |
| iOS Time Sensitive | Breaks through Focus modes |
| Cross Platform High | Sends both Android and iOS priority flags |

#### Multiple Devices

Send alerts to **any number of mobile devices**.

Example targets:

- `notify.mobile_app_pixel_9_pro`
- `notify.mobile_app_scotts_iphone`

---

### Requirements

- Home Assistant
- **NUT Integration**
- **Home Assistant Mobile App** for push notifications

---

### Example Notifications

- `UPS Monitor: UPS status changed to: On Battery`
- `UPS Monitor: Battery at 70%. Voltage: 27.2 V`
- `UPS Monitor: UPS load high: 68% (threshold: 50%)`

---

### Recommended Settings

For most UPS setups:

| Setting | Recommended |
|---------|-------------|
| Status Debounce | 30 seconds |
| Ignore Unavailable | Enabled |
| Online Notifications | Disabled |
| Battery Interval | 10% |

---

### Credit

Original idea and starting point credit goes to **Hacky1**.

</details>

<details>
<summary><strong>Blue Iris - Last Event Notifications</strong></summary>

Send actionable notifications from Blue Iris last event sensors using a single automation blueprint.

This blueprint is designed to make Blue Iris notifications cleaner and easier to manage by supporting multiple cameras, alarm-aware behavior, event filtering, optional mute support, and direct navigation back into your camera dashboard.

---

### Install Blueprint

Click the button below to import the blueprint into Home Assistant.

[![Import Blueprint](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https://raw.githubusercontent.com/kramttocs/ha-blueprints/main/Automations/blueiris-last-event-notifications.yaml)

---

### Features

#### Camera-Aware Notifications

- Supports multiple Blue Iris `*_last_event` sensors in a single automation
- Automatically derives the matching camera entity from the selected sensor
- Includes the camera name in the notification title
- Uses the latest Blue Iris snapshot image when available

#### Event Filtering

Only notify for the event types you care about.

Examples:

- `motion_person`
- `motion_vehicle`
- `motion_animal`

#### Alarm-Aware Behavior

Notifications are only sent when the selected alarm entity is armed.

Supports camera-specific suppression for:

- `armed_home`
- `armed_away`
- `armed_night`
- `armed_vacation`

This makes it easy to ignore indoor cameras while home, but still notify from them while away.

#### Actionable Notifications

Notifications include:

- tap-to-open dashboard navigation
- an **Open Camera** action button
- an optional **Mute** action button

#### Optional Dynamic Camera Dashboard Paths

You can use a shared dashboard path, such as:

- `/lovelace/cameras`
- `/dashboard-cameras`

Or enable dynamic camera dashboard navigation so the blueprint automatically appends the camera object ID, for example:

- `/dashboard-cameras/frontdoor`
- `/dashboard-cameras/driveway`

#### Optional Notification Mute Support

Supports a configurable mute helper and mute duration.

When paired with the companion automations below, the notification mute action can temporarily suppress future Blue Iris last event notifications.

---

### Requirements

- Home Assistant
- **Blue Iris integration**
- Blue Iris last event sensors with `event_type`
- Matching Blue Iris camera entities
- **Home Assistant Mobile App** for actionable notifications
- Optional: `input_boolean` helper for notification mute support

---

### Naming Assumptions

This blueprint assumes the following naming relationship:

- Last event sensor: `sensor.<camera_object_id>_last_event`
- Matching camera: `camera.<camera_object_id>`

Example:

- `sensor.home_driveway_last_event`
- `camera.home_driveway`

---

### Blueprint Inputs

#### Cameras and Events

- **Last Event Sensors**  
  Select one or more Blue Iris last event sensors.

- **Allowed Event Types**  
  Choose which `event_type` values should trigger notifications.

#### Alarm Behavior

- **Alarm Entity**  
  Notifications are only sent when this alarm is armed.

- **Ignore Cameras when Armed Home**
- **Ignore Cameras when Armed Away**
- **Ignore Cameras when Armed Night**
- **Ignore Cameras when Armed Vacation**

These let you suppress selected cameras for specific alarm states.

#### Notification Settings

- **Notification Service**  
  Enter the notify service to use. Examples:
  - `notify.notify`
  - `notify.mobile_app_your_phone`

- **Camera Dashboard Path**  
  Path opened when tapping the notification or using the **Open Camera** action.

- **Use Dynamic Camera Dashboard Path**  
  Automatically appends the camera object ID to the configured dashboard path.

#### Notification Mute

- **Notification Mute Helper**  
  Input boolean used to suppress notifications while muted.

- **Notification Mute Duration**  
  Duration shown on the mute action button and used by the companion mute reset automation.

#### Advanced Settings

- **Ignored Sensor States**  
  States that should never generate notifications.

Default ignored states:

- `unknown`
- `unavailable`
- `none`
- `idle`
- `no event`

---

### Example Notifications

Examples include:

- `🚨 Driveway` — `Person detected while Armed Away`
- `🏠 Front Door` — `Person detected while Armed Home`
- `🌙 Backyard` — `Animal detected while Armed Night`
- `🌴 Garage` — `Vehicle detected while Armed Vacation`

---

### Optional Companion Automations for Mute Support

If you want to use the **Mute** action button, add these two companion automations.

> [!IMPORTANT]
> The helper and duration used in these companion automations must match the values selected in the blueprint.

#### 1) Actionable Notification - Blue Iris

Handles the fixed mute notification action and turns on the mute helper.

```yaml
alias: Actionable Notification - Blue Iris
description: >-
  Handles the fixed Blue Iris mute notification action and enables the
  configured notification mute helper.
triggers:
  - trigger: event
    event_type: mobile_app_notification_action
conditions:
  - alias: Only continue for the Blue Iris mute action
    condition: template
    value_template: |
      {{ trigger.event.data.action == 'MUTE_BLUEIRIS_LAST_EVENT' }}
actions:
  - alias: Turn on the Blue Iris notification mute helper
    action: input_boolean.turn_on
    target:
      entity_id:
        - input_boolean.blue_iris_mute_last_event_notification
    data: {}
  - alias: Send optional confirmation notification
    action: notify.notify
    data:
      title: Blue Iris Notifications Muted
      message: Last event notifications muted for 4 hours.
mode: queued
max: 10
```

#### 2) Blue Iris - Reset Last Event Notification Mute

Turns the mute helper back off after the selected duration.

```yaml
alias: Blue Iris - Reset Last Event Notification Mute
description: "Automatically turns off the configured Blue Iris notification mute helper after the selected mute duration."
triggers:
  - alias: Mute helper has been on for the full mute duration
    trigger: state
    entity_id:
      - input_boolean.blue_iris_mute_last_event_notification
    to:
      - "on"
    for:
      hours: 4
      minutes: 0
      seconds: 0
conditions: []
actions:
  - alias: Turn off the Blue Iris notification mute helper
    action: input_boolean.turn_off
    target:
      entity_id:
        - input_boolean.blue_iris_mute_last_event_notification
    data: {}
mode: restart
```

---

### Notes

- The blueprint uses the fixed notification action:
  - `MUTE_BLUEIRIS_LAST_EVENT`
- If the last event sensor provides a stored snapshot path, the notification includes the image automatically.
- If no stored snapshot path is available, the notification is still sent without an image.
- The mute helper and mute duration in the companion automations should match the values selected in the blueprint.
- The notification service is entered manually so the blueprint remains portable across different Home Assistant installs.

---

### Source

Blueprint file:

`Automations/blueiris-last-event-notifications.yaml`

GitHub source:

`https://github.com/kramttocs/ha-blueprints/blob/main/Automations/blueiris-last-event-notifications.yaml`

</details>
