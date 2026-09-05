# Spec — sentimiento-huila v3

> Creado 2026-09-05. Repo: github.com/MaximumTech17/sentimiento-huila

## Qué se ha hecho hasta ahora

**v1 (jul 2026):** 43 noticias de Diario La Nación y Diario del Huila, clasificadas con Gemini (positivo/negativo/neutral). Índice de sentimiento = `(% positivo - % negativo)`, resultado −2.3. Baseline clásico añadido después (TF-IDF + Naive Bayes, 5-fold CV): 44% accuracy, la clase "neutral" nunca se predijo (solo 8 ejemplos).

**v2 (jul-sep 2026):** ampliado a 93 noticias (mismos 2 medios). Índice general −3.2. TF-IDF mejoró a 56% accuracy, pero "neutral" (14 ejemplos) sigue sin predecirse nunca. Archivos v1 conservados como `*_v1.*` para no perder el punto de partida.

**Problema detectado en v2 (motivo de la v3):** la gráfica y el índice de la v2 mezclan julio y septiembre en una sola serie continua y una sola cifra — un promedio que no representa bien a ningún mes en particular ("sancocho").

## Qué cambia en v3

1. **El índice de sentimiento se calcula y grafica por mes seleccionado**, no sobre todo el histórico junto. Se agrega una variable de configuración simple al inicio de `sentiment_index.ipynb` (ej. `mes = "2026-07"`) que filtra el dataset antes de calcular el índice y generar la gráfica — sin selector interactivo (Streamlit), para mantener el estilo minimalista del resto del repo (decisión tomada 2026-09-05).
2. **El dataset acumulado (histórico completo, todos los meses) sigue siendo la base de entrenamiento del clasificador clásico** (`classic_nlp_tfidf.ipynb`) — más datos para el TF-IDF + Naive Bayes es bueno para esa parte, el problema de "sancocho" era específico del índice/gráfica, no del entrenamiento.
3. El README debe reflejar claramente esta separación: "índice mensual" vs. "entrenamiento con histórico completo" — para que no se preste a la misma confusión de la v2.

## Decisiones ya tomadas
- **Dataset base (`data.xlsx` / `data_labeled.csv`) sigue siendo uno solo, acumulado con todo el histórico** — no se separa por mes. Es la misma fuente que alimenta el entrenamiento del clasificador clásico (punto 2 arriba).
- **Los archivos de salida (gráfica del índice) sí se generan uno por mes**, derivados de filtrar ese dataset acumulado según la variable `mes` — no se sobreescribe un único archivo genérico. Nombre sugerido: `sentiment_index_<mes>.png` (ej. `sentiment_index_2026-07.png`), a confirmar por quien implemente.

## Decisiones resueltas
- Formato de variable de configuración: `mes = "YYYY-MM"` (ej. `"2026-08"`).
- Formato de archivos de salida: `sentiment_index_<month>_<year>.png` y `sentiment_index_weekly_<month>_<year>.png` (ej. `sentiment_index_08_2026.png`).
- Acumulación de etiquetas: `data_labeled.csv` acumula todo el histórico etiquetado; al procesar un mes se reusan etiquetas cacheadas y solo se clasifica lo nuevo.
- Manejo de períodos con pocas noticias: corre con los datos disponibles en el mes seleccionado (postergado tratamiento especial).

## Historial de decisiones
- 2026-09-05: se descarta un selector interactivo tipo Streamlit para el análisis — se prefiere una variable simple en el notebook, consistente con el estilo ya establecido en v1/v2.
- 2026-09-05: el dataset base se mantiene único/acumulado (histórico completo); solo las salidas del índice se separan por mes.
- 2026-09-05: se define el formato `MM_YYYY` para los archivos PNG de salida mensual y se actualizan ambos notebooks y el README.
