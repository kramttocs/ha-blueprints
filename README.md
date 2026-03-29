# Home Assistant Blueprints

A collection of Home Assistant blueprints by **kramttocs**.
These are designed, heavily reviewed, and tested by me. AI assisted in generating documentation and some logic.

---

## Available Blueprints

<details>
<summary><strong>UPS Monitor via NUT</strong></summary>

Monitor a **Network UPS Tools (NUT)** device

This is started from a blueprint by **Hacky1** (reached out to collaborate on the forum). I was getting enough flakiness in my sensors that probably aren't the norm for most so just modified it to fit my needs. Alerts for things like:

- UPS switching to **On Battery**
- **Low Battery**
- Battery dropping by configurable percentages
- **Self-test failures**
- **High UPS load**

It also supports **multiple notification devices** and **cross-platform priority notifications** for both Android and iOS (still testing this since I just have Android).

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

I am still fine tuning mine but would start with these:

| Setting | Recommended |
|---------|-------------|
| Status Debounce | 30 seconds |
| Ignore Unavailable | Enabled |
| Online Notifications | Disabled |
| Battery Interval | 10% |

---

### Credit

Original idea and starting point credit goes to **Hacky1**

</details>

<details>
<summary><strong>Blue Iris - Last Motion Event Notifications</strong></summary>

Send actionable notifications from Blue Iris last motion event sensors.

This blueprint is built around the **Last Event sensors** from my Blue Iris integration and is designed to make motion notifications more useful, camera-aware, and Alarmo-friendly.

It currently focuses on **motion-related last events only**.

---

### Install Blueprint

Click the button below to import the blueprint into Home Assistant.

[![Import Blueprint](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https://raw.githubusercontent.com/kramttocs/ha-blueprints/main/Automations/blueiris-last-motion-event-notifications.yaml)

---

### Features

#### Camera-Aware Notifications

- Supports multiple Blue Iris `*_last_event` sensors in a single automation
- Automatically derives the matching camera entity from the selected sensor
- Includes the camera name in the notification title
- Uses the latest Blue Iris snapshot image when available

#### Motion Event Filtering

Only notify for the motion event types you care about.

Supported values:

- `motion_person`
- `motion_vehicle`
- `motion_animal`
- `motion_multi`
- `motion`

#### Alarm-Aware Behavior

Notifications are only sent when the selected alarm entity is armed.

Supports camera-specific suppression for:

- `armed_home`
- `armed_away`
- `armed_night`
- `armed_vacation`

This allows you to ignore indoor cameras while home, but still receive notifications from them for other armed states.

#### Per-Motion-Event Camera Suppression

Supports suppressing specific cameras for specific motion event types:

- Motion Person
- Motion Vehicle
- Motion Animal
- Motion Multi
- Motion

This makes it possible to do things like:
- ignore animal detections on one camera
- still allow person detections on that same camera
- still allow animal detections on other cameras

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

This requires additional setup shown below, but the notification mute action can temporarily suppress future Blue Iris last motion event notifications.

---

### Requirements

- Home Assistant
- **Blue Iris integration**
- Blue Iris last event sensors with motion-based `event_type`
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

#### Cameras and Motion Events

- **Last Event Sensors**  
  Select one or more Blue Iris last event sensors.

- **Allowed Motion Event Types**  
  Choose which motion `event_type` values should trigger notifications.

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

#### Per-Motion-Event Camera Suppression

- **Ignore Cameras for Motion Person**
- **Ignore Cameras for Motion Vehicle**
- **Ignore Cameras for Motion Animal**
- **Ignore Cameras for Motion Multi**
- **Ignore Cameras for Motion**

These let you suppress specific cameras for specific motion event types.

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
      message: Last motion event notifications muted for 4 hours.
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
- This blueprint is currently intended for **motion-related last events**.
- Supported motion event types are:
  - `motion_person`
  - `motion_vehicle`
  - `motion_animal`
  - `motion_multi`
  - `motion`
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
