# Agua superficial: ocurrencia, estacionalidad, recurrencia y cambio

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/AGRIdigital-Climbat/PlantillaMapa/blob/main/08_Agua_Superficial/01_agua_superficial.ipynb)

PROSUCO · CLIMBAT — análisis por municipio de Bolivia con Google Earth Engine desde Colab.

**Pregunta territorial:** ¿Dónde hay agua, cuántos meses al año, cada cuántos años repite y qué se perdió/ganó?

## Satélites y datasets (IDs exactos de GEE)

| Variable | ID GEE | Banda(s) | Resolución | Período | Detalle para Bolivia |
|---|---|---|---|---|---|
| Agua histórica (primario) | `JRC/GSW1_4/GlobalSurfaceWater` | `occurrence` (0–100 %), `seasonality` (0–12 meses), `recurrence` (%), `transition`, `change_abs`, `max_extent` | 30 m (Landsat) | **1984–2021** | El estándar mundial; `occurrence` = % del tiempo con agua (NO es conteo) |
| Agua anual | `JRC/GSW1_4/YearlyHistory` | clases 0–3 (sin dato/estacional/permanente) | 30 m | 1984–2021 por año | Serie anual de km² + comparación entre épocas |
| Agua mensual | `JRC/GSW1_4/MonthlyHistory` | clases mensuales | 30 m | 1984–2021 | Zoom temporal + control de cobertura válida ≥80 % |
| Agua reciente (complemento) | `COPERNICUS/S2_SR_HARMONIZED` | B3+B11 → **MNDWI** | 10–20 m | 2019–actual | Cubre el hueco post-2021 del JRC (celda avanzada incluida) |
| Lagos someros (referencia) | misión SWOT + `Hydrocron` NASA | altura/extensión | variable | 2022–actual | Caso Poopó del ejercicio Transcend; se cita como extensión opcional |

## Qué extrae este análisis (contenido avanzado incluido, nada desperdiciado)

- **M8.1** Ocurrencia + estacionalidad + recurrencia (las 3 dimensiones que suelen confundirse, con su interpretación separada).
- **M8.2** `change_abs` (2000–2021 vs 1984–1999) + serie anual de km² con agua.
- **Complemento avanzado heredado del ejercicio Poopó** (`01_long_term_surface_water_jrc.ipynb`): serie mensual Sentinel-2 MNDWI 2019→hoy **adaptada a tu `roi`** (máscara `SCL`, compuestos mensuales de mediana, umbral agua `MNDWI>0`, filtro ≥80 % cobertura) + gráfico combinado JRC+S2. El original usaba el dominio del lago; aquí corre sobre el GeoJSON del municipio.
- Contexto Bolivia: Titicaca (permanente), Poopó/Uru Uru (colapso 2015–16, el caso de estudio), bañados del Izozog, lagunas saladas, Pantanal norte. `occurrence` alta + `seasonality` 12 = permanente; `seasonality` 4–8 = estacional (lagunas de lluvia).

## Limitaciones

- JRC **termina en 2021** y no se puede extender con el mismo producto: lo reciente es S2/MNDWI (distinto sensor, valores no idénticos; se grafican juntos con advertencia).
- Landsat 30 m pierde ojos de agua <1 ha y bofedales pequeños: para eso está `09`.

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
| `01_agua_superficial.ipynb` | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/AGRIdigital-Climbat/PlantillaMapa/blob/main/08_Agua_Superficial/01_agua_superficial.ipynb) |

> El botón solo funciona si el repositorio es **público**. En repo privado, Colab pide permiso de GitHub.
> Antes de ejecutar: limpia salidas si vas a hacer push (`Edit → Clear all outputs`).
