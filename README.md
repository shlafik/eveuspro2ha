# Eveus Pro EVSE integration with Home Assistant

# Disclaimer
The code within this repository comes with no guarantee, the use of this code is your responsibility.

I take NO responsibility and/or liability for how you choose to use information available here. By using any of the files available in this repository, you understand that you are AGREEING TO USE AT YOUR OWN RISK.

# Sensor setup

1. Add sensor (/config/configuration.yaml)

Replace `{{ EVSE_HOST }}` with your IP, as well as `{{ EVSE_USER }}` and `{{ EVSE_PASSWORD }}`.

Note: attribute names are based on eveus pro v1.30. You may check full list of attributes with command:
```
curl -s -u {{ EVSE_USER }}:{{ EVSE_PASSWORD }} -X POST http://{{ EVSE_HOST }}/main
```
Add sensor to HA (edit /config/configuration.yaml):
```
sensor:
  - platform: rest
    name: EVSE Eveus
    resource: http://{{ EVSE_HOST }}/main
    username: {{ EVSE_USER }}
    password: {{ EVSE_PASSWORD }}
    method: POST
    json_attributes:
      - evseEnabled
      - state
      - currentSet
      - curDesign
      - typeEvse
      - typeRelay
      - aiModecurrent
      - aiStatus
      - aiVoltage
      - aiPatameter
      - ground
      - groundCtrl
      - timerType
      - limitsStatus
      - timeLimit
      - energyLimit
      - moneyLimit
      - tarif
      - startSchedule1
      - stopSchedule1
      - currentSchedule1
      - energySchedule1
      - tarifSchedule1
      - startSchedule2
      - stopSchedule2
      - currentSchedule2
      - energySchedule2
      - tarifSchedule2
      - sessionEnergy
      - sessionTime
      - totalEnergy
      - systemTime
      - timeZone
      - curMeas1
      - curMeas2
      - curMeas3
      - voltMeas1
      - voltMeas2
      - voltMeas3
      - temperature1
      - temperature2
      - leakValue
      - vBat
      - powerMeas
      - STA_IP_Addres
    value_template: "EVSE_Eveus"
  - platform: template
    sensors:
      evse_eveus_enabled:
        value_template: "{{ state_attr('sensor.evse_eveus', 'evseEnabled') }}"
        friendly_name: "EVSE Enabled"
      evse_eveus_state:
        value_template: >
          {% set mapper =  {
            0  : 'No data',
            1  : 'Ready',
            2  : 'Waiting',
            3  : 'Charging',
            7  : 'Current Leak',
            8  : 'No Ground',
            10 : 'Overheat Plug',
            11 : 'Overheat Relay',
            12 : 'OverCurrent',
            13 : 'OverVoltage',
            14 : 'UnderVoltage',
            15 : 'Limited By Time',
            16 : 'Limited By Energy',
            17 : 'Limited By Money',
            18 : 'Limited By Schedule1',
            19 : 'Limited By Schedule2',
            20 : 'Disabled By User',
            21 : 'Relay Stuck',
            22 : 'Limited By AI Mode'
          } %}
          {% set state_num = state_attr('sensor.evse_eveus', 'state') %}
          {{ mapper[state_num] if state_num in mapper else 'Unknown' }}
        friendly_name: "State"
      evse_eveus_currentset:
        value_template: "{{ state_attr('sensor.evse_eveus', 'currentSet') }}"
        friendly_name: "Current set"
        unit_of_measurement: "A"
      evse_eveus_curdesign:
        value_template: "{{ state_attr('sensor.evse_eveus', 'curDesign') }}"
        unit_of_measurement: "A"
        friendly_name: "Current designed"
      evse_eveus_voltmeas1:
        value_template: "{{ state_attr('sensor.evse_eveus', 'voltMeas1') | round(1) }}"
        unit_of_measurement: "V"
        friendly_name: "Voltage"
      evse_eveus_curmeas1:
        value_template: "{{ state_attr('sensor.evse_eveus', 'curMeas1')  | round(1) }}"
        unit_of_measurement: "A"
        friendly_name: "Current"
      evse_eveus_powermeas:
        value_template: "{{ state_attr('sensor.evse_eveus', 'powerMeas')  | round(1) }}"
        unit_of_measurement: "kW"
        friendly_name: "Power"
      evse_eveus_temperature1:
        value_template: "{{ state_attr('sensor.evse_eveus', 'temperature1') }}"
        unit_of_measurement: "°C"
        friendly_name: "Temp of box"
      evse_eveus_temperature2:
        value_template: "{{ state_attr('sensor.evse_eveus', 'temperature2') }}"
        unit_of_measurement: "°C"
        friendly_name: "Temp of plug"
      evse_eveus_ground:
        value_template: "{% if state_attr('sensor.evse_eveus', 'ground') == 1 %}Yes{% else %}No{% endif %}"
        friendly_name: "Ground"
      evse_eveus_groundctrl:
        value_template: "{% if state_attr('sensor.evse_eveus', 'groundCtrl') == 2 %}Yes{% else %}No{% endif %}"
        friendly_name: "Ground control"
      evse_eveus_aivoltage:
        value_template: "{{ state_attr('sensor.evse_eveus', 'aiVoltage') | round(1) }}"
        unit_of_measurement: "V"
        friendly_name: "Voltage adaptive min"
      evse_eveus_sessiontime:
        value_template: "{{ state_attr('sensor.evse_eveus', 'sessionTime') }}"
        unit_of_measurement: "s"
        friendly_name: "Session duration"
      evse_eveus_sessionenergy:
        value_template: "{{ state_attr('sensor.evse_eveus', 'sessionEnergy') | round(3) }}"
        unit_of_measurement: "kWh"
        friendly_name: "Session energy"
      evse_eveus_totalenergy:
        value_template: "{{ state_attr('sensor.evse_eveus', 'totalEnergy') | round(3) }}"
        unit_of_measurement: "kWh"
        friendly_name: "Total energy"
      evse_eveus_aistatus:
        value_template: "{{ state_attr('sensor.evse_eveus', 'aiStatus') }}"
        friendly_name: "Adaptive mode status"
      evse_eveus_vbat:
        value_template: "{{ state_attr('sensor.evse_eveus', 'vBat')  | round(2) }}"
        unit_of_measurement: "V"
      evse_eveus_systemtime:
        value_template: "{{ state_attr('sensor.evse_eveus', 'systemTime') | int | timestamp_custom('%d.%m.%Y %H:%M:%S', False) }}"
        friendly_name: "System time"
```

2. Validate config in HA
Configuration -> Server Controls
Press `CHECK CONFIGURATION` button in `Check Configuration` section

4. Restart HA server
Configuration -> Server Controls
Press `RESTART` in `Server managementn` section

5. Create dashboards/automation using new sensor entities

# Switch example
This example implements Eveus UI interface switch named "Остановить процес заряда"

add into /config/configuration.yaml
```
command_line:
  - switch:
      name: EVSE stop charging
      unique_id: evse_eveus_stop_charging
      command_on: "curl -s -u {{ EVSE_USER }}:{{ EVSE_PASSWORD }} -X POST -H 'Content-type: application/x-www-form-urlencoded' 'http://{{ EVSE_HOST }}/pageEvent' -d \"limitsStatus=$((512^$(curl -s -u {{ EVSE_USER }}:{{ EVSE_PASSWORD }} -X POST 'http://{{ EVSE_HOST }}/main' | jq '.limitsStatus')))\""
      command_off: "curl -s -u {{ EVSE_USER }}:{{ EVSE_PASSWORD }} -X POST -H 'Content-type: application/x-www-form-urlencoded' 'http://{{ EVSE_HOST }}/pageEvent' -d \"limitsStatus=$((512^$(curl -s -u {{ EVSE_USER }}:{{ EVSE_PASSWORD }} -X POST 'http://{{ EVSE_HOST }}/main' | jq '.limitsStatus')))\""
      command_state: "echo $((512&$(curl -s -u {{ EVSE_USER }}:{{ EVSE_PASSWORD }} -X POST 'http://{{ EVSE_HOST }}/main' | jq '.limitsStatus')))"
      value_template: '{{ value == "512" }}'
```

# Current regulator (+/- buttons)
add into /config/configuration.yaml
```
shell_command:
  evse_current_incr: "curl -s -u {{ EVSE_USER }}:{{ EVSE_PASSWORD }} -X POST -H 'Content-type: application/x-www-form-urlencoded' 'http://{{ EVSE_HOST }}/pageEvent' -d \"currentSet=$(($(curl -s -u {{ EVSE_USER }}:{{ EVSE_PASSWORD }} -X POST 'http://{{ EVSE_HOST }}/main' | jq '.currentSet')+1))\""
  evse_current_decr: "curl -s -u {{ EVSE_USER }}:{{ EVSE_PASSWORD }} -X POST -H 'Content-type: application/x-www-form-urlencoded' 'http://{{ EVSE_HOST }}/pageEvent' -d \"currentSet=$(($(curl -s -u {{ EVSE_USER }}:{{ EVSE_PASSWORD }} -X POST 'http://{{ EVSE_HOST }}/main' | jq '.currentSet')-1))\""
```
UI
```
type: vertical-stack
cards:
  - type: horizontal-stack
    cards:
      - type: button
        tap_action:
          action: call-service
          service: shell_command.evse_current_decr
        show_icon: true
        show_name: true
        name: EVSE current -
        icon: mdi:arrow-down-bold-box
      - type: entity
        entity: sensor.evse_eveus_currentset
        icon: mdi:lightning-bolt
      - type: button
        tap_action:
          action: call-service
          service: shell_command.evse_current_incr
        show_icon: true
        show_name: true
        name: EVSE current +
        icon: mdi:arrow-up-bold-box
  - type: horizontal-stack
    cards:
      - type: button
        tap_action:
          action: toggle
        entity: switch.evse_stop_charging
        icon: mdi:pause-octagon-outline
      - type: entity
        entity: sensor.evse_eveus_state
        icon: mdi:ev-station
```
![obraz](https://user-images.githubusercontent.com/5980725/167610181-d41f9cb4-77c5-4954-bc72-de6b5c7ac50c.png)



# Current regulator (slider)

add into /config/configuration.yaml
```
shell_command:
 evse_current_set: "curl -s -u {{ EVSE_USER }}:{{ EVSE_PASSWORD }} -X POST -H 'Content-type: application/x-www-form-urlencoded' 'http://{{ EVSE_HOST }}/pageEvent' -d \"currentSet={{ '%02d'|format(states('input_number.evse_eveus_current')|int) }}\""

input_number:
  evse_eveus_current:
    min: 6
    max: 16
    step: 1
```

add into /config/automations.yaml
```
- id: '1639106027277'
  alias: 'Set EVEUS EVSE Current'
  description: ''
  trigger:
  - platform: state
    entity_id: input_number.evse_eveus_current
  condition: []
  action:
    service: shell_command.evse_current_set
```

UI
```
type: entities
entities:
  - entity: input_number.evse_eveus_current
```
![obraz](https://user-images.githubusercontent.com/5980725/167610287-29698c61-8fc1-4a36-b012-8c39f6ff0467.png)


# Dashboard example
![obraz](https://user-images.githubusercontent.com/5980725/167611065-4f47ba0a-0176-4b1f-a20d-7caa61197586.png)
