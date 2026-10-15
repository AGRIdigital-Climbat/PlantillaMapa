# Manantiales y bofedales: proxies topográficos e hídricos

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/AGRIdigital-Climbat/PlantillaMapa/blob/main/09_Manantiales_Bofedales/01_manantiales_bofedales.ipynb)

PROSUCO · CLIMBAT — análisis por municipio de Bolivia con Google Earth Engine desde Colab.

**Pregunta territorial:** ¿Dónde mirar primero si busco manantiales o bofedales?

## Satélites y datasets (IDs exactos de GEE)

| Variable | ID GEE | Banda(s) / criterio | Resolución | Detalle para Bolivia |
|---|---|---|---|---|
| Agua permanente | `JRC/GSW1_4/GlobalSurfaceWater` | `occurrence` ≥ `manantial_occurrencia` (100 %) | 30 m | Ojos de agua que nunca se secaron 1984–2021 |
| Cabeceras | `WWF/HydroSHEDS/15ACC` | `b1` ≤ `manantial_drenaje_max` (50 celdas) y >0 | ~450 m | Nacientes, no ríos grandes |
| Relieve | `USGS/SRTMGL1_003` + `ee.Terrain` | pendiente ≤25° (manantial) | 30 m | Ladera que deja aflorar el agua |
| Bofedal: planicie alta | SRTM + pendiente | 3000–5200 m + pendiente ≤5° | 30 m | Piso de bofedal altoandino (`UMBRALES` ajustables) |
| Bofedal: agua+verde | JRC `seasonality` ≥8 + MODIS NDVI seco ≥0,25 | ver `07` y `08` | 30–250 m | Verde en plena estación seca = humedad permanente |
| Alternativa fina | `ESA/WorldCover/v200` clase 90 (humedales) | `Map` | 10 m | Verifica candidatos 2020–2021 |

## Qué extrae este análisis (contenido avanzado incluido)

- **M9.1** Máscara combinada + **vectorización a polígonos** (`reduceToVectors`) + tabla priorizada por área.
- **M10.1** Criterios combinados bofedal (estricto vs. laxo) + áreas por clase + mapa.
- **M10.2** Estado anual del bofedal: serie NDVI de estación seca año a año (tendencia de degradación/recuperación).
- Contexto Bolivia: bofedales (turberas altoandinas) entre 3800–4800 m: pastoreo de camélidos, esponjas de agua de la cuenca. Manantiales de ladera en Valles (Cochabamba, Tarija, Chuquisaca) sostienen riego en seca. **Todo candidato es hipótesis**: se valida con `PUNTOS_LOCALES` y caminata con la comunidad.

## Limitaciones

- Proxy ≠ confirmación: un píxel verde y húmedo puede ser riego, no bofedal natural. Sin validación de campo no se reporta como hallazgo.
- Si el filtro estricto da 0 km², el notebook indica cómo relajar (`seasonality` 8→6, NDVI 0,25→0,20) y reejecutar.

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
| `01_manantiales_bofedales.ipynb` | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/AGRIdigital-Climbat/PlantillaMapa/blob/main/09_Manantiales_Bofedales/01_manantiales_bofedales.ipynb) |

> El botón solo funciona si el repositorio es **público**. En repo privado, Colab pide permiso de GitHub.
> Antes de ejecutar: limpia salidas si vas a hacer push (`Edit → Clear all outputs`).
