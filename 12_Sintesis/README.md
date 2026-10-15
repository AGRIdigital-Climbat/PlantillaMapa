# Síntesis: matriz de indicadores de una fila por municipio

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/AGRIdigital-Climbat/PlantillaMapa/blob/main/12_Sintesis/01_sintesis.ipynb)

PROSUCO · CLIMBAT — análisis por municipio de Bolivia con Google Earth Engine desde Colab.

**Pregunta territorial:** ¿Qué digo del municipio en una sola fila comparable con otros?

## Qué consolida (contenido avanzado incluido, nada desperdiciado)

- **Marco de 8 dimensiones** (celda incluida): ocurrencia, frecuencia, estacionalidad, recurrencia, temporalidad, duración, intensidad, tendencia — cada indicador de los módulos 01–11 viene etiquetado.
- **Inventario verificado de datasets** (celda incluida): CHIRPS 1981+ · ERA5-Land 1950+ · JRC 1984–2021 · MODIS NDVI 2000+ / MCD12Q1 2001+ · SMAP 2015+ · WorldCover 2020/21, con factores de escala (Kelvin−273,15; TERRACLIMATE ×0,1/×0,01; NDVI ×0,0001; LST ×0,02).
- **M13.1** Matriz de una fila: área, elevación media, lluvia anual, Tmin/Tmax, días de helada, años SPI severo, humedad percentil, NDVI pico y mes, km² agua, candidatos manantial/bofedal, km² agrícola y tendencia, % coberturas.
- **M13.2** Lectura ejecutiva narrativa automática con los números.
- **Variables extra recomendadas** (no en M1–M13, mismo patrón): ET `TERRACLIMATE aet/pet`, viento y radiación `ERA5_LAND`, nieve `MODIS/061/MOD10A1` (Cordillera), quemas `MODIS/061/MCD64A1` (Chiquitanía/Chaco), altimetría SWOT/Hydrocron (lagos).

## Cómo comparar municipios

Ejecuta cada `01_xx.ipynb` cambiando solo `RUTA_AOI` (y `ZONA["nombre"]`), copia la fila de `12` a una tabla común y ordena por riesgo (heladas, sequía) u oportunidad (agua, suelo,verdura). El GeoJSON de cada municipio es la única diferencia entre corridas.

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
| `01_sintesis.ipynb` | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/AGRIdigital-Climbat/PlantillaMapa/blob/main/12_Sintesis/01_sintesis.ipynb) |

> El botón solo funciona si el repositorio es **público**. En repo privado, Colab pide permiso de GitHub.
> Antes de ejecutar: limpia salidas si vas a hacer push (`Edit → Clear all outputs`).
