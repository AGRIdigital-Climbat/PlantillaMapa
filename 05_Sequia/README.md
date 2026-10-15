# Sequía: SPI-3, frecuencia y duración de eventos secos

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/AGRIdigital-Climbat/PlantillaMapa/blob/main/05_Sequia/01_sequia.ipynb)

PROSUCO · CLIMBAT — análisis por municipio de Bolivia con Google Earth Engine desde Colab.

**Pregunta territorial:** ¿Cuántos años fueron severamente secos, cuánto duraron y con qué intensidad?

## Satélites y datasets (IDs exactos de GEE)

| Variable | ID GEE | Banda(s) | Resolución | Período | Detalle para Bolivia |
|---|---|---|---|---|---|
| SPI-3 (primario, calculado) | `UCSB-CHG/CHIRPS/DAILY` agregada a mensual | `precipitation` | ~5 km | 1981–actual | SPI-3 = anomalía estandarizada de lluvia 3 meses; severo ≤ −1,5 (`UMBRALES["spi_severo"]`) |
| Sequía (alternativa) | `IDAHO_EPSCOR/TERRACLIMATE` | `pdsi` (escala **×0,01**), `soil` (×0,1) | ~4 km | 1958–actual | PDSI ya integra temperatura (balance hídrico), útil para comparar con SPI puro |

## Qué extrae este análisis (contenido avanzado incluido)

- **M5.1** `precipitacion_mensual()` por bloques + SPI-3 mensual, eventos severos, **duración** (máxima racha ≤ −1,5) e **intensidad** (SPI mínimo).
- **M5.2** Mapa de frecuencia de años secos por píxel (percentil local P33).
- Contexto Bolivia: sequías El Niño 1982–83, 1997–98, 2015–16 (la del Poopó), 2022–23 (la más cruda reciente en Altiplano/Valles/Chaco). Un "verano fallido" dic–mar con SPI-3 ≤ −1,5 justifica riego/cosecha de agua.

## Limitaciones

- SPI solo usa lluvia (no calor): en años cálidos el impacto real es peor de lo que dice el SPI; contrasta con PDSI y con humedad de suelo (`06`).
- CHIRPS inicia en 1981: sequías anteriores (ej. 1970s) quedan fuera; ERA5 puede extender el contexto.

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
| `01_sequia.ipynb` | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/AGRIdigital-Climbat/PlantillaMapa/blob/main/05_Sequia/01_sequia.ipynb) |

> El botón solo funciona si el repositorio es **público**. En repo privado, Colab pide permiso de GitHub.
> Antes de ejecutar: limpia salidas si vas a hacer push (`Edit → Clear all outputs`).
