---
area: trading
estado: activo
siguiente: "ablation del filtro RMT: pipeline completo vs. sin Marchenko-Pastur"
repo: https://github.com/daisel10/grafos-mercado-paper.git
---

## Objetivo

Detectar la fase de acumulación en esquemas de *pump & dump* en criptoactivos usando el perfil del IPR de la Laplaciana normalizada de una red de correlaciones filtrada con RMT. Paper IEEE, Universidad del Rosario.

## Reglas de escritura/estilo

- Formato IEEE estricto (`\documentclass[journal]{IEEEtran}`)
- Idioma: español
- [ ] Secciones en `sec/`; la solución propuesta vive en `solucion_compañero.tex` (raíz)
- Sin `\section{Metodología}` explícito — el flujo es: Problema → Marco Teórico → Solución Propuesta
- Ecuaciones numeradas con `\label{eq:...}`; referencias cruzadas con `\eqref{}`

## Decisiones técnicas tomadas

- **Estadístico de detección:** `IPR_p90` (percentil 90 de la distribución de IPR), inmune al *eigenvalue crossing*
- **Filtrado:** RMT con ventana dual: calibración 720 h / detección 48 h
- **Esparsificación:** MST + k aristas adicionales calibradas por modularidad (algoritmo Leiden)
- **Modelo nulo:** Configuration Model (10 000 redes), umbral en percentil 99.7 empírico (no paramétrico)
- **Dataset:** ~351 eventos de La Morgia et al. 2020, pares SYM/BTC en Binance 2021–2022
- **Métrica principal:** PR-AUC (clases desbalanceadas); ROC-AUC como referencia
- **Laplaciana:** normalizada (no estándar ni random walk) — eigenvalores acotados en [0,2]
- Correlación de Pearson como base (no Spearman) por compatibilidad con RMT/Marchenko-Pastur

## Pendientes activos

- Sección de **Resultados / Experimentos** — no existe todavía
- Sección de **Conclusiones** — no existe todavía
- Ecuación `eq:iprmean` faltante: referenciada en `objetivos.tex` y `solucion_compañero.tex` pero nunca definida formalmente con `\label` y número de ecuación
- PDFs faltantes en `recursos/`: `traag2019` (Leiden), `maslov2002` (rewiring local)

## Revisión adversarial (pre-submission)

Críticas estructurales que justificarían Major Revision o Reject. Cada una requiere evidencia mínima para el rebuttal.

### Claims no sustentados

**C1 — Premisa conductual fundamental**
*"La acumulación coordinada previa a un pump induce correlaciones elevadas dentro de S..."* — se enuncia como hecho, no como hipótesis. No hay evidencia de que los manipuladores acumulen *múltiples* tokens simultáneamente.
→ Rebuttal: caso de estudio (gráfico de red pre-pump) de un evento confirmado del dataset.

**C2 — Distancia de Mantegna y correlaciones negativas**
El paper dice que la métrica "preserva correlaciones negativas". Pero $d_{ij}\approx 2$ cuando $C_{ij}\approx -1$, y el MST prioriza aristas de menor costo → las correlaciones negativas se descartan en la práctica.
→ Rebuttal: aceptar que el MST descarta correlaciones negativas y justificar por qué solo los co-movimientos positivos importan para detectar acumulación, o reportar qué porcentaje de aristas negativas sobrevive a la esparsificación.

**C3 — Robustez del Configuration Model ante choques sistémicos**
*"...el umbral absorbe cambios en el nivel de correlación agregado..."* — en pánico total ($C_{ij}\to 1$) la red post-MST casi deja de tener estructura, y no hay garantía de que el umbral se mantenga robusto.
→ Rebuttal: serie temporal del umbral empírico atravesando el colapso de FTX (nov 2022).

**C4 — MST y "correlaciones más fuertes"**
Afirmación topológicamente imprecisa: el MST garantiza conectividad, no preservar estrictamente las aristas de mayor peso.
→ Rebuttal: distribución empírica de pesos retenidos por el MST vs. k-NN o umbral duro.

**C5 — Ventana dual como solución a la restricción $T\gg N$**
No es una solución, es una heurística que asume estacionariedad. En cripto el régimen de volatilidad cambia abruptamente.
→ Rebuttal: error de tipo I del detector cuando la volatilidad de $T_{\text{det}}$ difiere en >1σ respecto a $T_{\text{cal}}$.

### Ablations/baselines faltantes

1. **Baseline de detección de comunidades pura**: aplicar Leiden directamente sobre matrices rodantes y declarar anomalía si Q o tamaño de comunidad salta. Mostrar que PR-AUC del IPR lo supera.
2. **Ablation del filtro RMT**: comparar pipeline completo vs. sin filtrado Marchenko-Pastur. Si el resultado es el mismo, RMT es sobreingeniería.
3. **Baseline CAPM-style**: en vez de eliminar $\lambda_1$, calcular correlaciones sobre residuales ajustados por Bitcoin. Comparar PR-AUC de ambos.

### Explicaciones alternativas

1. **Confounder de noticias/eventos fundamentales**: listados en exchanges, airdrops sectoriales, noticias regulatorias generan acumulación legítima con la misma firma espectral que P&D. Requiere filtro de noticias o análisis de supervivencia temporal para distinguir la caída brusca posterior.
2. **Data leakage en calibración**: si $T_{\text{cal}}$ incluye el evento, $\lambda_+$ queda contaminado. Debe aclararse explícitamente que $T_{\text{cal}}$ termina en $t-48\text{h}$ (separación causal estricta).

### Contribución incremental

Plerou+Nobi ya usaron IPR sobre matrices de correlación filtradas con RMT. Mantegna ya introdujo el MST. El paper es una *aplicación* del pipeline clásico de econofísica a P&D.
→ Rebuttal obligatorio: demostrar que la solución al *eigenvalue crossing* mediante $\mathrm{IPR}_{p90}$ distribucional es una innovación metodológica necesaria que habilita por primera vez el IPR como clasificador en *streaming*.

### Claims estadísticos sin uncertainty quantification

*"perturbar $\lambda_+$ en $\pm 15\%$ produce cambios en precisión menores al 5%"* — estimación puntual, sin intervalos de confianza. "Precisión" es engañosa con clases desbalanceadas (un modelo que nunca detecta nada tiene 99% de accuracy).
→ Rebuttal: CI 95% del PR-AUC bajo perturbación $\pm 15\%$; varianza a través de múltiples semillas en el Configuration Model. Reemplazar "precisión" por Precision-Recall en todo el texto.

---

## Ideas y trabajo futuro

- Análisis de robustez: comparar p80/p90/p95/p99; Pearson vs Spearman; Configuration Model puro vs rewiring local
- Verificar interpretabilidad: que los tokens con mayor peso en φ_{k*} coincidan con los manipulados del dataset
- Baseline univariado: detector de umbral sobre retornos (μ_hist + 3σ_hist)
