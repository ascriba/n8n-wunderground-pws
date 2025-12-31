# Weather Underground PWS → PostgreSQL (n8n)

This n8n workflow fetches **current observations** from a
**Weather Underground Personal Weather Station (PWS)** and stores them in a PostgreSQL database.

---

## Features

- Fetches real-time weather data from Weather Underground
- Fully configurable (station ID, units, precision)
- Stores structured metrics in PostgreSQL
- Automatically creates the database table if it does not exist
- Ready-to-use n8n template (no credentials included)

---

## Included Metrics

- Temperature
- Humidity
- Wind speed & gusts
- Wind direction (degrees)
- Pressure
- Solar radiation
- UV index
- Precipitation (rate & total)
- Observation timestamp

---

## Requirements

- n8n (v1.0+ recommended)
- PostgreSQL database
- Weather Underground API key (PWS access)

---

## API Credentials Setup

1. Create a Weather Underground API key
   👉 https://www.wunderground.com/member/api-keys

2. In n8n, create a new credential:
   - **Type:** HTTP Query Auth
   - **Parameter Name:** `apiKey`
   - **Value:** *your Weather Underground API key*

3. Assign this credential to the **HTTP Request** node.

---

## Workflow Configuration

Edit the **Configuration** node:

```json
{
  "userStationId": "YOUR_STATION_ID",
  "userUnits": "m",
  "numericPrecision": "decimal"
}
```
---



| Parameter          | Description                                 |
| ------------------ | ------------------------------------------- |
| `userStationId`    | Your PWS ID (e.g. `IXXXXXXXX`)              |
| `userUnits`        | `m` = Metric, `e` = English, `h` = Hybrid   |
| `numericPrecision` | Optional – use `decimal` for decimal output |

---

## Database Setup

The workflow automatically creates the table:

```sql
CREATE TABLE IF NOT EXISTS Wetterdaten (
  id SERIAL PRIMARY KEY,
  obsTimeLocal TIMESTAMP,
  solarRadiation NUMERIC(5,2),
  uv NUMERIC(5,2),
  winddir NUMERIC(5,2),
  humidity NUMERIC(5,2),
  temperature NUMERIC(5,2),
  heatIndex NUMERIC(5,2),
  dewpt NUMERIC(5,2),
  windChill NUMERIC(5,2),
  windSpeed NUMERIC(5,2),
  windGust NUMERIC(5,2),
  pressure NUMERIC(7,2),
  precipRate NUMERIC(5,2),
  precipTotal NUMERIC(5,2),
  created_at TIMESTAMP DEFAULT NOW()
);
```

## API Rate Limits (Important)

Weather Underground enforces strict API limits depending on your plan.

### Typical limits:

- Free / PWS plans: ~500 calls per day ?

- Paid plans: higher limits

### Recommendation

- Use a 30-minute interval or slower

- Avoid polling every few minutes

- Consider caching or aggregation if needed

⚠️ Exceeding the limit may result in:

- HTTP 429 errors

- Temporary API key suspension

Always check:
👉 https://www.wunderground.com/weather/api/

### Privacy & Security

- No credentials included in this repository

- No personal data stored by default

- Station ID must be provided by the user

- Workflow is safe to share publicly

## License

MIT License – feel free to use, modify, and share.
