# Temperatura del aire y del suelo: regímenes y tendencia

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/AGRIdigital-Climbat/PlantillaMapa/blob/main/03_Temperatura/01_temperatura.ipynb)

PROSUCO · CLIMBAT — análisis por municipio de Bolivia con Google Earth Engine desde Colab.

**Pregunta territorial:** ¿Qué regímenes térmicos tiene el municipio y cuánto se está calentando?

## Satélites y datasets (IDs exactos de GEE)

| Variable | ID GEE | Banda(s) | Resolución | Período | Detalle para Bolivia |
|---|---|---|---|---|---|
| T. aire (primario) | `ECMWF/ERA5_LAND/DAILY_AGGR` | `temperature_2m_min/max` (**Kelvin** → −273,15 a °C) | ~11 km | 1950–actual | Serie diaria 30+ años; base de tendencia °C/década |
| T. aire (alternativa) | `IDAHO_EPSCOR/TERRACLIMATE` | `tmmx/tmmn` (escala **×0,1**) | ~4 km | 1958–actual | Mensual; mejor resolución espacial que ERA5 |
| T. superficie LST (alternativa/avanzado) | `MODIS/061/MOD11A2` (Terra) y `MYD11A1` (Aqua, celda avanzada incluida) | `LST_Day_1km`/`LST_Night_1km` (×0,02 → Kelvin) | 1 km | 2000–actual | Temperatura **del suelo/vegetación**, no del aire: de día difiere +5–15 °C del aire en Altiplano |

## Qué extrae este análisis (contenido avanzado incluido)

- **Doble mapa Tmin/Tmax por año con selector** (`Dropdown` + refresco forzado con sufijo temporal, corrección del bug de capas que no repintan).
- **Serie diaria + mensual + anual + tendencia TODO EN COLAB** (ERA5-Land): detección del rango real de datos (intersección con `FECHA_INICIO/FIN`), extracción por bloques con `getRegion`, media móvil 12 meses, `scatter` diario diezmado, tendencia lineal °C/década con `np.polyfit`.
- **Celda avanzada de LST MODIS Aqua (MYD11A1)**: serie diaria+mensual de temperatura del suelo diurna/nocturna — no se desperdicia, queda como contraste suelo vs. aire.
- Contexto Bolivia: Altiplano con rango diario 15–20 °C (más estrés que el promedio); Valles templados 12–22 °C; Llanos 24–32 °C con surazos invernales. La tendencia andina ronda +0,15–0,25 °C/década según producto.

## Limitaciones

- ERA5 a 11 km no resuelve inversiones térmicas de valle ni microclimas de ladera: complementa con estaciones CLIMBAT.
- LST con nubes = sin dato (época húmeda dic–mar con huecos en Amazonía/Yungas); la celda usa medianas mensuales para mitigarlo.

## Autenticación GEE (igual en los 12 análisis)

1. Regístrate en [Earth Engine](https://earthengine.google.com/) (cuenta *noncommercial / Community*).
2. Crea un Cloud Project y anota su ID.
3. En la celda `1.2 Autenticación` del notebook pon tu ID y ejecuta:

```python
EE_PROJECT = "tu-cloud-project"
ee.Authenticate()
ee.Initialize(project=EE_PROJECT)
```

## Área de estudio: el GeoJSON siempre manda

```python
AOI_MODE = "auto"
RUTA_AOI = "https://drive.google.com/file/d/11k-2-0MfG84QcYld7XY3r5XVMDHWtJ0N/view?usp=sharing"
```

- El archivo debe estar compartido como **"Cualquiera con el enlace"**.
- Acepta 3 formas sin subir nada a mano: **enlace/ID de Drive** (descarga con `gdown`),
  **ruta en tu Drive montado** (`/content/drive/MyDrive/...`) o **carpeta con un `.geojson`**.
- Al cargarse, `roi`, `ZONA["bbox"]`, `ZONA["centro"]` y `area_km2` se **recalculan desde el polígono**.
  El `bbox` de CONFIG (Escoma por defecto) solo es respaldo si cambias a `AOI_MODE = "bbox"`.
- Prepara el polígono en QGIS: Exportar → Guardar entidades como → **GeoJSON, CRS EPSG:4326**.

## Años y satélite (igual en los 12 análisis)

```python
FECHA_INICIO = "1996-01-01"   # ventana analítica 30+ años
FECHA_FIN = "2026-12-31"      # el año en curso se excluye de métricas (ANIOS_COMPLETOS)
```

Cada celda de análisis trae el patrón primario + alternativas comentadas:

```python
# --- Primario (default) ---
precip = ee.ImageCollection("UCSB-CHG/CHIRPS/DAILY")
# --- Alternativas: comentar la primaria y descomentar la elegida ---
# precip = ee.ImageCollection("NASA/GPM_L3/IMERG_V06")   # 2000+, satelital puro
```

> Regla de reporte: todo número se cita con **período + dataset** (ej. `CHIRPS 1981–2025`).
> Un ajuste lineal simple no sustituye pruebas formales (Mann-Kendall).

## Sesión

| Notebook | Abrir en Colab |
|---|---|
| `01_temperatura.ipynb` | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/AGRIdigital-Climbat/PlantillaMapa/blob/main/03_Temperatura/01_temperatura.ipynb) |

> El botón solo funciona si el repositorio es **público**. En repo privado, Colab pide permiso de GitHub.
> Antes de ejecutar: limpia salidas si vas a hacer push (`Edit → Clear all outputs`).
