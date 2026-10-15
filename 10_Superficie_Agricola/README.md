# Superficie agrícola en el tiempo: serie, frecuencia y cambio

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/AGRIdigital-Climbat/PlantillaMapa/blob/main/10_Superficie_Agricola/01_superficie_agricola.ipynb)

PROSUCO · CLIMBAT — análisis por municipio de Bolivia con Google Earth Engine desde Colab.

**Pregunta territorial:** ¿Cuánta tierra se cultiva, dónde siempre y cómo cambió en 25 años?

## Satélites y datasets (IDs exactos de GEE)

| Variable | ID GEE | Banda(s) / clase | Resolución | Período | Detalle para Bolivia |
|---|---|---|---|---|---|
| Agricultura (primario) | `MODIS/061/MCD12Q1` | `LC_Type1`: **12** cultivos, **14** mosaico cultivo/natural | 500 m anual | **2001–actual** | Único con serie anual larga; 1996–2000 se marca *sin dato*, no se rellena |
| Cultivo fino (alternativa) | `GOOGLE/DYNAMICWORLD` | prob. clase `crops` (10 m, casi semanal) | 10 m | 2015–actual | Parcela a parcela desde 2015; ideal para validar MCD12Q1 |
| Cultivo actual (alternativa) | `ESA/WorldCover/v200` | clase 40 (cultivos) | 10 m | 2020–2021 | Foto fija de alta resolución |

## Qué extrae este análisis (contenido avanzado incluido)

- **M11.1** Serie km² agrícolas por año (2001→, con hueco 1996–2000 declarado) + % del AOI + tendencia por década.
- **M11.2** Mapa de **frecuencia de cultivo** (% de años con cultivo por píxel) + mapa de **cambio** (último vs. primer año).
- Contexto Bolivia: expansión de frontera en Santa Cruz (soya), parcelario minifundista altiplánico (papa/quinua/haba) que MODIS 500 m subestima, mosaicos de Valles. Pendiente positiva = expansión; negativa = abandono, urbanización o minería.

## Limitaciones

- 500 m no ve chacras <25 ha (casi todo el Altiplano): la serie es **tendencia de zona**, el área exacta se calibra con DynamicWorld/S2.
- MCD12Q1 confunde pastizal con mosaico en Puna: contrasta con NDVI (`07`) y WorldCover (`11`).

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
| `01_superficie_agricola.ipynb` | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/AGRIdigital-Climbat/PlantillaMapa/blob/main/10_Superficie_Agricola/01_superficie_agricola.ipynb) |

> El botón solo funciona si el repositorio es **público**. En repo privado, Colab pide permiso de GitHub.
> Antes de ejecutar: limpia salidas si vas a hacer push (`Edit → Clear all outputs`).
