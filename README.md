# Hers | Dashboard Lima Retail 2026

Dashboard de gasto publicitario en Google Ads de Hers. La agencia gestiona campanas de la red de busqueda para
generar leads. El tablero muestra **todas las campanas de la cuenta de Google Ads de Hers**. Hoy la cuenta tiene una
sola: `CE | Search | Canastas Navideñas | Lima` (canastas navideñas y vales de pavo para empresas, S/ 20 diarios,
activa desde el 7 de setiembre de 2026).

Es una copia adaptada del tablero de SUMI (`jorgeluis666/SUMI`), que a su vez sale del de Aquarius, con los mismos
modulos: Gasto Publicitario, Proyecciones, Palabras Clave y Usuarios y Claves.

Version actual: `v1.5.1`.

## Acceso

- URL publica: **https://hers.limaretail.com** (con clave, ver "Publicacion en hers.limaretail.com").
- La clave no esta en el codigo: es el secret `HERS_PAGE_PASSWORD` de GitHub.
- Entrada local: `index.html` (sin clave)
- Build publicado: `dist/index.html`

## Versionado

`vMAJOR.MINOR.PATCH`: `MAJOR` para cambios incompatibles, `MINOR` para modulos o funciones nuevas y `PATCH`
para correcciones. La version va en `package.json`, en los `?v=` de `index.html` y en la etiqueta de la barra
lateral.
`v1.6.0` cierra las carpetas de Drive: el barrido diario las lee por el Apps Script de Hers con una clave
privada (ver "Carpetas privadas") y el boton Actualizar de Palabras Clave pide ese mismo barrido.
`v1.5.1` alinea el formato con Royal Baby: `S/. ` en los montos, `—` para el dato vacio, Septiembre/Sep, tildes,
pestanas de mes y tablas con su estilo, y los bordes y hovers azules que quedaban pasan al bordo de la marca.

## Modulo principal

- Titulo: `Gasto publicitario · Google Ads`
- Subtitulo: `Leads en Google Ads` (el objetivo del contrato: leads con campanas de la red de busqueda).
- La sincronizacion (boton Sincronizar, ultimo y proximo barrido, carpetas de Drive) esta en la pildora de la barra
  superior derecha (ver Sincronizacion > Manual, desde el tablero).
- Logica en `js/objectives.js`. Sale del informe de campana de Google Ads (un total por mes,
  `data/hers-lima-retail-2026.json`) y del informe de palabras clave (`data/hers-palabras-clave-2026.json`, tal
  como lo publica el build).
- **Pestanas de mes**: Enero a Diciembre y **Anual**. Un mes sin informe de campana queda apagado y el punto
  marca el ultimo mes con datos. La eleccion se guarda en `hers_selected_month` (`anual` incluido). Si falta el
  informe de un mes entre el primero y el ultimo, un aviso lo dice.
- **Periodo** (`Periodo: 7-30 Setiembre 2026`, o `1-4 Octubre 2026 (mes en curso)`) con 8 KPIs: inversion,
  impresiones, clics, CTR, CPC, conversiones, costo por conversion y tasa de conversion (conversiones / clics).
  Los cocientes se recalculan sobre las sumas. El rango sale de `period` del informe. Solo el primer mes con
  datos empieza en su primer dia real (setiembre 2026, el 7, cuando arranco la campana); los siguientes
  empiezan el dia 1 aunque la campana no haya gastado ese dia.
  - Mes cerrado: contra el mes anterior completo. Mes en curso: contra el mes anterior **prorrateado a los
    mismos dias**, porque el informe trae un total por mes; CTR, CPC, costo y tasa no cambian al prorratear.
    Si el mes anterior no cubre el mes entero, la bajada dice sobre cuantos dias se prorratea ("vs Setiembre
    prorrateado a 4 dias, de 24 con datos": setiembre va del 7 al 30). Cuando llegue el informe por dia y
    campana (`months[].detail`) y cubra esos dias del mes anterior, inversion, clics y conversiones se
    comparan con el **mismo tramo real** ("1-4 Setiembre") y las impresiones (que ese informe no trae) siguen
    prorrateadas; un cociente sale del tramo si sus dos sumas son reales (CPC, costo y tasa) y si no, es el del
    mes anterior completo (CTR). Cada KPI dice contra que se compara y la bajada lo resume; cada campana usa su
    propio tramo. Solo el mes calendario anterior: si falta (setiembre, el primer mes), "Sin mes anterior para
    comparar" y no hay columnas de comparacion.
  - Flecha y color segun si subir es bueno: inversion neutra; CPC y costo por conversion, bajar es bueno.
  - Anual: acumulado de los meses con informe; los KPIs sumables muestran el promedio mensual de los meses
    cerrados en lugar de la variacion.
  - Los `*Delta` del JSON no se usan: comparan un mes en curso de pocos dias contra un mes completo.
- Debajo del periodo queda el grafico de la cuenta. En Anual queda en la vista Mes.
- **Distribucion de inversion por campana**: estado, presupuesto diario, inversion y su %, impresiones, clics,
  CTR, CPC, conversiones, costo por conversion y tasa de conversion, con el total del periodo. El nombre tecnico
  de la campana va en chico solo si difiere del corto. En Anual las campanas se suman por nombre y el estado y el
  presupuesto son los del ultimo mes.
- **Un cuadro por campana** (hoy una sola, `CE | Search | Canastas Navideñas | Lima`; la estructura aguanta
  mas). Es de temporada: cuando termine, su cuadro sigue en los meses en que corrio y no sale en los meses sin
  actividad.
  - KPIs con variacion: inversion, clics, CTR, CPC, conversiones, costo por conversion y presupuesto diario.
    El subtitulo dice contra que se compara, "Nueva: no corria en ..." o, en Anual, en que meses corrio.
  - Grafico **por mes** del ano, con hueco donde la campana no corrio. Indicadores: inversion y conversiones
    (por defecto), clics, impresiones y costo por conversion (inversion / conversiones del mes, el mismo del
    KPI; un mes sin conversiones queda como hueco). Cada punto lleva su valor (arriba en un pico, abajo en un
    valle; sin etiqueta en los ceros y, si dos chocan, una queda solo en el tooltip); los soles van sin
    decimales salvo montos menores a S/ 10. Con varios indicadores, cada uno en su panel con su escala sobre
    el mismo eje, o con el boton `Un solo cuadro` todos juntos, un color por indicador y cada uno con su escala
    (sin eje: el valor va en el punto).
  - Por dia no se ofrece (no hay serie diaria por campana): queda listo para cuando llegue un informe por dia
    y campana en `months[].detail` (sin impresiones; costo por conversion de cada dia, hueco si no hubo
    conversiones). No hay acumulado del mes: siempre sube y exagera el crecimiento (una vista guardada en
    acumulado vuelve a Por dia).
  - **Palabras clave de la campana** del mes: conversiones, conversiones del mes anterior y variacion ("Nuevo"
    si no estaba), costo por conversion, inversion y su % de la campana, impresiones, clics y CPC. Una palabra
    clave es su texto mas su concordancia, como en el modulo Palabras Clave. Solo se compara con el mes anterior
    si los dos informes son de mes completo o cubren los mismos dias; si no, las columnas de comparacion se
    ocultan. Si las palabras clave suman menos que la campana (palabras quitadas o con pocas impresiones), una
    linea lo dice (setiembre 2026: S/ 436.76 de S/ 461.27; no es un error).
  - Se minimiza con la flecha. Indicadores, vista, `Un solo cuadro` y minimizado se recuerdan por campana
    (`hers_gasto_campaign_charts`).
- **Ranking de palabras clave del periodo**: por conversiones dentro de cada campana, con la lider marcada
  con una estrella, conversiones del mes anterior y variacion. Cabecera fija, Campana y Palabra clave
  ancladas, filtro por campana, Compacto (`hers_gasto_kw_compact`) y Pantalla completa (las pestanas
  de mes entran al panel; `Esc` sale). "Ver analisis completo" abre el modulo Palabras Clave, que sigue
  siendo el analisis a fondo.
- Solo aparece lo que tuvo actividad: campanas y palabras clave sin inversion, impresiones ni clics en el
  periodo no salen en ninguna tabla, cuadro ni grafico (`isActive`).
- Las palabras clave respetan `keywords.excludeCampaigns` de `data/drive-config.json`.
- Grafico de la cuenta con tres detalles, que se eligen con los botones del panel y
  quedan guardados en `localStorage`:
  - **Dia** (por defecto): un punto por dia del mes elegido.
  - **Semana**: bloques de siete dias contados desde el 1 del mes (1-7, 8-14,
    ...). Solo el ultimo bloque puede quedar corto y el tooltip avisa cuantos
    dias trae.
  - **Mes**: un punto por mes con todo lo que va del ano. El mes elegido se
    marca con un punto mas grande y un clic en cualquier punto cambia la pestana
    a ese mes.
- Dia y Semana necesitan la serie diaria del mes. Si el mes no la tiene, esos dos
  botones quedan apagados y el grafico muestra la vista Mes sin perder la
  eleccion anterior. Hoy ningun mes de Hers tiene serie diaria: se puede cargar
  con `scripts/import-hers-data.py` (ver "Importar archivos sueltos").
- Las lineas de inversion y resultados salen punteadas y marcadas `(est.)`
  cuando el mes solo tiene impresiones diarias: son el total del mes repartido
  entre los dias segun las impresiones, no cifras diarias reales.

## Proyecciones

El modulo Proyecciones (pestana del panel lateral) proyecta el cierre del mes con los datos reales de
Gasto Publicitario. No lee el JSON ni Drive por su cuenta: consume el snapshot de solo lectura
`window.HersDashboard.snapshot()`:

```js
{ cutoff: '2026-10-04', source: 'Hers octubre 2026', year: 2026, lastSync: '...',
  months: [{ id, name, label, cost, clicks, conversions, impressions, dailyBudget, budgetTotal, period, campaigns }] }
```

- **Fecha de corte**: fin del rango del informe de Google Ads (`period.end`, la linea
  "1 de octubre de 2026 - 4 de octubre de 2026" del informe), nunca posterior a hoy en Lima. El mes proyectado
  es el del corte; si no tiene inversion, el ultimo mes con datos.
- **Proyeccion**: ritmo diario = acumulado / dias con datos; cierre = ritmo x dias del mes. Si el informe
  ya cubre el mes completo, la proyeccion es igual al acumulado y el simulador se desactiva.
- **Indicadores**: Inversion (referencia = presupuesto), Clics y Conversiones (sin referencia).
- **Presupuesto**: suma de la columna Presupuesto (diario) de las campanas *habilitadas* del informe, por los
  dias del mes. Un presupuesto compartido se cuenta una vez. Es el presupuesto vigente al exportar el informe,
  no el historico: en meses pasados es solo una referencia.
- Cada `applyData` emite `hers:data-ready` y el modulo se recalcula solo; si se abre antes de que
  carguen los datos muestra "Esperando los datos...".
- Setiembre de 2026 arranca el dia 7 (inicio de la campana): su informe cubre del 7 al 30.

### Simulador de objetivo

El ultimo punto de la linea proyectada es un nodo arrastrable: al moverlo se define el cierre deseado.

- El estado es un solo numero, `factor = cierre objetivo / cierre proyectado`, comun a los tres
  indicadores: el escenario conserva el CPC, el costo por conversion y la tasa de conversion del mes, asi
  que mover uno mueve los otros dos en la misma proporcion y cambiar de indicador conserva el escenario.
- El cierre objetivo nunca queda por debajo de lo ya realizado (`factor >= dias con datos / dias del mes`).
- Por indicador muestra el cierre objetivo, la diferencia contra la proyeccion, el ritmo diario requerido
  (para inversion, "Presupuesto diario requerido"), el ritmo actual, lo que falta realizar y la brecha contra
  el presupuesto. La cuarta tarjeta resume la inversion adicional o menor y la eficiencia que se conserva.
- Controles: arrastrar el nodo (escritorio), escribir el valor en "<Indicador> al cierre" (obligatorio en
  celular, donde arrastrar desplaza la pagina), "Llevar al presupuesto" y "Restablecer" (o doble clic sobre
  el nodo).
- El simulador se desactiva cuando el informe del mes proyectado ya cubre el mes completo.

### Proyeccion por campana

Un cuadro por campana del mes proyectado (el mismo de la linea de tiempo).

- Cada cuadro tiene inversion, clics y conversiones: real a la fecha de corte, ritmo diario y proyeccion al
  cierre, con el mismo metodo que la proyeccion general (ritmo diario de la campana x dias del mes).
- Presupuesto del mes = presupuesto diario de la campana x dias del mes, con una barra de avance de la
  proyeccion y lo que queda o excede. Es el presupuesto vigente al exportar el informe.
- Una campana con estado distinto de "Habilitada" (por ejemplo, "Detenida") cierra el mes con lo ya gastado.
- Tambien aparecen las campanas con gasto el mes anterior que aun no salen en el informe del mes, y cada
  cuadro muestra el gasto del mes anterior si su informe esta cargado.
- Con pocos dias de datos la proyeccion es muy sensible (un dia en cero proyecta cero): el cuadro lo avisa la
  primera semana del mes.

### Informe del mes en curso fuera de Drive

Un informe de campana descargado de Google Ads se puede cargar sin pasar por Drive, con el mismo parser:

```bash
python scripts/sync-drive.py --file "ruta/Informe de campaña.csv"
```

El mes se toma de la linea de rango del informe. Queda en el JSON como cualquier mes y la sincronizacion con
Drive lo conserva hasta que la carpeta traiga un informe del mismo mes, que entonces lo reemplaza. Lo ideal es
subir el informe a la carpeta como `Hers <mes> <ano>` y reemplazarlo cada vez que se actualice.

## Analisis de Palabras Clave

Pestana **Palabras Clave** del panel lateral. Lee el informe de palabras clave de busqueda de Google Ads de la
carpeta de Drive **Google Ads Hers Keywords**, una hoja de calculo por mes (`Hers KW <mes> <ano>`):

<https://drive.google.com/drive/folders/1Fc1ahioYK0F41NjJ9hWn-RSANUNbNKM3>

- **Filtros**: mes, campana (todas o una) e indicador de tendencia (clics, impresiones, costo, conversiones,
  CTR o costo por conversion). Quedan guardados en `localStorage` (`hers_keywords_prefs`).
- **KPIs del mes**: palabras clave (y cuantas convierten), impresiones, clics, costo, conversiones, costo por
  conversion y gasto sin conversiones, con la variacion contra el mes anterior.
- **Un cuadro por campana**, ordenadas por costo, con sus totales y dos vistas:
  - **Detalle del mes**: una fila por palabra clave con estado, impresiones, clics, CTR, CPC, costo,
    conversiones, costo por conversion, % de impresiones perdidas por ranking, una linea de tendencia del
    indicador elegido en todos los meses (punto lleno = mes del filtro) con la variacion contra el mes
    anterior, y una **Lectura**: *Eficiente* (costo por conversion igual o menor al de su campana), *CPA alto*
    (mayor), *Gasta sin convertir* (costo sin conversiones), *Sin clics* o *Sin impresiones*.
  - **Evolucion mensual**: una fila por palabra clave y una columna por mes con el indicador elegido, con
    color por intensidad dentro de la fila y el acumulado del periodo.
- Una palabra clave se identifica por texto y concordancia dentro de su campana; si esta en varios grupos de
  anuncios se suma en una fila. CTR, CPC y costo por conversion se calculan sobre las sumas, como Google Ads.
- La campana se marca "Campaña detenida" cuando todas sus palabras clave traen ese motivo de estado.
- **Informe sin columna Campaña.** Un informe exportado desde dentro de una campana no trae la columna Campaña
  (sus totales dicen "Palabras clave de tu campaña"). En ese caso las palabras clave se asignan a la campana del
  informe de Gasto Publicitario del mismo mes, si es una sola; si no, quedan como "Campaña del informe". Lo
  mejor es exportar desde el nivel de cuenta, que trae la columna (asi estan los informes de Hers).
- Los informes de setiembre y octubre de 2026 no traen las columnas de cuota de impresiones, asi que
  "Perdidas x ranking" sale vacia.
- **Palabras clave quitadas.** El informe no lista las palabras clave que se quitaron de la cuenta, pero su gasto
  si esta en el total de la cuenta. En setiembre de 2026 las filas suman S/ 436.75, 465 clics y 130.03
  conversiones ("Total: Todas las palabras clave, excepto las quitadas"), contra S/ 461.27, 487 clics y 135.03
  conversiones de la cuenta y de Gasto Publicitario. La diferencia es de las palabras clave quitadas, no un error.
- **Revisar el archivo antes de subirlo**: la primera linea debe decir "Informe de palabras clave de busqueda".
  Un "Informe de campaña" en esta carpeta se descarta.

### Datos y sincronizacion

- `scripts/sync-keywords.py` recibe cada hoja ya exportada como CSV del Apps Script de Hers (la carpeta es
  privada, ver "Carpetas privadas") y escribe
  `data/hers-palabras-clave-2026.json`, `data/keywords-manifest.json` y una copia de cada informe en
  `data/csv-backups/keywords/`. El mes sale de la linea de rango del informe ("7 de septiembre de 2026 - ...")
  y, si no esta, del nombre del archivo. Reusa los helpers de `scripts/sync-drive.py`.
- **Todos los dias**: es un paso mas de `.github/workflows/sync-drive.yml` (06:30, 13:30 y 18:30 en Lima). Si cambio algo,
  hace commit y publica el tablero. Cada carpeta se sincroniza aunque la otra falle. Si la carpeta no trae
  ningun informe el paso falla (y el workflow queda en rojo), pero los meses ya cargados se conservan.
- **Boton Actualizar** (en el filtro): como el navegador ya no puede bajar las hojas privadas, pide el mismo
  barrido del servidor que el boton Sincronizar (`HERS_SYNC_URL`), que trae tambien las palabras clave, y
  muestra el avance en su propio estado. Sin esa URL trae la ultima publicacion. Solo con
  `HERS_DRIVE_API_KEY` incrustada en el build lee Drive desde el navegador y guarda el resultado en
  `localStorage` (`hers_keywords_cache`).
- La carpeta y su ID estan en `data/drive-config.json` (`keywords.folderId`).
- **Campanas excluidas**: `keywords.excludeCampaigns` en `data/drive-config.json` lista campanas que no se
  muestran ni se guardan aunque vengan en el informe (hoy ninguna). Se filtran en la sincronizacion diaria, en
  el boton Actualizar y al mostrar. La copia cruda del informe en `data/csv-backups/keywords/` las conserva.
- Las hojas tienen que estar en **Google Ads Hers Keywords**. El Apps Script solo lee esa carpeta y la de
  campanas (`CARPETAS_LECTURA`); las otras carpetas de Hers en Drive ("Términos de Búsqueda Hers" y
  "Google Ads Hers Segmentacion") quedan fuera de la sincronizacion.

```bash
python scripts/sync-keywords.py          # sincroniza
python scripts/sync-keywords.py --check  # dice si hay cambios, sin escribir
python scripts/sync-keywords.py --file "Hers KW octubre 2026.csv"  # importa un informe local
```

**Numeros dañados por Sheets.** Al convertir un CSV a hoja de calculo, Sheets puede leer los miles como
decimales y perder los ceros finales: `1,460` impresiones quedan como `1,46` y `1,200` como `1,2`. Como un grupo
de miles siempre tiene tres digitos, los parsers (palabras clave y campanas) los completan (`1,46` -> 1460). Los
valores tipo `08.03` (Sheets los leyo como fechas) se leen como 8.03. No hay que corregir nada a mano. Con eso las
sumas de cada mes cuadran con la fila "Total: Todas las palabras clave, excepto las quitadas" del informe (el
costo puede diferir en centimos por el redondeo por fila). Un CSV subido sin convertir tambien sirve para la
sincronizacion diaria, pero sin API key el boton Actualizar solo lee hojas de Google Sheets.


## Leads (CRM de feedback del cliente)

Pestana **Leads** del panel lateral (`js/leads.js`). Una tabla por campana y mes: la plataforma se carga sola y
el cliente completa en las columnas resaltadas lo que de verdad recibio; el tablero calcula los indicadores que
las plataformas no ven.

- **Se habilita solo con campanas de leads o WhatsApp** (`detectObjective`): en Google Ads, Busqueda,
  Maximo rendimiento y Demand Gen (no Shopping, Display ni Video); en Meta Ads, el objetivo Clientes potenciales
  o Mensajes; un nombre con WhatsApp o mensajes va como WhatsApp. Si la marca no tiene ninguna, la pestana no
  aparece. En Hers entra su campana de Busqueda (Canastas Navideñas).
- **Se cargan solos**, del modulo Gasto Publicitario (`HersDashboard.snapshot()`): la inversion y los leads
  que reporta la plataforma. En Google Ads son sus conversiones (contactos); en Meta Ads serian los formularios o
  las conversaciones de WhatsApp, cuando la marca tenga esa data (`platformMonths` es el punto para sumarla).
- **Lo completa el cliente** (columnas resaltadas, con guardado automatico): leads reales, calificados, ventas y
  monto vendido de cada campana en el mes. Vacio = sin dato. Se valida que sean enteros desde 0 y que no haya mas
  calificados que leads ni mas ventas que calificados. En "Todo el ano" la tabla suma los meses y queda de solo
  lectura. La columna Campana queda fija al desplazarse.
- **Indicadores por campana y en total**: coincidencia (leads reales / de la plataforma), costo por lead de la
  plataforma y real, tasa de calificacion y de cierre, costo por calificado, costo por venta y ROAS (monto
  vendido / inversion). Cada cociente usa solo las campanas y meses que tienen ese dato completo, asi la tabla a
  medio llenar no infla los costos; los KPIs dicen cuantas campanas estan completadas.
- **Guardado**: en una hoja de Google privada de la agencia (pestana "Leads por campana", una fila por mes y
  campana), a traves del Web App de `scripts/apps-script/crm-leads.gs`. Nunca pasa por el repositorio publico.
  Sin el Web App conectado, el modulo guarda solo en el navegador y lo avisa ("Modo de prueba").

### Conectar la hoja privada (una sola vez)

1. Crear una hoja de calculo de Google (por ejemplo "Hers - CRM de leads") en la cuenta de la agencia.
2. En la hoja: Extensiones > Apps Script, pegar `scripts/apps-script/crm-leads.gs` y guardar.
3. Ejecutar `crearToken` una vez (aceptar los permisos): crea la pestana "Leads por campana" y deja el token en
   el registro de ejecucion.
4. Implementar > Nueva implementacion > Aplicacion web: ejecutar como **Yo**, acceso **Cualquier usuario**.
   Copiar la URL que termina en `/exec`.
5. En GitHub > Settings > Secrets and variables > Actions, crear `HERS_CRM_URL` (la URL) y `HERS_CRM_TOKEN` (el
   token). El siguiente deploy los incrusta dentro del HTML cifrado.

"Cualquier usuario" deja la URL abierta, pero sin el token responde "No autorizado"; el token solo existe en el
secret y dentro del HTML cifrado con `HERS_PAGE_PASSWORD`. Para cambiarlo: ejecutar otra vez `crearToken` y
actualizar `HERS_CRM_TOKEN`. Si se edita el script, hay que publicar una version nueva de la implementacion
(Implementar > Gestionar implementaciones) para que la URL use el codigo nuevo.

## Fuente de datos

El tablero se arma desde la carpeta de Google Drive **Google Ads Hers Campañas** (dentro de
`02_Clientes de la agencia/Hers`):

<https://drive.google.com/drive/folders/1sNZ2FuYWntAn9ARHiY-TLxcE9SqMJbLR>

Una hoja de calculo por mes con el informe de campana de Google Ads (`Hers <mes> <ano>`, con Costo, Impr.,
Clics, CTR, Conversiones, Costo/conv., Presupuesto y Estado de la campaña). Tambien sirve el CSV tal como lo
exporta Google Ads (`Hers <mes> <ano>.csv`). Para agregar un mes basta con subir su informe a esa carpeta: la
sincronizacion lo detecta por el rango de fechas del archivo y lo suma a las pestanas de mes. Los informes se
exportan a nivel de **cuenta**, para que entren todas las campanas de Hers.

La fuente normalizada que consume el tablero es `data/hers-lima-retail-2026.json`, agrupada por mes:

```json
{
  "defaultMonth": "2026-10",
  "drive": { "lastSync": "2026-10-06T06:40:00Z", "discovery": "publica", "files": [] },
  "months": [
    {
      "id": "2026-09",
      "label": "Setiembre 2026",
      "sourceFile": "Hers setiembre 2026",
      "driveFileId": "1TV1uqJomZbE4GrQkdOXsXPgN78dHbY7PNSnEIZGjcmY",
      "records": [ /* una fila por campana, con estado, presupuesto diario y % de variacion */ ],
      "totals": { "cost": 461.27, "impressions": 3593, "clicks": 487, "conversions": 135.03 },
      "period": { "start": "2026-09-07", "end": "2026-09-30" },
      "dailyBudget": 20
    }
  ]
}
```

Los campos `*Delta` de cada campana (variacion contra el mes anterior cargado) los calcula la sincronizacion; el
tablero no los usa: compara con el mes calendario anterior (ver Modulo principal). Cada campana guarda tambien su
`type` (columna `Tipo de campaña`, si el informe la trae).
`period` es el rango de fechas del informe y `dailyBudget` la suma de presupuestos diarios de las campanas
habilitadas (ver Proyecciones); cada campana trae ademas su `status` y su `dailyBudget`. Las series diarias
(`daily`, opcionales) son independientes: la sincronizacion no las toca.

## Sincronizacion

### Automatica, todos los dias

`.github/workflows/sync-drive.yml` corre todos los dias a las **06:30, 13:30 y 18:30 de Lima**,
baja las dos carpetas, actualiza `data/` y, solo si algo cambio, hace commit y pide la
publicacion del tablero. Tambien se puede lanzar a mano desde
**Actions > Sincronizar Drive > Run workflow**.

GitHub atrasa varias horas los workflows programados (en octubre de 2026 el de las
06:20 corria entre las 10:50 y las 15:20), asi que la hora la fija un activador de
**Google Apps Script** que lanza el workflow al momento
(`scripts/apps-script/sincronizar-hers.gs`; Apps Script lo corre dentro de unos
15 minutos de cada horario). El workflow ya no tiene cron de GitHub: atrasaba varias
horas y repetia el barrido. Si el activador falla, Apps Script avisa por correo y
la pildora del tablero pasa a ambar a las 30 horas sin barrido. Instalacion, una
sola vez:

1. En GitHub > Settings > Developer settings > Personal access tokens > Fine-grained
   tokens, crear un token solo para el repositorio `Hers` con el permiso
   **Actions: Read and write**. Anotar su vencimiento para renovarlo.
2. En <https://script.google.com>, proyecto nuevo: pegar
   `scripts/apps-script/sincronizar-hers.gs` y poner la zona horaria del proyecto
   en (GMT-05:00) Lima.
3. Configuracion del proyecto > Propiedades del script: `GITHUB_TOKEN` = el token. El
   token no va en el codigo ni en el repo.
4. Ejecutar `instalarActivador` una vez (aceptar los permisos) y luego
   `sincronizarHers` para probar: en GitHub > Actions aparece la ejecucion.

La pildora de la barra superior muestra el ultimo barrido real (lo lee de la API
publica de GitHub, sin credenciales) y se pone ambar si fallo o si pasaron mas de
30 horas sin uno.

Para correrla en local:

```bash
python scripts/sync-drive.py          # sincroniza
python scripts/sync-drive.py --check  # dice si hay cambios, sin escribir
```

El script encuentra los archivos en este orden: Apps Script de Hers (si hay
`HERS_DRIVE_SCRIPT_URL` y `HERS_DRIVE_READ_KEY`), API de Drive (si hay API key),
vista publica de la carpeta (solo si siguiera compartida por enlace) y, como
ultimo recurso, `data/drive-manifest.json`. Las hojas de calculo se exportan como
CSV (`docs.google.com/spreadsheets/d/<id>/export?format=csv`).

### Carpetas privadas

Las carpetas de campanas y palabras clave no se comparten por enlace (sus IDs estan en este repositorio
publico). El barrido las lee con la accion `leer` del mismo Web App del boton Sincronizar
(`scripts/apps-script/sincronizar-hers.gs`), que corre con la cuenta duena de las carpetas:

- Solo responde con la clave `LECTURA_CLAVE` de las Propiedades del script, enviada por POST (nunca en la
  URL), y solo para las carpetas de `CARPETAS_LECTURA`. Devuelve cada hoja exportada como CSV.
- En GitHub la clave es el secret `HERS_DRIVE_READ_KEY` y la URL es la de `HERS_SYNC_URL`
  (`sync-drive.yml` la pasa como `HERS_DRIVE_SCRIPT_URL`).
- Al editar el script hay que publicar una version nueva de la misma implementacion (Implementar >
  Gestionar implementaciones > lapiz > Version: Nueva version), para que la URL no cambie, y ejecutar
  `probarLectura` una vez para aceptar el permiso de Drive.
- De la carpeta de reglas («08 Reglas y Optimizacion») solo devuelve las cinco columnas de
  `COLUMNAS_PERMITIDAS`: la «Instruccion Estrategica para la IA» de esa hoja esta escrita para la
  agencia y no sale de LR Suite, asi que el recorte se hace en el Apps Script y no aqui.
- Para probar en local: `HERS_DRIVE_SCRIPT_URL=<url> HERS_DRIVE_READ_KEY=<clave> python scripts/sync-drive.py --check`
  (y lo mismo con `--reglas --check` para la hoja del ciclo mensual).

### Manual, desde el tablero

La sincronizacion vive en la barra superior derecha. La pildora dice hasta que dia
llegan los datos y la hora del ultimo barrido; al pulsarla abre el detalle: datos, de
donde salen (Origen), meses cargados, ultimo y proximo barrido, cuando cambiaron los
datos, las dos carpetas de Drive (con enlace, solo para quien tenga acceso) y lo que falta subir. A su lado va el boton
**Sincronizar** (en Gasto Publicitario y Proyecciones; Palabras Clave tiene su propio
boton Actualizar). Si la sincronizacion falla, la pildora se pone ambar y el detalle
dice por que. Su comportamiento depende de `data/drive-config.json`:

- **Con el Web App del barrido** (`HERS_SYNC_URL`, el que manda sobre los demas): el boton le pide a
  `scripts/apps-script/sincronizar-hers.gs`, publicado como aplicacion web, el mismo barrido programado tres veces al día
  (`sync-drive.yml`). Sigue la corrida por la API publica de GitHub: si Drive trajo cambios espera la
  publicacion y recarga; si no, avisa que el tablero ya estaba al dia. Como mucho un barrido cada 4
  minutos para todos los visitantes. La URL llega por el secret `HERS_SYNC_URL` y solo queda dentro
  del HTML cifrado.
- **Con `apiKey`**: el navegador lee la carpeta y los informes (hojas o CSV)
  directo de Drive, actualiza el tablero sin recargar y guarda el resultado en
  `localStorage`. Ademas se sincroniza solo cuando pasaron `autoSyncHours` horas
  (24 por defecto) desde la ultima vez.
- **Sin `apiKey`** (estado actual del sitio publicado): el boton trae la ultima
  publicacion y el Origen del detalle dice "Ultima publicacion del tablero". La
  carga diaria la sigue haciendo GitHub Actions.

Para habilitar la sincronizacion desde el navegador hace falta una **clave de
API de Google** (Google Cloud > APIs y servicios > Credenciales > Clave de API),
con la **Google Drive API** habilitada y la clave restringida a
`https://hers.limaretail.com/*`. La clave no va en el repo: se guarda en el
secret **`HERS_DRIVE_API_KEY`** y `scripts/build.js` la incrusta en el HTML
cifrado al publicar. Para probar en local se puede poner en `apiKey` dentro de
`data/drive-config.json` (sin commitearla).

Las carpetas deben seguir compartidas como "cualquiera con el enlace puede ver":
es lo que permite leerlas sin cuenta de servicio.

## Importar archivos sueltos

`scripts/import-hers-data.py` sigue disponible para cargar un CSV o Excel
que no este en Drive, por ejemplo las series diarias de impresiones:

```bash
python scripts/import-hers-data.py "ruta/al/archivo.csv" --month 2026-09
```

## Build

```bash
npm.cmd run build
```

El resultado se genera en `dist/`: `index.html` (CSS, JS y datos incrustados), `assets/` y `CNAME`.
Nada mas: `data/` (incluido `data/csv-backups`), `scripts/` y `docs/` no se publican.

Con `HERS_PAGE_PASSWORD=<clave> npm run build` el `index.html` sale cifrado, como en GitHub Pages.

En este equipo hay que detener el preview `dev` antes del build: mientras corre bloquea `dist/`.

## Publicacion en hers.limaretail.com (GitHub Pages)

Repositorio: `jorgeluis666/Hers`. URL publica: **https://hers.limaretail.com**. Cada push a `main` la actualiza
sola (`.github/workflows/deploy-pages.yml`).

Puesta en marcha (una sola vez):

1. **Settings > Secrets and variables > Actions**: crear `HERS_PAGE_PASSWORD` (la clave del tablero) y
   `HERS_USERS_PRIVATE_KEY` (el contenido de `credentials/hers-usuarios-privada.pem`).
   `HERS_DRIVE_API_KEY` es opcional (ver "Manual, desde el tablero").
2. **Settings > Pages > Build and deployment > Source**: *GitHub Actions*.
3. **Actions > Publicar en GitHub Pages > Run workflow** (o un push a `main`). El primer push falla mientras no
   exista `HERS_PAGE_PASSWORD`: es lo esperado.
4. **Settings > Pages > Custom domain**: `hers.limaretail.com`, pulsar *Save* y, cuando el certificado este
   listo, marcar **Enforce HTTPS**.

Detalles:

- Pages publica con Actions (sube `dist/`), asi que el dominio propio se configura en **Settings > Pages >
  Custom domain** y queda guardado en el repo. Ademas `CNAME` (raiz) lleva el dominio y `build.js` lo copia a
  `dist/`, igual que en los demas tableros de la agencia.
- DNS en Banahosting (cPanel > Zone Editor > `limaretail.com`): registro **CNAME** `hers` ->
  `jorgeluis666.github.io` (ya existe).
- **Clave:** Pages no tiene Basic Auth, asi que `scripts/build.js` cifra el tablero completo (datos y JS) con
  AES-256-GCM y una llave PBKDF2-SHA256 (600 000 iteraciones) derivada del secret **`HERS_PAGE_PASSWORD`**.
  `deploy/pages-gate.html` pide la clave y lo descifra en el navegador; sin ella el HTML publicado no revela
## Notificaciones y alerta de Google Ads

El mismo sistema de alertas de Royal Baby, con los numeros de Google Ads. Tiene dos caras y las dos
salen del mismo calculo (`js/alertas-core.js`): el ultimo mes con inversion, su ritmo diario y el
cierre proyectado (ritmo x dias del mes, igual que Proyecciones), frente a dos referencias: el
**presupuesto del mes** (el presupuesto diario de las campanas por los dias del mes, como en
Proyecciones) y el **mes anterior**, llevado a mes completo cuando su informe quedo a medias.

**La campanita de la barra superior** (`js/notificaciones.js`, a la izquierda de la pildora de
sincronizacion) muestra los avisos del mes y, con un clic, abre el modulo donde se ve ese numero:

| Aviso | Cuando aparece |
| --- | --- |
| Cierre del gasto vs presupuesto | hay presupuesto diario; rojo si el cierre pasa el 115 %, ambar sobre 105 % o por debajo del 80 % |
| Conversiones al cierre vs mes anterior | rojo bajo el 80 %, ambar bajo el 95 % |
| Costo por conversion vs mes anterior | ambar si sube mas de 10 %, rojo sobre 30 % |
| Campanas que gastan sin conversiones | alguna campana del mes gasto y no convirtio |
| La campana que sostiene el mes | informativo: cuanta conversion concentra y a que costo |

**El Ciclo Mensual de Analisis y Optimizacion** (`data/reglas.json`) decide en que orden se leen
esos avisos. Las cuatro reglas dicen, por el dia del mes que cubre el informe, en que semana del
ciclo vamos; con eso el panel encabeza con una linea de contexto («Semana 3 del mes») y sube
primero el aviso que toca mirar esa semana:

| Semana | Acción | Qué sube primero |
| --- | --- | --- |
| 1 | `alerta_pacing` | el cierre del gasto frente al presupuesto |
| 2 | `pausar_fatigados` | las campañas que gastan sin resultado |
| 3 | `alerta_overlap` | la campaña que concentra el resultado |
| 4 | `escalar_presupuesto` | el presupuesto que va a quedar sin usar |

Ningun aviso se esconde: el que no toca esa semana baja de puesto, nada mas. La hoja
`Reglas_Nexus_Hers` de la carpeta «08 Reglas y Optimizacion» del Drive de la marca es la raiz
editable de estas reglas: lo que el equipo cambie ahi llega al tablero en el siguiente barrido.
`scripts/sync-drive.py --reglas` la lee con el Apps Script de la marca (esa carpeta tiene los
permisos restringidos al equipo, asi que no se puede leer por la vista publica de Drive) y escribe
`data/reglas.json`, que el workflow commitea solo cuando cambia una regla. La **instruccion
estrategica** de esa hoja no viaja aqui: la recorta el propio Apps Script y se queda en LR Suite.
Sin `data/reglas.json` los avisos salen como siempre, sin ordenar y sin la linea de contexto.

La ruedita del panel abre la lista de **correos de la alerta** (nombre opcional y correo, hasta 25).
La lista vive en el Web App de `scripts/apps-script/alertas-destinatarios.gs` (propiedades del
script, no en el repo): el tablero publicado la edita con la URL y la clave que el build deja dentro
del HTML cifrado, y la alerta la lee antes de cada envio. Mientras nadie guardo una lista, o si el
Web App no responde, se usan los destinatarios de `data/alertas-config.json`; una lista guardada
vacia no envia nada.

**El correo** (`scripts/alertas.js`, con la estetica del tablero) sale por
`.github/workflows/alertas-semanales.yml`:

- **Lunes a las 6:00 p. m. de Lima**, una vez por semana.
- **Despues de cada barrido de Drive** (workflow "Sincronizar Drive"), solo si cambiaron las cifras
  del mes desde el ultimo envio: se compara una huella guardada en `data/alertas-envios.json`.
- A mano: `Actions > Alerta semanal de Google Ads > Run workflow`, con «Simulacion» para no enviar y
  «Reenviar» para mandarla aunque ya haya salido esa semana.
- En local, sin enviar nada: `node scripts/alertas.js --preview correo.html` escribe el correo en un
  archivo y `node scripts/alertas.js --dry-run` muestra a quien iria.

El correo lee `data/hers-lima-retail-2026.json`, el mismo archivo que publica el tablero, asi que las dos vistas
muestran los mismos numeros.

**Puesta en marcha** (una vez): ejecutar `instalar()` en el Web App de
`scripts/apps-script/alertas-destinatarios.gs`, implementarlo como aplicacion web con acceso
«Cualquier persona» y crear los secrets:

| Secret | Para que |
| --- | --- |
| `HERS_ALERTAS_URL`, `HERS_ALERTAS_TOKEN` | la lista de correos que se edita en el tablero (build y alerta) |
| `SMTP_HOST`, `SMTP_PORT`, `SMTP_USER`, `SMTP_PASS` | cuenta alertas@limaretail.com del hosting, que tambien es el remitente |
| `RESEND_API_KEY` | alternativa si no hay SMTP |
| variable `DASHBOARD_URL` | opcional, si el enlace del correo debe ser otro que el de `data/alertas-config.json` |

  nada. Si falta el secret, o si `dist/index.html` sale sin cifrar, el workflow falla en vez de publicar.
  La pantalla usa el mismo diseno y el azul de LR Suite que el acceso de los demas tableros de la agencia
  (fondo `assets/login-bg.jpg`), pero la clave no esta escrita en el codigo.
- Es una sola clave compartida. Como el HTML cifrado es publico, se puede atacar sin limite de intentos. Para
  cambiarla: editar el secret `HERS_PAGE_PASSWORD` y volver a ejecutar el workflow (Actions > Publicar en
  GitHub Pages > Run workflow).
- Tras entrar, la llave queda en `sessionStorage` de esa pestana para no pedir la clave al recargar; cada deploy
  genera una sal nueva, asi que despues de publicar se vuelve a pedir. Una pestana nueva tambien la pide.
- Las preferencias (mes elegido, vista y panel minimizado) viven en `localStorage` de cada dominio.
- El repo es publico: lo que esta en `data/` sigue siendo visible en GitHub aunque el sitio vaya con clave. La
  excepcion es el directorio de Usuarios y Claves, que va cifrado (ver "Cifrado del directorio").

## Marca

| Que | Donde |
| --- | --- |
| Isotipo de la barra lateral | `assets/hers-isotipo.png` (provisional: "H" blanca en Georgia Bold sobre `#8b1d41`, 168×168 con esquinas redondeadas; no se encontro una web de Hers con logo) |
| Color de marca (vino Hers `#8b1d41`) | `css/dashboard.css`, variables `--brand*` y colores de los graficos en `js/` |
| Fondo del acceso | `assets/login-bg.jpg` (el mismo de los demas tableros) |

Para cambiar el isotipo por el logo real basta con reemplazar `assets/hers-isotipo.png` por otro PNG cuadrado
del mismo tamano. Si cambia el color, hay que reemplazar `#8b1d41` y sus derivados (`#f8e8ee`, `#6b1632`,
`#ecc6d3`, `#d38aa2` y `rgba(139, 29, 65, ...)`) en `css/dashboard.css` y en `js/`.


Nexus extrae Google Ads a las 06:00, 13:00 y 18:00 (America/Lima).
Los tres activadores de Apps Script barren Drive aproximadamente media hora después.
El cambio de mes crea las hojas en las carpetas existentes; los meses ya configurados conservan sus enlaces.
