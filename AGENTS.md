# AGENTS.md — Contexto para agentes IA

Este archivo proporciona contexto estructurado para que agentes de IA (como Hermes, Claude, ChatGPT, etc.) entiendan, colaboren y ejecuten tareas en este proyecto de manera autónoma y precisa.

## Resumen del proyecto

**Título:** Detección de acumulación en esquemas de pump & dump usando IPR de la Laplaciana con filtrado RMT

**Formato:** Paper IEEE (`\documentclass[journal]{IEEEtran}`) en español.

**Universidad:** Universidad del Rosario.

**Área:** Econofísica / Market Microstructure / Graph Theory / Random Matrix Theory.

## Stack tecnológico

| Recurso | Detalle |
|---------|---------|
| **Lenguaje** | LaTeX (IEEEtran) |
| **Compilador** | pdflatex con bibtex |
| **Fuente de datos** | ~351 eventos P&D (La Morgia et al. 2020), Binance 2021–2022 |
| **Análisis** | Python (scripts en `codigo/`) |
| **Control de versiones** | Git + GitHub (`origin/main`) |

## Estructura del repositorio

```
├── main.tex                 % Punto de entrada — documentclass, paquetes, \input de secciones
├── sec/
│   ├── abstract.tex         % Resumen en español
│   ├── intro.tex            % Introducción y motivación
│   ├── problema.tex         % Planteamiento del problema de investigación
│   ├── marco_teorico.tex    % Marco teórico (grafos, RMT, IPR, Laplaciana)
│   ├── modelamiento.tex     % Modelamiento matemático
│   ├── pipeline.tex         % Pipeline metodológico completo
│   ├── solucion.tex         % Solución propuesta detallada
│   ├── objetivos.tex        % Objetivos general y específicos
│   └── referencias.tex      % Bibliografía
├── codigo/                  % Implementación y experimentos
├── recursos/                % PDFs de referencia bibliográfica
├── CONTEXTO.md              % Contexto del proyecto, decisiones técnicas y pendientes
├── AGENTS.md                % Este archivo
└── README.md                % Descripción general
```

## Convenciones de escritura

- **Idioma:** Español (castellano).
- **Formato:** IEEE estricto — usar `\cite{}`, `\label{eq:...}`, `\eqref{}`.
- **Secciones:** Cada sección en su propio archivo bajo `sec/`. Se incluyen vía `\input{}` en `main.tex`.
- **Sin `\section{Metodología}` explícito:** El flujo es: Problema → Marco Teórico → Solución Propuesta.
- **Ecuaciones:** Numeradas con `\label{eq:...}`; todas las referencias cruzadas usan `\eqref{}`.

## Decisiones técnicas clave

| Decisión | Valor |
|----------|-------|
| Estadístico de detección | IPR<sub>p90</sub> (percentil 90) |
| Filtrado de ruido | RMT con Marchenko-Pastur (ventana dual: calibración 720 h / detección 48 h) |
| Esparsificación | MST + aristas adicionales calibradas por modularidad (Leiden) |
| Modelo nulo | Configuration Model (10 000 redes), umbral percentil 99.7 empírico |
| Laplaciana | Normalizada (eigenvalores en [0,2]) |
| Correlación | Pearson (compatibilidad con RMT) |
| Métrica principal | PR-AUC (clases desbalanceadas) |
| Métrica secundaria | ROC-AUC |

## Pendientes activos

- [ ] Sección **Resultados / Experimentos** — no existe todavía
- [ ] Sección **Conclusiones** — no existe todavía
- [ ] Ecuación `eq:iprmean` referenciada pero nunca definida formalmente
- [ ] PDFs faltantes en `recursos/`
- [ ] Ablations pendientes: pipeline completo vs. sin filtrado RMT
- [ ] Series temporales para robustness (colapso FTX, umbral empírico)

## Flujo de trabajo para agentes

1. **Leer `CONTEXTO.md`** primero para entender el estado actual, decisiones técnicas y pendientes.
2. **Leer `main.tex`** y la sección relevante antes de editar.
3. **Compilar** con pdflatex + bibtex después de cada cambio significativo para verificar que no se rompen referencias cruzadas.
4. **Commits** descriptivos en español, siguiendo el estilo convencional del proyecto.
5. **No modificar** archivos en `codigo/` sin coordinación con el autor — los scripts experimentales son independientes del paper.

## Reglas para edición de LaTeX

- Usar `\input{sec/nombre}` en `main.tex` para incluir secciones.
- Las ecuaciones deben tener label único: `\label{eq:descripcion}`.
- Referencias bibliográficas con BibTeX (`\bibliography{referencias}`).
- No usar `\section{Metodología}` — el flujo aprobado es Problema → Marco Teórico → Solución Propuesta.
- Mantener consistencia en nomenclatura matemática (ej: $L$ para Laplaciana, $\lambda$ para eigenvalores, $\phi_k$ para eigenvectors).

## Contacto / autoría

Proyecto de David (daisel10). Repositorio: https://github.com/daisel10/grafos-mercado-paper.git
