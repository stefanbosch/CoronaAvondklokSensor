# CoronaAvondklokSensor
Templating Sensor Home Assistant for CoronaAvondklok


**Add the following to your configuration.yaml:**
```
sensor:
  - platform: template
    sensors:
      corona_avondklok:
        value_template: >
          {% set regulation = strptime('2021-02-10 04:30:00','%Y-%m-%d %H:%M:%S') %}
          {% if (now().replace(tzinfo=None) < regulation) %}
              {% if (now().hour+(now().minute/60) >=21 or now().hour+(now().minute/60) <= 4.5) %}
                  Actief
              {% else %}
                  Over {{ (timedelta( hours=21-(now().hour+(now().minute/60)) )|string)[:-3] }} uur
              {% endif %}
          {% else %}
              Non-Actief
          {% endif %}
        friendly_name: 'Corona Avondklok'
        icon_template: >
          {% if is_state('sensor.corona_avondklok','Actief') %}
              mdi:clock-time-nine-outline
          {% else %}
              mdi:alarm-off
          {% endif %}
```













```
{% if (now().replace(tzinfo=None) < strptime('2021-02-10 04:30:00','%Y-%m-%d %H:%M:%S')) %}
  {% if (now().hour+(now().minute/60) >=21 or now().hour+(now().minute/60) <= 4.5) %}
    Actief
  {% else %}
    Over {{ '%.2f'%(21 - (now().hour+(now().minute/60))) }} uur
  {% endif %}
{% else %}
  Non-Actief
{% endif %}
```

```
{% set regulation = strptime('2021-02-10 04:30:00','%Y-%m-%d %H:%M:%S') %}
{% if (now().replace(tzinfo=None) < regulation) %}
  {% if (now().hour+(now().minute/60) >=21 or now().hour+(now().minute/60) <= 4.5) %}
    Actief
  {% else %}
    Over {{ (timedelta( hours=21-(now().hour+(now().minute/60)) )|string)[:-3] }} uur
  {% endif %}
{% else %}
  Non-Actief
{% endif %}
```
