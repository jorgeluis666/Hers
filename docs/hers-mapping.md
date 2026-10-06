# Mapeo Hers Lima Retail 2026

## Objetivo

Dashboard estatico de Hers con lectura de gasto publicitario en Google Ads: marca, visual y modelo de datos propios.

## Identidad

- Marca principal: Hers
- Cuenta de Google Ads: la de Hers, con todas sus campanas. Hoy tiene una sola,
  `CE | Search | Canastas Navideñas | Lima` (grupos "AG1 | Canastas Navideñas Empresariales" y
  "AG2 | Vales de Pavo"), activa desde el 7 de setiembre de 2026 con S/ 20 diarios.
- Repo: `jorgeluis666/Hers`. Dominio: `hers.limaretail.com` (GitHub Pages con Actions).
- Acceso: clave en el secret `HERS_PAGE_PASSWORD` (el build cifra el tablero; ver README)
- Titulo: `Hers | Dashboard Lima Retail 2026`
- Paleta: vino Hers `#8b1d41` (isotipo provisional con la inicial); el acceso usa el azul de LR Suite
- Enfoque: gasto publicitario en Google Ads (red de busqueda) para generar leads, segun el objetivo del contrato

## Carpetas de Drive

| Carpeta | ID | Contenido | Compartida por enlace |
| --- | --- | --- | --- |
| Google Ads Hers Campañas | `1sNZ2FuYWntAn9ARHiY-TLxcE9SqMJbLR` | `Hers <mes> <ano>`: informe de campana | Si |
| Google Ads Hers Keywords | `1Fc1ahioYK0F41NjJ9hWn-RSANUNbNKM3` | `Hers KW <mes> <ano>`: informe de palabras clave de busqueda | Si |
| Términos de Búsqueda Hers | `1u31D9gUzw_SKVhGFzBSb1TjJoZAx7zJh` | No la usa el tablero | No (401) |
| Google Ads Hers Segmentacion | `160sYt96XtzcbmrXRruju_EacBkM2-M2x` | No la usa el tablero | No (401) |

Las cuatro estan en `02_Clientes de la agencia/Hers` (`1etL5vjvK2RIT2wxEeBkk4PWDUQJg4uXW`).

## Campos reflejados

| Fuente (informe de campana) | Campo JSON | Uso |
| --- | --- | --- |
| `Campaña` | `campaign` | Tabla y eje del grafico |
| `Costo` | `cost` | KPI, grafico y tabla |
| `% Δ` despues de Costo | `costDelta` | Tabla |
| `CTR` | `ctr` | KPI, grafico y tabla |
| `% Δ` despues de CTR | `ctrDelta` | Tabla |
| `Clics` | `clicks` | KPI, grafico y tabla |
| `% Δ` despues de Clics | `clicksDelta` | Tabla |
| `Conversiones` | `conversions` | KPI, grafico y tabla |
| `% Δ` despues de Conversiones | `conversionsDelta` | Tabla |
| `Costo/conv.` | `costPerConversion` | KPI, grafico y tabla |
| `% Δ` despues de Costo/conv. | `costPerConversionDelta` | Tabla |
| Rango de fechas del informe | `period` (por mes) | Fecha de corte de Proyecciones |
| `Presupuesto` de campanas habilitadas | `dailyBudget` (por mes) | Presupuesto de Proyecciones |
| `Estado de la campaña` | `status` (por campana) | Proyeccion por campana |
| `Presupuesto` (tipo diario) | `dailyBudget` (por campana) | Proyeccion por campana |

Los `% Δ` no vienen en el informe: se calculan contra el mes anterior.

## Palabras clave (informe de palabras clave de busqueda)

| Columna del informe | Campo JSON | Uso |
| --- | --- | --- |
| `Palabra clave` | `keyword` | Fila del cuadro de la campana |
| `Tipo de concordancia` | `matchType` | Debajo de la palabra clave; junto con ella identifica la fila mes a mes |
| `Campaña` | `campaign` | Un cuadro por campana; si el informe no la trae, la campana del mes en Gasto Publicitario |
| `Grupo de anuncios` | `adGroup` | Una palabra clave en varios grupos se suma en una fila |
| `Estado de palabras clave` | `state` | Guardado (Habilitado / Detenido) |
| `Estado` / `Motivos del estado` | `status` / `reasons` | Columna Estado; "campaña detenida" en todas = campana detenida |
| `Impr.` / `Clics` / `Costo` / `Conversiones` | `impressions` / `clicks` / `cost` / `conversions` | Base de todos los indicadores |
| `% impr. parte sup. búsqueda` | `topShare` (texto, p. ej. `< 10%`) | Columna Perdidas x ranking (los informes de Hers no la traen) |
| `% impr. perdidas de la Búsqueda (ranking)` | `lostRank` (texto) | Columna Perdidas x ranking (los informes de Hers no la traen) |

CTR, CPC, tasa y costo por conversion no se guardan: se calculan de las sumas. Las filas `Total: ...` se ignoran.
Las palabras clave quitadas de la cuenta no salen en el informe, asi que en setiembre de 2026 las filas suman
S/ 436.75 contra S/ 461.27 de la cuenta.

## Archivos clave

- `js/objectives.js`: logica de gasto publicitario, KPIs, grafico y tabla de resultados.
- `js/projections.js`: proyeccion al cierre y simulador de objetivo; lee `HersDashboard.snapshot()`.
- `js/keywords.js`: analisis de palabras clave y boton Actualizar (lee las hojas de Drive desde el navegador).
- `data/drive-config.json`: IDs y nombres de las dos carpetas de Drive y `keywords.excludeCampaigns` (vacio).
- `data/hers-lima-retail-2026.json`: fuente normalizada (`scripts/sync-drive.py`).
- `data/hers-palabras-clave-2026.json`: informe de palabras clave normalizado (`scripts/sync-keywords.py`).
- `data/hers-usuarios-2026.json`: directorio de Usuarios y Claves, cifrado (`scripts/usuarios-cifrado.js`).
- `scripts/import-hers-data.py`: importador CSV/XLSX.
- `scripts/build.js`: build estatico con la data de Hers incrustada y cifrada con `HERS_PAGE_PASSWORD`.
