# 📊 Informe de Desempeño Comercial Interactivo
### Evaluación del Módulo #8 — Fundamentos de Business Intelligence

---
## 👤 Información del Estudiante

- **Nombre:** Germán Riveros Sepúlveda  
- **Curso:** RTD-25-01-01-0002-1  
- **Jornada:** Vespertina  

---

## 📌 Descripción del proyecto

Este proyecto fue desarrollado como parte del módulo de **Fundamentos de Business Intelligence** en Alkemy. El objetivo fue construir un informe dinámico en **Power BI Desktop** que permita a la Dirección Comercial visualizar el desempeño de ventas versus presupuesto, segmentado por región, producto y período.

El informe integra múltiples fuentes de datos, aplica transformaciones con Power Query, modela las relaciones entre tablas y expone KPIs clave mediante medidas DAX y visualizaciones interactivas.

---

## 🗂️ Estructura del repositorio

```
📦 ABP-8/
├── 📄 ABP-8.pbix                  # Informe Power BI con dashboard completo
├── 📊 Ventas_Comerciales.xlsx     # Datos de ventas por producto y región
├── 📄 Metas_Ventas.csv            # Metas de ventas por región y período
└── 📋 README.md                   # Este documento
```

---

## 📂 Fuentes de datos

### `Ventas_Comerciales.xlsx` — 1.684 registros de transacciones

| Hoja | Descripción | Campos clave |
|---|---|---|
| **Ventas** | Transacciones comerciales (2023–2024) | ID_Venta, Fecha, Región, Vendedor, ID_Producto, Cantidad, Precio_Unitario, Descuento_Pct |
| **Productos** | Catálogo de 13 productos | ID_Producto, Producto, Categoría, Precio_Base |
| **Regiones** | Información de las 5 zonas | Región, Zona, Gerente_Regional |

### `Metas_Ventas.csv` — 120 registros de presupuesto

| Campo | Descripción |
|---|---|
| Año | 2023 y 2024 |
| Mes | Mes del objetivo (1–12) |
| Región | Norte, Sur, Centro, Oriente, Occidente |
| Meta_Ventas | Presupuesto asignado en pesos |

---

## 🧩 Modelo de datos

El modelo sigue un esquema **estrella** con las siguientes relaciones:

```
Metas_Ventas ──────────────┐
                            │  (Región + Año + Mes)
Ventas ──── ID_Producto ──► Productos
       ──── Región      ──► Regiones
       ──── Fecha       ──► Tabla Calendario (generada en DAX)
```

- Relación **uno a muchos** entre `Productos` → `Ventas`
- Relación **uno a muchos** entre `Regiones` → `Ventas`
- Tabla `Calendario` generada con DAX para soporte de inteligencia de tiempo

---

## ⚙️ Transformaciones aplicadas (Power Query)

- Eliminación de registros nulos o incompletos
- Extracción de columnas **Año** y **Mes** a partir de la columna `Fecha`
- Unificación de tipos de datos entre tablas
- Normalización de nombres de región para garantizar la relación con `Metas_Ventas`
- Conexión y carga de fuentes heterogéneas (Excel + CSV)

---

## 📐 Medidas DAX implementadas

```dax
-- Ventas netas aplicando descuento
Ventas Netas =
SUMX(
    Ventas,
    Ventas[Cantidad] * Ventas[Precio_Unitario] * (1 - Ventas[Descuento_Pct] / 100)
)

-- Total de metas presupuestadas
Total Metas = SUM(Metas_Ventas[Meta_Ventas])

-- Porcentaje de cumplimiento de objetivo
% Cumplimiento = DIVIDE([Ventas Netas], [Total Metas], 0)

-- Variación respecto al año anterior
Ventas Año Anterior =
CALCULATE([Ventas Netas], SAMEPERIODLASTYEAR(Calendario[Fecha]))

-- Variación porcentual interanual
Variación YoY % =
DIVIDE([Ventas Netas] - [Ventas Año Anterior], [Ventas Año Anterior], 0)
```

---

## 📈 Resultados del análisis

### KPIs globales (2023–2024)

| Métrica | Valor |
|---|---|
| 💰 Ventas Netas Totales | $2.212.436.800 |
| 🎯 Meta Total | $2.495.900.000 |
| ✅ Cumplimiento Global | **88,6%** |
| 📦 Registros de venta | 1.684 transacciones |
| 👥 Vendedores activos | 10 |
| 🗂️ Categorías de producto | Tecnología, Software, Mobiliario, Insumos |

### Evolución anual de ventas

| Año | Ventas Netas |
|---|---|
| 2023 | $1.082.975.100 |
| 2024 | $1.129.461.700 |
| **Crecimiento YoY** | **+4,3%** |

### Cumplimiento por región

| Región | Ventas Netas | Meta | Cumplimiento |
|---|---|---|---|
| 🥇 Occidente | $406.156.250 | $455.300.000 | **89,2%** |
| 🥈 Norte | $374.402.050 | $421.300.000 | **88,9%** |
| 🥉 Oriente | $542.963.550 | $611.600.000 | **88,8%** |
| Sur | $411.577.950 | $466.300.000 | 88,3% |
| Centro | $477.337.000 | $541.400.000 | 88,2% |

### Top 5 productos por ventas netas

| # | Producto | Ventas Netas |
|---|---|---|
| 1 | Software ERP | $1.081.247.550 |
| 2 | Laptop Pro 15 | $407.306.650 |
| 3 | Escritorio Standing | $199.997.700 |
| 4 | Monitor UHD 27 | $165.286.850 |
| 5 | Silla Ejecutiva | $130.799.400 |

> **Insight clave:** El producto **Software ERP** representa el **48,9%** del total de ventas netas, siendo el principal driver comercial del período analizado.

---

## 🖥️ Estructura del dashboard (Power BI)

El informe `.pbix` está organizado en **3 páginas funcionales** más una portada de navegación:

### Página 1 — Portada / Navegación
Portada con acceso directo a las secciones del informe.

### Página 2 — Informe de Desempeño
Visión ejecutiva del cumplimiento de ventas con:
- 🃏 **Tarjetas KPI**: Ventas Netas, % Cumplimiento
- 📊 **Gráfico de columnas**: Ventas vs. Metas por región
- 🌡️ **Gauge**: Nivel de cumplimiento global
- 🔽 **Segmentadores**: por período y región

### Página 3 — Análisis Comercial
Desglose por producto y categoría con:
- 🍩 **Gráfico de dona**: Participación por categoría
- 📊 **Gráfico de barras**: Ranking de ventas por producto
- 🗺️ **Treemap**: Distribución de ventas
- 📋 **Tabla pivot**: Detalle cruzado producto × región
- 🔽 **Segmentadores**: por producto y región

### Página 4 — Tendencia y Desempeño
Análisis temporal con:
- 📈 **Gráficos de líneas**: Evolución mensual de ventas y tendencia YoY
- 📋 **Tablas pivot**: Comparativo por período
- 🔽 **Segmentadores**: por año y mes

---

## 🛠️ Tecnologías utilizadas

| Herramienta | Uso |
|---|---|
| **Power BI Desktop** | Desarrollo del informe completo |
| **Power Query (M)** | Transformación y limpieza de datos |
| **DAX** | Medidas calculadas y KPIs |
| **Excel (.xlsx)** | Fuente de datos de ventas |
| **CSV** | Fuente de datos de metas |

---

## 📚 Referencias

- [Documentación oficial Power BI](https://learn.microsoft.com/es-es/power-bi/)
- [Repositorio de funciones DAX](https://dax.guide/)
- [Documentación Power Query](https://learn.microsoft.com/en-us/power-query/)
- [Power BI Community](https://community.fabric.microsoft.com/)

---

*Proyecto desarrollado en el marco del módulo #8 — Fundamentos de Business Intelligence · Alkemy · 2024*