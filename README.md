# Eveus Pro EVSE integration with Home Assistant

# Disclaimer
The code within this repository comes with no guarantee, the use of this code is your responsibility.

I take NO responsibility and/or liability for how you choose to use any of the source code available here. By using any of the files available in this repository, you understand that you are AGREEING TO USE AT YOUR OWN RISK.

# Setup steps

1. Add sensor (/config/configuration.yaml)

Replace `{{ EVEUS_IP }}` with your IP, as well as `{{ EVEUS_USER }}` and `{{ EVEUS_PASSWORD }}`.
Note: ttribute names are based on eveus pro v1.3. You may check full list of attributes with command:
```
curl -u {{ EVEUS_USER }}:{{ EVEUS_PASSWORD }} -X POST http://{{ EVEUS_IP }}/main
```
Add sensor to HA (edit /config/configuration.yaml):
```
sensor:
  - platform: rest
    name: EVSE ES
    resource: http://{{ EVEUS_IP }}/main
    username: {{ EVEUS_USER }}
    password: {{ EVEUS_PASSWORD }}
    authentication: basic
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
    value_template: "EVSE_ES"
  - platform: template
    sensors:
      evse_es_enabled:
        value_template: "{{ state_attr('sensor.evse_es', 'evseEnabled') }}"
        friendly_name: "EVSE Enabled"
      evse_es_state:
        value_template: "{{ state_attr('sensor.evse_es', 'state') }}"
        friendly_name: "State"
      evse_es_currentset:
        value_template: "{{ state_attr('sensor.evse_es', 'currentSet') }}"
        friendly_name: "Current set"
        unit_of_measurement: "A"
      evse_es_curdesign:
        value_template: "{{ state_attr('sensor.evse_es', 'curDesign') }}"
        unit_of_measurement: "A"
        friendly_name: "Current designed"
      evse_es_voltmeas1:
        value_template: "{{ state_attr('sensor.evse_es', 'voltMeas1') | round(1) }}"
        unit_of_measurement: "V"
        friendly_name: "Voltage"
      evse_es_curmeas1:
        value_template: "{{ state_attr('sensor.evse_es', 'curMeas1')  | round(1) }}"
        unit_of_measurement: "A"
        friendly_name: "Current"
      evse_es_powermeas:
        value_template: "{{ state_attr('sensor.evse_es', 'powerMeas')  | round(1) }}"
        unit_of_measurement: "kW"
        friendly_name: "Power"
      evse_es_temperature1:
        value_template: "{{ state_attr('sensor.evse_es', 'temperature1') }}"
        unit_of_measurement: "°C"
        friendly_name: "Temp of box"
      evse_es_temperature2:
        value_template: "{{ state_attr('sensor.evse_es', 'temperature2') }}"
        unit_of_measurement: "°C"
        friendly_name: "Temp of plug"
      evse_es_ground:
        value_template: "{{ state_attr('sensor.evse_es', 'ground') }}"
        friendly_name: "Ground"
      evse_es_groundctrl:
        value_template: "{{ state_attr('sensor.evse_es', 'groundCtrl') }}"
        friendly_name: "Ground control"
      evse_es_aivoltage:
        value_template: "{{ state_attr('sensor.evse_es', 'aiVoltage') | round(1) }}"
        unit_of_measurement: "V"
        friendly_name: "Voltage adaptive min"
      evse_es_sessiontime:
        value_template: "{{ state_attr('sensor.evse_es', 'sessionTime') }}"
        unit_of_measurement: "s"
        friendly_name: "Session duration"
      evse_es_sessionenergy:
        value_template: "{{ state_attr('sensor.evse_es', 'sessionEnergy') | round(3) }}"
        unit_of_measurement: "kWh"
        friendly_name: "Session energy"
      evse_es_totalenergy:
        value_template: "{{ state_attr('sensor.evse_es', 'totalEnergy') | round(3) }}"
        unit_of_measurement: "kWh"
        friendly_name: "Total energy"
      evse_es_vbat:
        value_template: "{{ state_attr('sensor.evse_es', 'vBat')  | round(2) }}"
        unit_of_measurement: "V"
      evse_es_systemtime:
        value_template: "{{ state_attr('sensor.evse_es', 'systemTime') | int | timestamp_custom('%d.%m.%Y %H:%M:%S', False) }}"
        friendly_name: "System time"
```

2. Validate config in HA
Configuration -> Server Controls
Press `CHECK CONFIGURATION` button in `Check Configuration` section

4. Restart HA server
Configuration -> Server Controls
Press `RESTART` in `Server managementn` section

5. Create dashboards/automation using new sensor entities
![image](https://user-images.githubusercontent.com/5980725/147565859-ef10c469-1bee-4e78-b85f-e080b9eac392.png)
