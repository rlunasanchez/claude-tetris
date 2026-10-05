---
name: clima
description: Consulta el clima actual o el pronóstico de una ciudad usando wttr.in (sin API key, vía curl). Ciudad por defecto SIEMPRE Santiago de Chile, salvo que el usuario indique otra explícitamente. Úsala cuando el usuario pida "el clima", "el tiempo", "cómo está el clima", "pronóstico", o invoque /clima.
---

# Clima

Consulta el clima local usando el servicio público [wttr.in](https://wttr.in), que no requiere API key ni registro. Funciona con un simple `curl`.

## Uso

- **La ciudad por defecto es SIEMPRE Santiago de Chile.** Si el usuario invoca `/clima` sin argumentos, o pide "el clima"/"el tiempo" sin mencionar ninguna ciudad, usar Santiago de Chile — nunca asumir otra ubicación ni preguntar cuál.
- Solo usar otra ciudad si el usuario la indica explícitamente, ya sea como argumento (`/clima Buenos Aires`, `/clima Madrid`) o en el mensaje ("el clima en Lima").
- El usuario puede pedir distintos formatos: resumen rápido de una línea, reporte completo, o pronóstico de varios días.

## Cómo obtener los datos

Ejecutar con la herramienta Bash (o PowerShell si Bash no está disponible). Reemplazar `CIUDAD` por la ciudad solicitada; **por defecto siempre `Santiago,Chile`** (nunca dejar `CIUDAD` vacío ni usar solo `Santiago` sin el país, para evitar que wttr.in resuelva otro "Santiago" como el de Cuba o República Dominicana). Si la ciudad tiene espacios, codificarlos como `+`.

**Resumen rápido (una línea):**
```bash
curl -s "wttr.in/CIUDAD?format=3&lang=es"
```

**Reporte completo (ASCII, día actual):**
```bash
curl -s "wttr.in/CIUDAD?lang=es&M"
```

**Pronóstico extendido (varios días), forzando salida angosta para terminal:**
```bash
curl -s "wttr.in/CIUDAD?lang=es&M&n"
```

Notas:
- `&M` fuerza unidades métricas (°C, km/h).
- `&lang=es` pide la respuesta en español (no todos los textos se traducen, es normal).
- Si la ciudad tiene espacios, reemplazarlos por `+` en la URL (ej. `Buenos+Aires`).
- Si `curl` no está disponible, usar `Invoke-RestMethod` o `Invoke-WebRequest` en PowerShell como alternativa:
  ```powershell
  (Invoke-WebRequest -Uri "https://wttr.in/CIUDAD?format=3&lang=es").Content
  ```

## Comportamiento esperado

1. Si el usuario no especifica ciudad, usar SIEMPRE `Santiago,Chile` — es el comportamiento por defecto de esta skill, no una suposición a validar.
2. Elegir el formato según lo que pida el usuario: si pide algo rápido/breve, usar `format=3`; si no especifica, usar el reporte completo (ASCII); si pide pronóstico o "próximos días", usar el formato extendido.
3. Ejecutar el `curl` correspondiente y mostrar la salida tal cual (el ASCII art de wttr.in se ve bien en una respuesta de texto/código).
4. Si el comando falla (sin conexión, ciudad no encontrada), informar el error claramente y sugerir revisar el nombre de la ciudad o la conexión a internet.
