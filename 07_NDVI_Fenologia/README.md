# NDVI y fenología: ciclo vegetativo vs. calendario agrícola

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/AGRIdigital-Climbat/PlantillaMapa/blob/main/07_NDVI_Fenologia/01_ndvi_fenologia.ipynb)

PROSUCO · CLIMBAT — análisis por municipio de Bolivia con Google Earth Engine desde Colab.

**Pregunta territorial:** ¿Cuándo reverdece el municipio y calza con siembra/cosecha local?

## Satélites y datasets (IDs exactos de GEE)

| Variable | ID GEE | Banda(s) / índice | Resolución | Período | Detalle para Bolivia |
|---|---|---|---|---|---|
| NDVI serie (M7.1) | `COPERNICUS/S2_SR_HARMONIZED` | B4 (rojo) + B8 (NIR) → `NDVI=(B8−B4)/(B8+B4)` | 10 m | 2017–actual | Parcela a parcela; máscara de nubes `SCL`. Serie + curva estacional vs. `CALENDARIO` |
| NDVI estacional (M7.2) | `LANDSAT/LC08/C02/T1_L2` (+ `LC09`) | `SR_B2–SR_B7` → `NDVI=(NIR−Red)/(NIR+Red)` | 30 m | 2013–actual | Mosaico mediana seca (may–sep, `CLOUD_COVER<30`) vs. húmeda (nov–abr, `<60`) + **ΔNDVI**. Adapta Day4 Sesión 2 Transcend |
| NDVI serie larga (alternativa) | `MODIS/061/MOD13Q1` | `NDVI` (factor **×0,0001**), cada 16 días | 250 m | 2000–actual | Fenología 25 años; también trae EVI. Útil si S2 tiene muchos huecos por nube |

## Qué extrae este análisis (solo NDVI, sin entrenamiento)

- **M7.1** Serie NDVI Sentinel-2 + curva estacional vs. `CALENDARIO` (siembra 11 / cosecha 4 por defecto altiplánico; ajustar a doble campaña en Llanos).
- **M7.2** Comparación estacional Landsat del **mismo año agrícola Y-1/Y** (`Y = ANIOS_COMPLETOS[-1]`): mosaico seca vs. mosaico húmeda en color natural (RGB) y en NDVI lado a lado (`split_map`), bandas con sufijo `_seca/_humeda` y **mapa ΔNDVI = húmeda − seca** (verde = reverdece en húmeda: cultivos, pastizal húmedo; marrón/blanco = sin cambio). Adapta `02_building_the_classification_input_es` (Day4 Sesión 2) **sin** muestras ni Random Forest.
- **Temporalidad:** la húmeda (nov–abr) precede a la seca (may–sep) del mismo ciclo para que ambos mosaicos muestren el mismo cultivo. Todo número se cita con **período + dataset** (ej. `LANDSAT LC08/LC09 C02 T1_L2 2023–2024`).
- Contexto Bolivia: Altiplano pico verde feb–mar; Valles ene–mar; Llanos/Amazonía verde casi todo el año con caída en invierno seco (jun–ago). Si el NDVI sube **antes** de la siembra hay humedad residual; si sube **después**, siembra tardía o dependencia de riego.

## Limitaciones

- Nubes dic–mar agujerean la serie en Yungas/Chapare/Amazonía: el notebook usa compuestos y declara huecos, no los inventa.
- NDVI satura en bosque denso (>0,8): para Amazonía/Chiquitanía el EVI discrimina mejor (se indica cómo calcularlo).

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
| `01_ndvi_fenologia.ipynb` | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/AGRIdigital-Climbat/PlantillaMapa/blob/main/07_NDVI_Fenologia/01_ndvi_fenologia.ipynb) |

> El botón solo funciona si el repositorio es **público**. En repo privado, Colab pide permiso de GitHub.
> Antes de ejecutar: limpia salidas si vas a hacer push (`Edit → Clear all outputs`).
