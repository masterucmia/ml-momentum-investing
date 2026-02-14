# Documentación Técnica – `format_alert_humana`

## 1. Descripción General

La función `format_alert_humana(r)` genera un mensaje formateado en HTML usando la etiqueta `<pre>` para representar alertas de trading de forma estructurada y legible.

Soporta dos tipos de eventos:

- BUY → Nueva entrada al mercado
- SELL → Cierre de posición (con ganancia, pérdida o resultado neutro)

El mensaje está optimizado para enviarse a sistemas de notificaciones que soporten HTML preformateado.

---

## 2. Firma de la Función

```python
def format_alert_humana(r):
```

---

## 3. Parámetro de Entrada

La función recibe un objeto `r` que debe contener los siguientes atributos:

| Atributo          | Tipo        | Descripción                   |
| ----------------- | ----------- | ----------------------------- |
| `timestamp_alert` | datetime    | Fecha y hora del evento       |
| `hold_minutes`    | int \| None | Duración del trade en minutos |
| `signal`          | str         | `"BUY"` o `"SELL"`            |
| `symbol`          | str         | Activo operado                |
| `tipo_estrategia` | str         | Nombre de la estrategia       |
| `entry_price`     | float       | Precio de entrada             |
| `price`           | float       | Precio de salida (solo SELL)  |
| `pnl_pct`         | float       | Resultado porcentual          |
| `exit_reason`     | str         | Motivo de salida (solo SELL)  |
| `trade_id`        | str         | Identificador único           |

---

## 4. Formateo de Fechas

El timestamp principal se formatea como:

```python
"%Y-%m-%d %H:%M"
```

Si `hold_minutes` no es `None`, la hora de entrada se calcula:

```python
ts_entry = ts_exit - timedelta(minutes=r.hold_minutes)
```

Si `hold_minutes` es `None`, se muestra:

```
Hora entrada: N/A
```

---

## 5. Separador Visual

Se utiliza la siguiente línea para separar secciones:

```
━━━━━━━━━━━━━━
```

---

## 6. Caso BUY – Nueva Entrada

Condición:

```python
if r.signal == "BUY":
```

### Información mostrada

- Asset
- Tipo de estrategia
- Precio de entrada
- Hora de entrada
- Trade ID

### Ejemplo

```
NUEVA ENTRADA
━━━━━━━━━━━━━━
Asset: BTCUSDT
Tipo: Momentum

Precio entrada: 43250.5
Hora entrada: 2026-02-14 10:30
━━━━━━━━━━━━━━
Trade ID:
TRD-00123
```

---

## 7. Caso SELL – Cierre de Posición

Se ejecuta cuando la señal no es `"BUY"`.

### 7.1 Cálculo de PnL

```python
pnl = r.pnl_pct
```

### 7.2 Determinación del Título

| Condición  | Título              |
| ---------- | ------------------- |
| `pnl > 0`  | SALIDA CON GANANCIA |
| `pnl < 0`  | SALIDA CON PÉRDIDA  |
| `pnl == 0` | SALIDA NEUTRA       |

---

### 7.3 Cálculo de Duración

```python
horas = r.hold_minutes // 60
minutos = r.hold_minutes % 60
```

Se muestra como:

```
Hold: 3h 25m
```

---

### 7.4 Información mostrada en SELL

- Asset
- Tipo de estrategia
- Motivo de salida
- Precio entrada → Precio salida
- Hora entrada
- Hora salida
- PnL formateado con signo (+/-)
- Duración
- Trade ID

### Ejemplo

```
SALIDA CON GANANCIA
━━━━━━━━━━━━━━
Asset: BTCUSDT
Tipo: Momentum
Motivo: Take Profit

Precio: 43250.5 → 43800.0
Hora entrada: 2026-02-14 07:05
Hora salida: 2026-02-14 10:30
━━━━━━━━━━━━━━
PnL: +1.27%
Hold: 3h 25m
━━━━━━━━━━━━━━
Trade ID:
TRD-00123
```

---

## 8. Características del Formato

- Usa bloque `<pre>` para mantener alineación
- Separa visualmente las secciones
- Formatea el PnL con signo explícito
- Mantiene compatibilidad con sistemas que soporten HTML preformateado

---

## 9. Consideraciones Técnicas

- Validar `hold_minutes` en SELL para evitar errores si es `None`
- Asegurar que todos los atributos del objeto `r` estén presentes
- La función asume que los datos vienen correctamente tipados
