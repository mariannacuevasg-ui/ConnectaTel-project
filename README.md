# Análisis ConnectaTel — Segmentación y Comportamiento de Clientes

Proyecto de análisis de datos desarrollado para ConnectaTel, una empresa de telecomunicaciones en Latinoamérica. El objetivo del proyecto es analizar la calidad y el comportamiento de los datos de clientes para identificar patrones y generar insights relevantes para el negocio.

El análisis incluye la evaluación de la calidad de los datos, su limpieza y preparación, así como la segmentación de clientes por edad y nivel de uso. A partir de los resultados obtenidos, se desarrolla un análisis ejecutivo orientado a transformar los datos en recomendaciones accionables para optimizar la oferta de planes y fortalecer la retención de clientes.

Este proyecto fue realizado como parte del Bootcamp de Data Analytics de TripleTen, con fines educativos y prácticos, aplicando los conocimientos y habilidades adquiridos durante el programa a un escenario de negocio simulado.

## 🎯 Objetivo del proyecto

Como analista de datos, el objetivo es evaluar el **comportamiento de los clientes** de ConnectaTel para responder preguntas de negocio como:

- ¿Qué problemas de calidad tenían los datos originales y cómo se resolvieron?
- ¿Qué segmentos de clientes existen según su edad y nivel de uso, y cómo se comportan?
- ¿Qué segmentos son más valiosos para el negocio?
- ¿Existen patrones de uso extremo (outliers) y qué implican?
- ¿La oferta actual de planes (Básico/Premium) responde al comportamiento real de uso?
- ¿Qué recomendaciones se pueden hacer para mejorar la oferta de planes y la retención de clientes?

El análisis cubre información registrada **hasta el año 2024**.

## 📂 Datasets utilizados

El proyecto parte de **tres datasets crudos**, que se limpian y combinan en un cuarto dataset de trabajo:

| Dataset | Descripción | Filas | Columnas clave |
|---|---|---|---|
| `plans.csv` | Catálogo de planes disponibles (precio, mensajes/minutos/GB incluidos, costos por excedente) | 2 | `plan_name`, `usd_monthly_pay`, `messages_included`, `minutes_included` |
| `users_latam.csv` (`users`) | Datos demográficos y de suscripción de cada usuario | 4,000 | `user_id`, `age`, `city`, `reg_date`, `plan`, `churn_date` |
| `usage.csv` (`usage`) | Registro individual de cada mensaje/llamada realizado | 40,000 | `id`, `user_id`, `type`, `date`, `duration`, `length` |

A partir de `users` y `usage` limpios, se construye:

| Dataset generado | Descripción |
|---|---|
| `user_profile.csv` | Un registro por usuario (4,000 filas), combinando los datos demográficos de `users` con el uso agregado de `usage` (`cant_mensajes`, `cant_llamadas`, `cant_minutos_llamada`), más las columnas de segmentación `grupo_uso` y `grupo_edad` |

> `plans.csv` no se une directamente a `user_profile.csv`; se usa como referencia para contrastar el uso real de cada cliente contra lo que su plan contratado incluye (ver Paso 5 del notebook).

## 🧩 Etapas del análisis

El notebook (`S7_Version-Estudiante-Project-ConnectaTel.ipynb`) sigue estos pasos:

1. **Carga y exploración**: lectura de los tres datasets crudos, revisión de forma (`shape`), tipos de datos (`.info()`) y primeras filas.
2. **Identificación de problemas de calidad de datos**:
   - Revisión de valores nulos por columna y dataset.
   - Detección de valores inválidos/sentinels: `-999` en `age`, `"?"` en `city`, fechas futuras (2026) en `reg_date`, códigos de error (`120.0`, `1490.0`) en `duration`/`length`.
   - Revisión y estandarización de formatos de fecha.
3. **Limpieza básica de datos**: tratamiento de sentinels y fechas imposibles; clasificación de nulos en `duration`/`length` como *Missing At Random* (MAR) dependientes de la columna `type`.
4. **Agregación de uso por usuario**: cálculo de `cant_mensajes`, `cant_llamadas` y `cant_minutos_llamada` por `user_id`, y unión con los datos demográficos para construir `user_profile.csv`.
5. **Visualización de distribuciones y detección de outliers**: histogramas de `age`, `cant_mensajes`, `cant_llamadas` y `cant_minutos_llamada` por plan; boxplots y cálculo de outliers con el método de rango intercuartílico (IQR).
6. **Segmentación de clientes**: construcción de `grupo_uso` (Bajo/Medio/Alto uso) y `grupo_edad` (Joven/Adulto/Adulto Mayor), y visualización de su distribución.
7. **Insight ejecutivo para stakeholders**: análisis ejecutivo que traduce los hallazgos técnicos en conclusiones y recomendaciones de negocio (calidad de datos, aprovechamiento de planes vs. uso real, segmentos de valor, outliers y recomendaciones).
8. **Entrega**: carga del notebook y este README a GitHub.

## ▶️ Cómo ejecutar el notebook

### Opción A: Google Colab (recomendado)

1. Sube el archivo `S7_Version-Estudiante-Project-ConnectaTel.ipynb` a [Google Colab](https://colab.research.google.com/) (`Archivo → Subir cuaderno`), o ábrelo directamente desde GitHub una vez esté en el repositorio (`Archivo → Abrir cuaderno → GitHub`).
2. Sube los tres archivos de datos (`plans.csv`, `users_latam.csv`, `usage.csv`) a la ruta `/datasets/` dentro del entorno de Colab, o ajusta las rutas de carga en la celda de "cargar archivos" según donde los hayas colocado (por ejemplo, montando Google Drive).
3. Ejecuta las celdas en orden, de principio a fin (`Entorno de ejecución → Ejecutar todas`).

### Opción B: Jupyter local

1. Clona este repositorio.
2. Instala las dependencias (ver sección de Requisitos).
3. Coloca `plans.csv`, `users_latam.csv` y `usage.csv` en una carpeta `datasets/` en la raíz del proyecto, o ajusta las rutas en la celda de carga de archivos.
4. Abre el notebook con `jupyter notebook S7_Version-Estudiante-Project-ConnectaTel.ipynb` y ejecuta todas las celdas en orden.

### Requisitos

```
python >= 3.9
pandas
numpy
matplotlib
seaborn
scipy
```

Instalación rápida:

```bash
pip install pandas numpy matplotlib seaborn scipy
```

## 🔁 Guía de reproducción

Para reproducir el análisis completo desde cero:

1. **Ejecuta el notebook de principio a fin, en orden secuencial** — varias celdas dependen del estado generado por celdas anteriores (por ejemplo, `users_clean` y `usage_clean` se construyen progresivamente a lo largo del notebook).
2. **Los comentarios de diagnóstico (celdas marcadas con ✍️)** documentan las decisiones tomadas en cada etapa de limpieza (qué hacer con nulos, sentinels y fechas inválidas) — revísalos si necesitas justificar o modificar alguna decisión de tratamiento de datos.
5. **El análisis ejecutivo final (Paso 7)** resume los hallazgos y recomendaciones; si se corrige o actualiza algún dato upstream (por ejemplo, una nueva versión de `users_latam.csv`), este análisis debe volver a generarse para reflejar los nuevos resultados.

## 📁 Estructura del repositorio

```
├── S7_Version-Estudiante-Project-ConnectaTel.ipynb   # Notebook principal del análisis
├── README.md                                          # Este archivo
└── datasets/                                          # (no incluido) plans.csv, users_latam.csv, usage.csv
```

## 📌 Notas

- El dataset `usage.csv` cubre únicamente registros del año 2024; `users_latam.csv` incluye usuarios registrados desde 2022.
- La columna `city` conserva valores faltantes sin imputar en la versión actual del notebook; se recomienda aplicar una categoría explícita ("Desconocido") antes de cualquier análisis geográfico.
