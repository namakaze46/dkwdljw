# Precio del m² por UPZ · Bogotá D.C.

Mapa interactivo (choropleth) con el **avalúo catastral por m² de la vivienda en propiedad
horizontal (PH)** en las **112 UPZ** de Bogotá, construido a partir del dato **oficial** de
Catastro Bogotá (vigencia 2026).

**Abrir:** `mapa-precio-m2-por-upz-bogota.html` (doble clic; no requiere servidor).

La escala del mapa tiene **7 clases**: 6 cortes por cuantiles entre las UPZ con dato y una
**clase alta destacada** para los valores **≥ $4.000.000/m²**.

---

## 1. Avalúo catastral de propiedad horizontal (oficial)

| Componente | Fuente | Detalle |
|---|---|---|
| Valor $/m² | **UAECD – Catastro Bogotá** | *Mediana del avalúo catastral por m² de predios en propiedad horizontal por manzana* (capa `catastro/medavalcatphmz`). Vigencias 2017–2026; se usa el dato más reciente de cada manzana. |
| Geometría | **IDECA – Catastro Bogotá** | *Unidad de Planeamiento Zonal* (`ordenamientoterritorial/unidadplaneamientozonal`), 112 polígonos. |
| Contexto de mercado | **Aptología** (CC BY 4.0) | Precio **de oferta** de apartamentos por localidad, datos al 18/10/2026. Solo se muestra como referencia en la ficha de cada UPZ. |

Servicios consultados el **24/10/2026**:
- https://serviciosgis.catastrobogota.gov.co/arcgis/rest/services/catastro/medavalcatphmz/MapServer/0
- https://serviciosgis.catastrobogota.gov.co/arcgis/rest/services/ordenamientoterritorial/unidadplaneamientozonal/MapServer/0

### Metodología

1. Se descargaron las **141.225** filas del servicio de avalúo (todas las vigencias)
   y se conservó, por manzana, el registro más reciente → **15.816 manzanas** con dato.
2. La manzana (`MANCODIGO`, 9 dígitos) se asigna a su **sector catastral** (`SCACODIGO`, primeros 6 dígitos).
3. Cada uno de los **1.230 sectores** se asigna a una UPZ por su centroide
   (138 sectores de borde por proximidad al centroide de la UPZ).
4. El valor de la UPZ es la **mediana ponderada** de los valores de sus manzanas,
   ponderando por el **número de predios PH** de cada manzana (evita que una
   manzana con 3 predios pese igual que una con 300).

### Resultados

- **111 de 112 UPZ** con dato. *San Isidro – Patios* (Chapinero) no registra
  predios PH con avalúo.
- Mínimo **$1.374.400/m²** (Alfonso López, Usme) · mediana **$2.667.000/m²** ·
  máximo **$7.753.600/m²** (Aeropuerto El Dorado, solo 143 predios: muestra reducida).

### Clase alta: ≥ $4.000.000/m²

Por encima del umbral quedan **10 UPZ** (el 9 % de las que tienen dato). El corte es fijo,
no por cuantiles, para que el grupo de precios más altos no quede diluido en una sola clase:

| UPZ | Localidad | $/m² | Predios PH |
|---|---|---:|---:|
| Aeropuerto El Dorado | Fontibón | 7.753.600 | 143 |
| Chico Lago | Chapinero | 5.305.500 | 83.175 |
| Pardo Rubio | Chapinero | 4.567.700 | 31.150 |
| Puente Aranda | Puente Aranda | 4.354.900 | 7.975 |
| La Esmeralda | Teusaquillo | 4.195.200 | 17.780 |
| La Alhambra | Suba | 4.142.200 | 45.841 |
| Chapinero | Chapinero | 4.114.200 | 29.408 |
| Quinta Paredes | Teusaquillo | 4.106.500 | 9.479 |
| Teusaquillo | Teusaquillo | 4.055.900 | 11.610 |
| Galerías | Teusaquillo | 4.020.000 | 20.499 |

> **Alcance:** esta capa cubre **solo predios en propiedad horizontal** (apartamentos y
> similares). **No incluye** casas unifamiliares, multifamiliares no PH, lotes ni bodegas.

---

## Cómo leer el mapa

- **Avalúo (PH):** valor oficial, base del impuesto predial; suele estar **por debajo**
  del precio de oferta del mercado.
- **Escala:** 6 clases por cuantiles (cada color agrupa ~1/6 de las UPZ con dato) más una
  **clase alta fija para ≥ $4.000.000/m²**, con el tono más oscuro. La leyenda muestra
  cuántas UPZ hay en cada clase; el gris es «sin dato».
- **Búsqueda:** filtra por nombre de UPZ o de localidad (ignora tildes).
- **Zoom ≥ 13:** aparecen las etiquetas de las UPZ.
- **Clic:** abre la ficha, que incluye la clase de la escala en que cae la UPZ; el botón
  «Ver tabla completa» lista las 112 UPZ.

## Archivos

```
mapa-precio-m2-por-upz-bogota.html   mapa interactivo autocontenido (datos y Leaflet embebidos)
data/upz-precio-m2-bogota.geojson    polígonos + atributos para SIG o web
data/tabla-precio-m2-upz-bogota.csv  tabla plana (abre en Excel)
data/precio-m2-localidades-bogota-2026.csv  oferta por localidad (Aptología)
data/upz-bogota.geojson              geometría UPZ original
data/manzanas-ph.jsonl               cache crudo del servicio de avalúos
data/sectores-bogota.geojson         sectores catastrales (llave manzana → UPZ)
build/fetch_data.py                  descarga de UPZ + avalúos
build/fetch_sectores.py              descarga de sectores catastrales
build/build_map.py                   agregación y generación del HTML
build/geo.py                         utilidades geométricas compartidas
build/template.html                  plantilla de la interfaz
```

Para regenerar el mapa (no requiere volver a descargar nada):

```powershell
python build/build_map.py
```

Para reconstruir los datos desde las fuentes:

```powershell
python build/fetch_data.py
python build/fetch_sectores.py
python build/build_map.py
```

## Limitaciones

- El avalúo catastral **no es precio de venta**: es un valor oficial de referencia,
  actualizado por ciclos (cada manzana conserva su última vigencia, 2017–2026).
- La agregación por sector catastral supone que el sector no cruza límites de UPZ
  (válido en la práctica; 11 % de los sectores se asignaron por proximidad).
- UPZ con pocos predios PH (p. ej. Aeropuerto El Dorado, 143) tienen alta incertidumbre;
  la ficha lo advierte.
- El umbral de $4.000.000/m² es una **convención de lectura**, elegida para separar el
  extremo alto de la distribución; no tiene significado normativo.

## Cambios

- **oct. 2026** — Se retiró la capa de *oferta de casas* de metrocuadrado.com (precios de
  oferta, muestra no aleatoria) y se añadió la clase **≥ $4.000.000/m²**. El mapa quedó con
  una sola métrica (avalúo oficial). Los scripts `build/fetch_metrocuadrado.py` y
  `build/build_casas.py` se conservan como referencia, pero ya no alimentan el mapa.

## Otras fuentes revisadas

- **Visor Inmobiliario de Bogotá** (Catastro + Finca Raíz, Cien Cuadras, Properati):
  tiene precios de oferta por m², pero solo consulta interactiva (WMS), sin descarga masiva.
- **SDHT – Indicadores Comerciales de Vivienda** (datos.gov.co, `xwtp-x7qc`):
  resumen de vivienda nueva por localidad y UPZ; el recurso devuelve **HTTP 403**, sin acceso.
- **DANE – IPVN**: índices de precios (variaciones), no niveles de precio por UPZ.
- **metrocuadrado.com /preciosm2/**: página de índices, pero `robots.txt` la bloquea;
  se usaron las páginas de resultados, que sí están permitidas.
