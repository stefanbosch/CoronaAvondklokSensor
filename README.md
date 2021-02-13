# CoronaAvondklokSensor
Templating Sensor Home Assistant for CoronaAvondklok

![Alt text](https://github.com/stefanbosch/CoronaAvondklokSensor/blob/main/screenshot.png)

Curfew lasts till 3rd of march 4:30 AM

**Add the following to your configuration.yaml:**
```yaml
- platform: template
  sensors:
    corona_avondklok_regulation:
      value_template: '2021-03-03 04:30:00'
    corona_avondklok:
      value_template: >
        {% set regulation = strptime(states('sensor.corona_avondklok_regulation'),'%Y-%m-%d %H:%M:%S') %}
        {% if (now().replace(tzinfo=None) < regulation) %}
            {% if now() >= now().replace(hour=21,minute=0,second=0,microsecond=0) or now() < now().replace(hour=4,minute=30,second=0,microsecond=0) %}
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
      attribute_templates:
        Avondklok tot: "{{ states('sensor.corona_avondklok_regulation') }}"
        Aantal dagen: >
            {{ '%.2f'%(((strptime(state_attr('sensor.corona_avondklok', 'Avondklok tot'),'%Y-%m-%d %H:%M:%S')) - now().replace(second=0,microsecond=0,tzinfo=None)).total_seconds() / 60 / 60 / 24) }}
        Volgende avondklok: >
            {% if (now().hour >= 21 ) %}
                {{ ((now().replace(hour=21,minute=0,second=0,microsecond=0) + timedelta(days=1)) | string)[:19] }}
            {% else %}  
                {{ ((now().replace(hour=21,minute=0,second=0,microsecond=0)) | string)[:19] }}
            {% endif %}
        Einde avondklok: >
            {% if (now().hour == 4 and now().minute >= 30 or now().hour > 4 ) %}
                {{ ((now().replace(hour=4,minute=30,second=0,microsecond=0) + timedelta(days=1)) | string)[:19] }}
            {% else %}
                {{ ((now().replace(hour=4,minute=30,second=0,microsecond=0)) | string)[:19] }}
            {% endif %}
        Looptijd: >
            {% if (states('sensor.corona_avondklok') == 'Actief' ) %}
                {{ (((strptime(state_attr('sensor.corona_avondklok', 'Einde avondklok'),'%Y-%m-%d %H:%M:%S')) - now().replace(second=0,microsecond=0,tzinfo=None)) | string)[:-3] }}
            {% elif (states('sensor.corona_avondklok') == 'Non-Actief' ) %}
                ∞
            {% else %}
                {{ (((strptime(state_attr('sensor.corona_avondklok', 'Volgende avondklok'),'%Y-%m-%d %H:%M:%S')) - now().replace(second=0,microsecond=0,tzinfo=None)) | string)[:-3] }}
            {% endif %}
```











# Templating:

```yaml
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

```yaml
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
