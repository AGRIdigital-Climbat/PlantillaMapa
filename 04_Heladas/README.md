# Heladas: frecuencia, estacionalidad, duración e intensidad

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/AGRIdigital-Climbat/PlantillaMapa/blob/main/04_Heladas/01_heladas.ipynb)

PROSUCO · CLIMBAT — análisis por municipio de Bolivia con Google Earth Engine desde Colab.

**Pregunta territorial:** ¿Cuántas noches de helada caen por año, en qué meses y con qué intensidad?

## Satélites y datasets (IDs exactos de GEE)

| Variable | ID GEE | Banda(s) | Resolución | Período | Detalle para Bolivia |
|---|---|---|---|---|---|
| Helada (primario) | `MODIS/061/MOD11A1` LST nocturna (Terra diaria) | `LST_Night_1km` (×0,02 → K → °C) | 1 km | 2000–actual | Conteo de noches con LST ≤ 0 °C (y graves ≤ −2 °C). 1 km resuelve fondos de valle vs. ladera |
| Helada (alternativa) | `ECMWF/ERA5_LAND/DAILY_AGGR` | `temperature_2m_min` (K → °C) | ~11 km | 1950–actual | Serie larga 1950+: ideal para frecuencia histórica donde MODIS no llega |
| Helada grave | umbral `UMBRALES["helada_grave_c"] = −2,0 °C` | — | — | — | Daño severo en papa/quinua en floración |

## Qué extrae este análisis (contenido avanzado incluido)

- **M4.1 (BIS)** Días de helada por año con LST nocturna + serie y estacionalidad mensual.
- **M4.2** Mapa de heladas con **selector de año**.
- Métrica accionable CLIMBAT: nº noches de alerta por temporada (mallas, riego por aspersión, estaciones IoT).
- Contexto Bolivia: Altiplano 100–200 noches/año (may–ago concentrado, pero heladas tempranas mar–abr y tardías sep–oct son las que matan cultivos); Valles 10–60; Llanos casi 0 salvo surazo. La LST nocturna en cielo despejado altiplánico es el mejor proxy satelital disponible.

## Limitaciones

- LST ≠ aire a 2 m: en noches despejadas el suelo enfría más que el aire (sobrestima leve) y con nubes no hay dato (subestima en húmeda). Se declara en el notebook.
- MODIS 1 km mezcla salar/cultivo/agua en bordes (ej. lago Titicaca modera heladas ribereñas y el píxel lo promedia).

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
| `01_heladas.ipynb` | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/AGRIdigital-Climbat/PlantillaMapa/blob/main/04_Heladas/01_heladas.ipynb) |

> El botón solo funciona si el repositorio es **público**. En repo privado, Colab pide permiso de GitHub.
> Antes de ejecutar: limpia salidas si vas a hacer push (`Edit → Clear all outputs`).
