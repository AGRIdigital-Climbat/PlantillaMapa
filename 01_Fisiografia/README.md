# Fisiografía: relieve, pendiente y drenaje del municipio

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/AGRIdigital-Climbat/PlantillaMapa/blob/main/01_Fisiografia/01_fisiografia.ipynb)

PROSUCO · CLIMBAT — análisis por municipio de Bolivia con Google Earth Engine desde Colab.

**Pregunta territorial:** ¿Dónde y cómo es el terreno? ¿Dónde se acumula el frío, qué es mecanizable y por dónde corre el agua?

## Satélites y datasets (IDs exactos de GEE)

| Variable | ID GEE | Banda(s) | Resolución | Cobertura | Detalle para Bolivia |
|---|---|---|---|---|---|
| Elevación (primario) | `USGS/SRTMGL1_003` | `elevation` | 30 m | global, vuelo 2000 | Base de pisos térmicos: Puna >3800 m, Valles 1500–3500 m, Llanos <500 m. Error vertical ±16 m |
| Elevación (alternativa) | `COPERNICUS/DEM/GLO30` | `DEM` | 30 m | 2011–2015 | Más reciente que SRTM; mejor en bordes de salar y dunas |
| Elevación (alternativa) | `NASA/NASADEM_HGT/001` | `elevation` | 30 m | reproceso SRTM+ICESat | Menos vacíos en Cordillera Oriental/Occidental |
| Pendiente/aspecto | derivados (`ee.Terrain.slope/aspect`) | — | 30 m | — | Mecanizable ≈ pendiente <8 %; laderas >25 % solo pastoreo/forestal |
| Acumulación de flujo (primario) | `WWF/HydroSHEDS/15ACC` | `b1` (nº celdas aguas arriba) | ~450 m | global | Define red hídrica y cabeceras (clave para M9 manantiales). Alternativa `WWF/HydroSHEDS/30ACC` (~1 km) |

## Qué extrae este análisis (contenido avanzado incluido)

- **M1.1** Mapa de elevación + pendiente + aspecto con `geemap` y leyendas.
- **M1.2** Restricciones del terreno: % del municipio por bandas de elevación, pendiente y orientación (tabla pandas).
- **M1.3** Red de drenaje por umbrales de acumulación + km de cauce por umbral y por altura (doble gráfico de barras horizontales lado a lado).
- Contexto Bolivia: el Altiplano es endorreico (TDPS: Titicaca–Desaguadero–Poopó); la Cordillera Oriental drena a la Amazonía y el sureste al Plata. Un municipio puede abarcar dos vertientes: verifica el divisor con el mapa de acumulación.

## Limitaciones

- SRTM es del año 2000: no registra movimientos de tierra, minería ni urbanización recientes.
- HydroSHEDS a 450 m subestima quebradas pequeñas de Valles: valida con saber local.

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
| `01_fisiografia.ipynb` | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/AGRIdigital-Climbat/PlantillaMapa/blob/main/01_Fisiografia/01_fisiografia.ipynb) |

> El botón solo funciona si el repositorio es **público**. En repo privado, Colab pide permiso de GitHub.
> Antes de ejecutar: limpia salidas si vas a hacer push (`Edit → Clear all outputs`).
