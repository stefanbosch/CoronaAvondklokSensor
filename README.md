# CoronaAvondklokSensor
Templating Sensor Home Assistant for CoronaAvondklok
```
{% if (now().replace(tzinfo=None) < strptime('2021-02-10 04:30:00','%Y-%m-%d %H:%M:%S')) %}
  {% if (now().hour+(now().minute/60) >=21 or now().hour+(now().minute/60) <= 4.5) %}
    Actief
  {% else %}
    over {{ '%.2f'%(21 - (now().hour+(now().minute/60))) }} uur
  {% endif %}
{% else %}
  Non-Actief
{% endif %}
```
