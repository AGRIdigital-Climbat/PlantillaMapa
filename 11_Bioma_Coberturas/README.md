# Bioma y coberturas: estado actual de alta resolución

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/AGRIdigital-Climbat/PlantillaMapa/blob/main/11_Bioma_Coberturas/01_bioma_coberturas.ipynb)

PROSUCO · CLIMBAT — análisis por municipio de Bolivia con Google Earth Engine desde Colab.

**Pregunta territorial:** ¿Qué coberturas tiene hoy el municipio y a qué bioma pertenece?

## Satélites y datasets (IDs exactos de GEE)

| Variable | ID GEE | Banda(s) / clases | Resolución | Período | Detalle para Bolivia |
|---|---|---|---|---|---|
| Cobertura (primario) | `ESA/WorldCover/v200/2021` | `Map`: 10 árbol, 20 arbustal, 30 pastizal, **40 cultivos**, 50 construido, 60 suelo desnudo, 70 nieve/hielo, 80 agua, **90 humedales**, 95 manglar, 100 musgo/líquenes | 10 m (Sentinel-1+2) | 2021 (y `.../2020`) | La foto más nítida y gratuita del municipio |
| Cobertura dinámica (alternativa) | `GOOGLE/DYNAMICWORLD` | 9 probabilidades (incl. `crops`, `trees`, `flooded_vegetation`) | 10 m | 2015–actual | Serie casi semanal: detecta chaqueo/quema y rebrote |
| Serie histórica (contexto) | `MODIS/061/MCD12Q1` | `LC_Type1` anual | 500 m | 2001–actual | Para decir *de qué* cambió cada clase actual |

## Qué extrae este análisis (contenido avanzado incluido)

- **M12.1** Mapa 10 m + tabla de % por clase + % agrícola contextualizado.
- Contexto Bolivia (biomas que puede cruzar un municipio): Amazonía (10 bosque), Yungas (transición), Chiquitanía (bosque seco, fuego), Chaco (20/30), Puna altoandina (30/60), salares (60/80). La clase dominante define el carácter productivo; el % de cultivos dimensiona la agricultura; 90+80 localizan agua y humedales (puente con `08`/`09`).

## Limitaciones

- Foto 2020/2021: el chaqueo posterior no aparece; para cambios recientes usa DynamicWorld.
- WorldCover confunde mosaico cultivo/pastizal en Valles: valida con `10` y saber local.

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
| `01_bioma_coberturas.ipynb` | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/AGRIdigital-Climbat/PlantillaMapa/blob/main/11_Bioma_Coberturas/01_bioma_coberturas.ipynb) |

> El botón solo funciona si el repositorio es **público**. En repo privado, Colab pide permiso de GitHub.
> Antes de ejecutar: limpia salidas si vas a hacer push (`Edit → Clear all outputs`).
