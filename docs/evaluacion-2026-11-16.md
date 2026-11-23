# Evaluación de ufc-fight-analytics — 2026-11-16

Nota global: **6/10**. Tiene un planteamiento de BI sólido: un charter con problema, objetivos y alcance; un documento de diseño con hipótesis y diccionario de datos; y un dashboard rico (radar, ROI por tipo de cuota, distribución de tiempos, correlaciones, PCA, t-SNE, UMAP y K-Means). Pero no se puede presentar como portafolio: el README está vacío, el código está en dos archivos monolíticos, el notebook tiene salidas pesadas y la parte de cuotas y ROI no tiene el rigor estadístico que exigiría un analista profesional.

Datos al evaluar:
- `src/app.py`: 1.370 líneas.
- `src/d_pca.py`: 567 líneas.
- Notebook de EDA.
- 10 commits.
- Cambios sin commitear en los tres archivos, y carpetas `.ipynb_checkpoints/` sin ignorar.
- Datos: Kaggle `mdabbert/ultimate-ufc-dataset`, descargados en tiempo de ejecución con `kagglehub`.

## Notas por área

| Área | Nota | Comentario |
|---|---|---|
| Planteamiento | 8 | El charter y el diseño de análisis con hipótesis están por encima de lo habitual en un proyecto de curso. |
| Visualización y dashboard | 7 | Muchas vistas y comparación entre dos peleadores con diálogos. El riesgo es que sean demasiados gráficos sin una narrativa que responda a las hipótesis. |
| Rigor analítico | 4.5 | Los arquetipos usan umbrales fijos (`avg_SIG_STR_landed > 4`, `avg_TD_landed > 2`) sin justificar. El ROI no considera el margen de la casa, el intervalo de confianza ni el tamaño de muestra. No hay pruebas estadísticas para las hipótesis. El clustering no se valida (silueta, estabilidad). |
| Calidad del código | 4 | Dos archivos monolíticos que duplican `cargar_y_procesar_todo()`. Mezclan carga, transformación y visualización. No hay tests. |
| Reproducibilidad | 4.5 | `requirements.txt` sin versiones fijadas y sin `umap-learn` (UMAP solo funciona si está instalado aparte). Depende de la descarga de Kaggle en cada arranque y no hay datos de respaldo. |
| Presentación y portafolio | 2 | El README solo tiene el título y el nombre del repositorio tiene una errata. No hay capturas, demo desplegada, conclusiones ni hallazgos. |
| Higiene del repositorio | 4 | `.ipynb_checkpoints/` sin `.gitignore`, notebook con salidas de unas 10.000 líneas y commits con mensajes genéricos («Actualiza»). |

## Comparación con trabajos profesionales de analítica deportiva

| Práctica | Proyectos destacados de Kaggle / UFCStats / FiveThirtyEight-style | **ufc-fight-analytics** |
|---|---|---|
| README con pregunta, datos, método, hallazgos y limitaciones | Sí | No |
| Hallazgos explícitos que responden a cada hipótesis | Sí | No: el dashboard muestra, pero no concluye |
| Pruebas estadísticas e intervalos (chi², test de proporciones, bootstrap) | Sí | No |
| Cuotas convertidas a probabilidad implícita sin margen y calibración | Sí | No: ROI bruto |
| Validación temporal (no usar información posterior a la pelea) | Sí | Sin verificar |
| Segmentación justificada (cuantiles, clustering validado) | Sí | Umbrales fijos |
| Pipeline separado (ETL → features → app) y datos versionados | Sí | Monolítico |
| Demo desplegada (Streamlit Community Cloud) | Habitual | No |

## Mejoras por prioridad

### Presentación

1. Escribir el README: pregunta, dataset (enlace y licencia), método, 3-5 hallazgos con cifras y gráfico, limitaciones y cómo ejecutarlo.
2. Desplegarlo en Streamlit Community Cloud (gratis) y enlazarlo desde el README y desde el perfil de GitHub.
3. Renombrar el repositorio a `ufc-fight-analytics` (GitHub redirige el nombre antiguo).
4. Añadir una pestaña o sección «Conclusiones» que responda a cada hipótesis del diseño.

### Rigor

5. Convertir las cuotas americanas a probabilidad implícita y quitar el margen de la casa. Comparar la tasa de victorias real con la implícita (calibración) en lugar de mostrar solo el ROI.
6. Acompañar el ROI y los porcentajes de victoria con el tamaño de muestra e intervalos de confianza (bootstrap), y pruebas de proporciones o chi² para Striker frente a Grappler.
7. Justificar los arquetipos: umbrales por cuantiles dentro de cada división, o usar los clústeres de K-Means validados con silueta e interpretados.
8. Comprobar que las métricas `avg_*` del dataset son previas a la pelea para no introducir información del futuro, y documentarlo.

### Código y reproducibilidad

9. Separar en `src/datos.py` (carga y limpieza), `src/metricas.py` (KPIs y arquetipos) y `src/graficos.py`, más una app multipágina de Streamlit (`pages/`) que fusione `app.py` y `d_pca.py`.
10. Fijar versiones en `requirements.txt` y añadir `umap-learn`.
11. Guardar en caché local el CSV (o una muestra pequeña versionada) para no depender de Kaggle en cada arranque.
12. Tests unitarios de `calcular_retorno`, `get_tipo_apuesta` y del cálculo de récords.
13. `.gitignore` con `.ipynb_checkpoints/`, y limpiar las salidas del notebook con `nbstripout`.
14. Commitear los cambios pendientes con mensajes descriptivos.

## Idea: separar en dos proyectos (anotada 2026-11-23)

Propuesta de Piero: dividir el proyecto en (A) un dashboard solo para comparar peleadores y (B) un proyecto de ciencia de datos.

**Veredicto: buena idea (8/10).** Actualización 2026-11-23: el objetivo es el portafolio, no el curso; se hace ya. Sustituye a la mejora 9 (fusionar `app.py` y `d_pca.py` en una app multipágina).

A favor:
- El código ya está partido así: `app.py` es casi todo comparación (radar, donas, ROI, récords, historial) y `d_pca.py` es análisis (PCA, t-SNE, UMAP, K-Means).
- Cada proyecto tiene un público y una pregunta claros. Con uno solo, el dashboard «muestra mucho pero no concluye», que es la crítica principal de la evaluación.
- Para el portafolio son dos piezas distintas: un producto desplegable (Streamlit Cloud) y un análisis con hallazgos. Encaja con el vídeo 03 de Marca personal (enseñar el comparador).

Riesgos:
- La carga y limpieza de datos ya está duplicada; separar sin resolverlo la duplica en dos repos. Que B genere un dataset limpio y versionado (parquet) y que A solo lo consuma, o compartir un `datos.py` único.
- Doble mantenimiento: dos README, dos `requirements.txt` y dos despliegues.
- B solo vale si gana rigor: pruebas de hipótesis, cuotas a probabilidad implícita sin margen y calibración, intervalos, clustering validado (silueta) y control de fuga temporal. Si no, queda como un notebook flojo separado de lo vistoso.

Reparto propuesto:
- **A — comparador** (`ufc-fighter-compare`): selección de dos peleadores, radar, récord, finalizaciones, historial y ROI por peleador. Sin PCA ni gráficos globales por división.
- **B — análisis** (este repo, renombrado a `ufc-fight-analytics`): hipótesis del charter, gráficos por división, correlaciones con cuotas, calibración, arquetipos/clústeres y conclusiones. Notebooks + informe; opcionalmente un modelo predictivo con validación temporal.

Orden recomendado para portafolio: 1) comparador A pulido y desplegado (demo + vídeo); 2) B como un único caso de estudio con 3-4 hallazgos sólidos, no un catálogo de gráficos.

Siguiente paso: definir el contrato del dataset limpio (columnas y origen) y sacar A a un repo nuevo con `datos.py`.
