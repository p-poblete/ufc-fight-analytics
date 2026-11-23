# Contexto de sesión — ufc-fight-analytics

Actualizado: 2026-11-23

## Estado
- Ya no es proyecto de curso: es proyecto personal de portafolio. Movido a `~/Proyectos/Personales/ufc-fight-analytics`; repo de GitHub renombrado a `p-poblete/ufc-fight-analytics`.
- Código actual: `src/app.py` (comparador + gráficos globales, ~1.370 líneas), `src/d_pca.py` (PCA, t-SNE, UMAP, K-Means, ~570 líneas), `src/ufc_eda.ipynb`. Ambos duplican `cargar_y_procesar_todo()`. Datos: Kaggle `mdabbert/ultimate-ufc-dataset` vía `kagglehub`.
- Cambios pendientes commiteados y publicados el 2026-11-23 (.gitignore, docs, d_pca, app, notebook sin salidas). Merge de origin/main: se conserva el comparador en `src/app.py`, `.devcontainer/` (de Streamlit Cloud) y la versión web del PCA en `src/d_pca_web.py`.
- Hay una app antigua en Streamlit Cloud de este repo: borrarla cuando el comparador nuevo esté publicado.
- Evaluación (6/10) y decisión de dividir: `docs/evaluacion-2026-11-16.md`.

## Decisión (2026-11-23)
Dividir en dos proyectos:
1. **Comparador** (`ufc-fighter-compare`, repo nuevo, va primero): dos peleadores, radar, récord, finalizaciones/derrotas, historial, ROI por peleador. Desplegado en Streamlit Cloud con README, captura y demo.
2. **Análisis** (este repo): un caso de estudio con 3-4 hallazgos (calibración de cuotas sin margen, estilos con pruebas estadísticas, clústeres validados, modelo sin fuga temporal opcional).
- El análisis genera el dataset limpio versionado; el comparador solo lo consume.

## Actualización (2026-11-23, tarde)
- El comparador se hace en `~/Proyectos/Personales/ufc-fighter-compare` (id `ufc-compare`) con Astro + TS, no Streamlit. Su pipeline genera el dataset limpio (UFC-Almanac + Kaggle jerzyszocik/mdabbert); este repo lo consumirá. Ver `docs/spec.md` de ese repo.

## Siguiente paso
Comparador primero (ver su `docs/session-context.md`). Este repo queda en pausa hasta entonces.

Nota del vault: `Proyectos/UFC/UFC.md`.
