# Humedad de suelo: estado, percentiles y anomalía

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/AGRIdigital-Climbat/PlantillaMapa/blob/main/06_Humedad_Suelo/01_humedad_suelo.ipynb)

PROSUCO · CLIMBAT — análisis por municipio de Bolivia con Google Earth Engine desde Colab.

**Pregunta territorial:** ¿Cuánta agua disponible hay hoy en el suelo respecto a su historia?

## Satélites y datasets (IDs exactos de GEE)

| Variable | ID GEE | Banda(s) | Resolución | Período | Detalle para Bolivia |
|---|---|---|---|---|---|
| Humedad (primario) | `NASA/SMAP/SPL4SMGP/008` | `sm_rootzone` (m³/m³), `sm_rootzone_pctl` (percentil), `sm_surface_anomaly` | 9 km (remuestreado ~11 km) | 2015–actual | Asimilación modelo+satelital; zona radicular = la que beben los cultivos. `007` está deprecado (se cita, no se usa) |
| Humedad (alternativa) | `ECMWF/ERA5_LAND/DAILY_AGGR` | `volumetric_soil_water_layer_1..4` (m³/m³) | ~11 km | 1950–actual | 4 capas hasta ~3 m; serie larga para contexto pre-2015 |

## Qué extrae este análisis (contenido avanzado incluido)

- **M6.1** Serie mensual radicular + umbrales (seco < percentil 10–20).
- **M6.2** Mapas: percentil histórico, último estado y anomalía.
- Contexto Bolivia: señal accionable de riego en campaña (oct–abr). Percentil <10 = entre lo más seco del histórico → alerta temprana (complementa al SPI de `05`, que va rezagado 1–3 meses respecto al suelo).

## Limitaciones

- 9–11 km no resuelve parcelas: es lectura de **zona**, no de chacra. En topografía fuerte (Valles) el píxel mezcla ladera/fondo.
- SMAP falla bajo nieve/hielo y en salares saturados (Coipasa/Uyuni): se enmascara y declara.

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
| `01_humedad_suelo.ipynb` | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/AGRIdigital-Climbat/PlantillaMapa/blob/main/06_Humedad_Suelo/01_humedad_suelo.ipynb) |

> El botón solo funciona si el repositorio es **público**. En repo privado, Colab pide permiso de GitHub.
> Antes de ejecutar: limpia salidas si vas a hacer push (`Edit → Clear all outputs`).
