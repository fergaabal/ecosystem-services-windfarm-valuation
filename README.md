# Valoración de servicios ecosistémicos — Parque Eólico Paxareiras II

Flujo de trabajo reproducible que **integra GIS + Python + valoración económica de servicios ecosistémicos** sobre un expediente eólico real de Galicia, con un dashboard web autónomo para comunicar los resultados.

> **Alcance.** Pieza de portfolio técnico. Las cifras económicas son una **aproximación de primer orden** (contabilidad de capital natural de línea base por transferencia de valores unitarios): ilustran el método y el orden de magnitud, **no** constituyen una valoración de impacto apta para fundamentar una decisión de Evaluación de Impacto Ambiental (EIA). Ver [Limitaciones](#limitaciones).

---

## Por qué este proyecto

La Ley 2/2024 de Galicia incorpora la valoración de servicios ecosistémicos en los procedimientos de EIA, pero su Art. 10.3 habilita unas directrices metodológicas que **la Xunta aún no ha publicado**. En ese vacío, este trabajo muestra cómo abordar el problema combinando:

- **GIS / geoprocesamiento** — geometría oficial en UTM, recortes, distancias a espacios protegidos.
- **Python científico** — GeoPandas, Shapely, pandas, Matplotlib.
- **Economía ambiental** — CICES v5.1, benefit transfer, Valor Actual Neto (VAN).

Es una combinación poco común que responde directamente a una necesidad regulatoria emergente en Galicia.

---

## Caso de estudio

| | |
|---|---|
| **Expediente** | IN661A 2011/8 (NT) |
| **Localización** | Dumbría, A Coruña (Costa da Morte) |
| **Potencia** | 26,40 MW — 5 aerogeneradores (3×5,0 + 2×5,7 MW, Nordex N163) |
| **Línea de evacuación** | 6,65 km (LAT 66 kV) |
| **Superficie poligonal** | 909,6 ha |
| **Área de influencia** | 7.471,7 ha (buffer 5 km analizado) |
| **Red Natura 2000** | 1.575 m de distancia mínima, sin intersección |
| **SRC** | UTM Fuso 29N · ETRS89 (EPSG:25829) |

Datos a partir de fuentes públicas (ver [Fuentes](#fuentes-de-datos)).

---

## Resultados (escenario central)

**Línea base — área de influencia (5 km):**

- Valor anual de servicios ecosistémicos: **6.347.650 €/año**
- **VAN a 25 años (tasa 3 %): 110,5 M€**

**Poligonal del parque (proxy de superficie, 909,6 ha):**

- Valor anual: **753.487 €/año**
- VAN a 25 años (3 %): **13,1 M€**

> ⚠️ **Las dos cifras no se suman.** La poligonal está *dentro* del área de influencia: son dos lecturas del mismo territorio a distinta escala. Y ninguna es el impacto neto del parque — la huella física, la fragmentación, la colisión de avifauna, el paisaje y el contrapeso del beneficio climático de la generación renovable se abordan en piezas posteriores.

---

## Contenido del repositorio

```
.
├── README.md
├── memoria/
│   └── dashboard.html                      # dashboard autónomo (1 archivo)
├── data/                                   # shapefiles 
└── notebook/                               # se generan gráficos al ejecutar el notebook
    ├── paxareiras_ii_valoracion_se.ipynb   
   
```

---

## Cómo ejecutarlo

**1. Entorno**

```bash
pip install geopandas shapely pandas numpy matplotlib contextily openpyxl
```

**2. Datos**

Descarga los shapefiles desde sus fuentes (ver abajo) y colócalos en `data/`:

- CORINE Land Cover 2018 recortado a la zona — p. ej. `data/CLC2018_ZONA_CEE_DUMBRIA.shp`
- Red Natura 2000 de la zona — p. ej. `data/ZEC_CEE-DUMBRIA.shp`

El notebook detecta los archivos automáticamente; si el nombre difiere, ajusta `RUTA_CORINE` y `RUTA_NATURA` en las celdas correspondientes.

**3. Análisis**

Abre `paxareiras_ii_valoracion_se.ipynb` y ejecútalo de principio a fin. Genera la tabla de valoración, el VAN, el análisis de sensibilidad, el Excel y la figura.

**4. Dashboard**

Abre `dashboard/index.html` en el navegador (doble clic). No necesita servidor ni dependencias de build. Para publicarlo en **GitHub Pages**: *Settings → Pages → Deploy from branch*, y quedará servido en `https://<usuario>.github.io/<repo>/dashboard/`.

---

## Metodología

1. **Definición espacial** — poligonal, aerogeneradores y línea de evacuación a partir de coordenadas oficiales; buffer de influencia de 5 km.
2. **Usos del suelo** — recorte de CORINE Land Cover 2018 al área de influencia y cálculo de superficies por clase.
3. **Contexto de protección** — distancia y posible intersección con Red Natura 2000.
4. **Asignación de servicios** — correspondencia clase CORINE → hábitat (marco CICES v5.1); superficies artificiales valoradas a 0 (supuesto conservador).
5. **Valoración monetaria** — *benefit transfer* con valores unitarios (€/ha/año) en tres escenarios (mín / central / máx).
6. **VAN** — horizonte de 25 años, tasa de descuento del 3 %, con análisis de sensibilidad a tasa (3/5/7 %) y horizonte (25/30 años).

---

## Limitaciones

Declarar estas limitaciones forma parte del rigor del trabajo, no lo debilita.

- **Vacío normativo.** Sin directrices oficiales (Art. 10.3, Ley 2/2024), la valoración descansa en juicio técnico del analista.
- **Línea base, no impacto.** Se valora el *stock* de servicios del territorio, no el *cambio marginal* atribuible al parque.
- **Benefit transfer básico.** Transferencia de valor unitario por clase (el escalón de menor precisión; error documentado del 20–100 %).
- **Encaje de sitio.** Las fuentes son agregados globales o de zonas áridas peninsulares, distintas de la Galicia atlántica húmeda.
- **Valores = síntesis del analista**, informada por la literatura, no extracciones literales.
- **Resolución de CORINE** (unidad mínima 25 ha) gruesa para la poligonal; en Galicia, las clases 312/313 son mayoritariamente plantaciones (pino/eucalipto), no bosque natural, lo que tiende a sobrevalorar.
- **Descuento.** Aplicar el horizonte financiero del parque a un stock de capital natural es discutible; el debate ambiental favorece tasas bajas y decrecientes.

---

## Próximos pasos

- [ ] **Pieza 2 — Fragmentación de hábitats** y modelado de las vías de impacto reales.
- [ ] Sustituir CORINE por Hábitats de Interese Comunitario (Anexo I, Dir. 92/43/CEE) y corregir plantación vs. bosque natural.
- [ ] Desagregación por servicio finalista CICES (control de doble conteo).
- [ ] Propagación de incertidumbre (Monte Carlo) sobre los valores unitarios.
- [ ] Reindexación temporal (IPC) de los valores.

---

## Fuentes de datos

- **Usos del suelo** — CORINE Land Cover 2018 · IGN / Copernicus · [Centro de Descargas del CNIG](https://centrodedescargas.cnig.es) (serie 02113)
- **Coordenadas** — Acordo de Información Pública, Xunta de Galicia, octubre 2025 (CVE: bWmKo5yl53t5)
- **Espacios protegidos** — Red Natura 2000 (ES1110005 Costa da Morte; ES1110008 Carnota-Monte Pindo)

## Referencias

- Costanza, R., de Groot, R., Sutton, P., et al. (2014). *Changes in the global value of ecosystem services.* Global Environmental Change, 26, 152–158.
- de Groot, R., Brander, L., van der Ploeg, S., et al. (2012). *Global estimates of the value of ecosystems and their services in monetary units.* Ecosystem Services, 1(1), 50–61.
- Quintas-Soriano, C., Castro, A.J., Castro, H., García-Llorente, M. (2016). *Impacts of land use change on ecosystem services and implications for human well-being in Spanish drylands.* Land Use Policy, 54, 534–548.
- Haines-Young, R. & Potschin, M. (2018). *CICES v5.1: Guidance on the application of the revised structure.*

---

## Autor

**Fernando García Abal** — Ingeniero Agrónomo / Ingeniero de Montes · Galicia

## Licencia

Código y notebook bajo licencia MIT (sugerida — añade un archivo `LICENSE` si procede). Los datos de origen conservan las licencias de sus respectivas fuentes.
