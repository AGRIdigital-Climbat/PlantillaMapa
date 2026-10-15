# PlantillaMapa — Análisis satelital por municipio de Bolivia

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/AGRIdigital-Climbat/PlantillaMapa/blob/main/12_Sintesis/01_sintesis.ipynb)

**PROSUCO · CLIMBAT** — diagnóstico territorial y agroclimático de cualquier municipio de Bolivia
con **Google Earth Engine (GEE)** desde **Google Colab**. Un tema = una carpeta = un notebook
autocontenido + su README con botón Colab.

> **Área de estudio = tu GeoJSON.** Los 12 notebooks usan por defecto el mismo AOI:
> `RUTA_AOI = "https://drive.google.com/file/d/11k-2-0MfG84QcYld7XY3r5XVMDHWtJ0N/view?usp=sharing"`
> (compartido "Cualquiera con el enlace"). El polígono **siempre manda**: al cargarse se
> recalculan `roi`, `bbox`, `centro` y `area_km2`. Preparado en QGIS como GeoJSON, EPSG:4326.

## Estructura (12 análisis)

| Carpeta | Análisis | Notebook | Colab |
|---|---|---|---|
| `01_Fisiografia/` | Relieve, pendiente, drenaje (SRTM + HydroSHEDS) | `01_fisiografia.ipynb` | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/AGRIdigital-Climbat/PlantillaMapa/blob/main/01_Fisiografia/01_fisiografia.ipynb) |
| `02_Precipitacion/` | Régimen y meses críticos (CHIRPS 1981+) | `01_precipitacion.ipynb` | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/AGRIdigital-Climbat/PlantillaMapa/blob/main/02_Precipitacion/01_precipitacion.ipynb) |
| `03_Temperatura/` | T. aire y suelo + tendencia (ERA5-Land + MODIS LST) | `01_temperatura.ipynb` | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/AGRIdigital-Climbat/PlantillaMapa/blob/main/03_Temperatura/01_temperatura.ipynb) |
| `04_Heladas/` | Frecuencia e intensidad (LST nocturna 1 km) | `01_heladas.ipynb` | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/AGRIdigital-Climbat/PlantillaMapa/blob/main/04_Heladas/01_heladas.ipynb) |
| `05_Sequia/` | SPI-3 y años severos (CHIRPS) | `01_sequia.ipynb` | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/AGRIdigital-Climbat/PlantillaMapa/blob/main/05_Sequia/01_sequia.ipynb) |
| `06_Humedad_Suelo/` | Estado y anomalía (SMAP 008) | `01_humedad_suelo.ipynb` | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/AGRIdigital-Climbat/PlantillaMapa/blob/main/06_Humedad_Suelo/01_humedad_suelo.ipynb) |
| `07_NDVI_Fenologia/` | Ciclo vegetativo vs. calendario (MODIS + S2) | `01_ndvi_fenologia.ipynb` | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/AGRIdigital-Climbat/PlantillaMapa/blob/main/07_NDVI_Fenologia/01_ndvi_fenologia.ipynb) |
| `08_Agua_Superficial/` | Ocurrencia y cambio (JRC 1984–2021 + S2) | `01_agua_superficial.ipynb` | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/AGRIdigital-Climbat/PlantillaMapa/blob/main/08_Agua_Superficial/01_agua_superficial.ipynb) |
| `09_Manantiales_Bofedales/` | Proxies de manantial y bofedal | `01_manantiales_bofedales.ipynb` | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/AGRIdigital-Climbat/PlantillaMapa/blob/main/09_Manantiales_Bofedales/01_manantiales_bofedales.ipynb) |
| `10_Superficie_Agricola/` | Serie y cambio agrícola (MCD12Q1 2001+) | `01_superficie_agricola.ipynb` | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/AGRIdigital-Climbat/PlantillaMapa/blob/main/10_Superficie_Agricola/01_superficie_agricola.ipynb) |
| `11_Bioma_Coberturas/` | Coberturas 10 m (WorldCover 2021) | `01_bioma_coberturas.ipynb` | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/AGRIdigital-Climbat/PlantillaMapa/blob/main/11_Bioma_Coberturas/01_bioma_coberturas.ipynb) |
| `12_Sintesis/` | Matriz de una fila por municipio | `01_sintesis.ipynb` | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/AGRIdigital-Climbat/PlantillaMapa/blob/main/12_Sintesis/01_sintesis.ipynb) |

Cada carpeta tiene su `README.md` con: pregunta territorial, tabla de satélites
(ID GEE exacto, resolución, período, detalle para Bolivia), salidas, limitaciones
y la guía de autenticación GEE + carga del GeoJSON + cambio de años/satélite.

## Cómo usar (resumen)

1. Abre cualquier notebook con su botón **Open in Colab**.
2. Ejecuta en orden: **Instalación → Autenticación GEE** (pon tu `EE_PROJECT`) →
   **CONFIGURACIÓN MAESTRA** (años, satélite, umbrales) → **AOI desde Drive** → módulo.
3. Solo edita la celda **CONFIGURACIÓN MAESTRA**. Para otro municipio cambia
   `RUTA_AOI` y `ZONA["nombre"]`; todo lo demás se recalcula solo.
4. Para cambiar de satélite: comenta la línea primaria y descomenta la alternativa
   (cada una indica resolución, período y para qué sirve).

## Convenciones

- Ventana analítica **1996-01-01 → 2026-12-31**; el año en curso se excluye de métricas.
- Todo número se cita con **período + dataset** (ej. `CHIRPS 1981–2025`).
- Cada indicador lleva su dimensión: ocurrencia, frecuencia, estacionalidad,
  recurrencia, temporalidad, duración, intensidad, tendencia.
- Antes del push: limpiar salidas (`Edit → Clear all outputs`).

## Licencia

Material público bajo [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/deed.es):
úsalo y adáptalo libremente citando a PROSUCO · CLIMBAT.
