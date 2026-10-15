# Precipitación: régimen, estacionalidad y meses críticos

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/AGRIdigital-Climbat/PlantillaMapa/blob/main/02_Precipitacion/01_precipitacion.ipynb)

PROSUCO · CLIMBAT — análisis por municipio de Bolivia con Google Earth Engine desde Colab.

**Pregunta territorial:** ¿Cuánto llueve, en qué meses se concentra y qué años fueron extremos?

## Satélites y datasets (IDs exactos de GEE)

| Variable | ID GEE | Banda(s) | Resolución | Período | Detalle para Bolivia |
|---|---|---|---|---|---|
| Precipitación (primario) | `UCSB-CHG/CHIRPS/DAILY` | `precipitation` (mm/día) | 0,05° (~5 km) | 1981–actual | Fusión estación+satelital; el más estable para series 30+ años en Bolivia |
| Precipitación (alternativa) | `NASA/GPM_L3/IMERG_V06` | `HQprecipitation` | 0,1° (~10 km) | 2000–actual | Satelital puro, horario; mejor para tormentas convectivas de Llanos/Amazonía |
| Precipitación (alternativa) | `ECMWF/ERA5_LAND/DAILY_AGGR` | `total_precipitation_sum` (**metros** → ×1000 a mm) | ~11 km | 1950–actual | Reanálisis; útil 1950–1980 donde CHIRPS no llega |
| Precipitación (alternativa) | `IDAHO_EPSCOR/TERRACLIMATE` | `pr` (mm, ×0,1 si viene escalado según versión) | ~4 km | 1958–actual | Mensual; cómodo para climatologías rápidas |

## Qué extrae este análisis (contenido avanzado incluido)

- **M2.1b** Doble gráfico: líneas mensuales por año + barras de total anual por año (los anuales derivan de los mismos mensuales, sin llamadas extra a GEE).
- **M2.2** Serie anual, detección de años extremos y **mapa con selector de año** (`ipywidgets` Dropdown).
- Climatología de 12 meses, meses secos (`< UMBRALES["mes_seco_mm"] = 30 mm/mes`).
- Contexto Bolivia: gradiente extremo — Amazonía/Pando >2000 mm/año, Chapare >3000 mm, Altiplano 400–700 mm, Chaco <600 mm. Régimen monzónico (SAMS): verano húmedo dic–mar, invierno seco may–ago. Eventos El Niño suelen secar Altiplano/Chaco.

## Limitaciones

- CHIRPS subestima picos convectivos aislados en Llanos; IMERG los capta mejor pero solo desde 2000.
- ERA5-Land a 11 km suaviza valles estrechos (Yungas, valles interandinos): úsalo como contexto, no como valor puntual.

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
| `01_precipitacion.ipynb` | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/AGRIdigital-Climbat/PlantillaMapa/blob/main/02_Precipitacion/01_precipitacion.ipynb) |

> El botón solo funciona si el repositorio es **público**. En repo privado, Colab pide permiso de GitHub.
> Antes de ejecutar: limpia salidas si vas a hacer push (`Edit → Clear all outputs`).
