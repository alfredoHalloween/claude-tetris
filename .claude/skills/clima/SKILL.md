---
name: clima
description: Consulta la temperatura y el estado del clima actual de una ciudad (por defecto Coacalco, Estado de México) usando wttr.in desde la terminal. Úsalo cuando el usuario pida el clima, la temperatura o "/clima", incluso dentro de un /loop.
---

# Clima

Obtiene el clima actual con datos en vivo. La búsqueda web (`WebSearch`) NO sirve para esto: devuelve páginas con fechas viejas. Usa siempre `curl` contra wttr.in.

## Ciudad

- Por defecto SIEMPRE es **Coacalco, Estado de México** (`Coacalco,Mexico` en wttr.in, que resuelve a Coacalco, región México, lat 19.635, lon -99.101). No preguntes la ciudad: úsala directamente.
- Solo cambia de ciudad si el usuario nombra otra de forma explícita en esa petición (reemplaza espacios por `+`, ej. `Ciudad+de+Mexico`). La siguiente petición vuelve a Coacalco.

## Pasos

1. Ejecuta con Bash:

   ```bash
   curl -s --max-time 15 "https://wttr.in/Coacalco,Mexico?format=j1&lang=es"
   ```

2. Del JSON, toma `current_condition[0]`:
   - `temp_C`: temperatura en °C
   - `FeelsLikeC`: sensación térmica
   - `humidity`: humedad en %
   - `lang_es[0].value` (o `weatherDesc[0].value`): estado del clima
   - `windspeedKmph`: viento en km/h
   - `observation_time`: hora de la observación, en UTC (el JSON no trae hora local)

3. Responde en español, corto, en este formato:

   **Coacalco: 22 °C, parcialmente nublado.** Sensación 19 °C · humedad 39 % · viento 10 km/h · observado 1:17 pm.

## Notas

- `observation_time` viene en UTC; la hora local de México es UTC-6. Conviértela a hora local o indica que es UTC.
- wttr.in actualiza cada ~30–60 min, así que lecturas repetidas pueden ser idénticas. Dentro de un loop, informa solo si cambió algo.
- Si `curl` falla o devuelve algo que no es JSON, dilo claramente; no inventes datos ni uses promedios históricos como si fueran la lectura actual.
- Solo lectura: no modifiques archivos del proyecto.
